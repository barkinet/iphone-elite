# Overview #

The **teasz** program below implements the same encryption scheme as used by the iPhone firmware when accessing the seczone.  Specifically, it's [TEA encryption](http://en.wikipedia.org/wiki/Tiny_Encryption_Algorithm) used in [CBC mode](http://en.wikipedia.org/wiki/Cipher_block_chaining).  It is used in the [re-virginizer](http://code.google.com/p/iphone-elite/wiki/RevirginizingTool) to encrypt a non-corrupt "virgin" lock table into your seczone.

## Note about re-virginization ##

When the code below is incorporated into a program running on the baseband CPU, it has the ability to properly encrypt every part of the seczone.  But such a program still needs to know **what** to encrypt.  At this point, the differences between the anySIM 1.0x unlock and the IPSF unlock become important.

  * The anySIM 1.0x unlock inadvertantly convinces the Apple baseband code to rewrite the lock tables at seczone+0xc88, away from their virgin locked status.  That's okay as long as the anySIM 1.0x patch to the baseband is still in place, because that inconsistency is ignored (by the patch).  The anySIM 1.0x unlock does not change the "token" at seczone+0x400.

  * The IPSF unlock intentionally rewrites the lock tables at seczone+0xc88, away from their virgin locked status.  They also **zero out** your 'token" at seczone+0x400.  The zeroed-out token combined with the lock table changes convince your iPhone it's unlocked because of an RSA bug that Apple has in their code (RSA is used on the token).

Now, consider this:

  * The decrypted lock table for a locked phone is **identical in every iPhone**.  Encrypting the lock table makes it look different on every iPhone, but once decrypted it is identical.

  * The "token" is **unique to every iPhone**.  It uses RSA encryption and only Apple knows the private key used to encrypt.  In other words, even after doing the initial TEA CBC decryption implemented below, your decrypted token is still a very unique RSA value specific to your iPhone..  If it gets wiped out, it is gone forever (unless you, or IPSF, saved a backup).

  * The code on this page can be used to restore the virgin lock table to any iPhone, regardless of unlock method.  That's because the data to encrypt is known (in fact, it's shown below).

  * The code on this page **cannot** be used to restore the token on your iPhone **unless you have a backup of the encrypted seczone**.  That sucks for IPSF users who didn't make such a backup, if Apple ever closes the RSA hole.

  * **IN SUMMARY**: If you used anySIM 1.0x and became 0049'd, the source code on this page will help revirginize your phone.  If you used IPSF, the source code here will help you only if Apple closes the RSA hole (otherwise, why bother to re-virginize) and if you (or IPSF) has a backup of your original seczone.

# Getting your norID and HWID #

In order to use this program, you need to know the norID (8 bytes) and HWID (12 bytes) of your iPhone.  There are a few ways to get those.

## LTOKEN extraction ##

You can capture the LTOKEN sent to IPSF when you unlock your iPhone (this was the method that I used).  The LTOKEN itself is encrypted, and you need a program like  [depisf](http://lpahome.com/geohot/deipsf.c) from geohot to decrypt it.  Once you do decrypt it, the HWID is 0x18 bytes into the decrypted part and lasts for 12 bytes.  The norID is 0x302 bytes into the decrypted part and lasts for 8 bytes.

  * For anyone curious, you can tell the only 12 bytes of the HWID section that matter begin at 0x18 bytes into the HWID section because the RCE of their bbsimfree v1.6 shows they are interested in those bytes.  See the code at 0xA0024818.  That was a big tip-off.

## Custom baseband program ##

### HWID ###
A baseband program can access the HWID directly by reading from 0xF440006C through 0xF4400074

  * For the curious, bbsimfree v1.6 can be seen doing this at 0xA00245CC.

### norID ###
A baseband program can read the norID directly by issuing a CFI query command and reading back the data at 0x102 through 0x109.  Such a program would need to be executing out of RAM, because if it is executing out of NOR then its address space will drop out from under it after the command, and it will be "executing" CFI response bytes, which is garbage.  The IPSF hlloader executes out of RAM, and uses this very method.  The eliteloader.bin executes out of NOR, and capitalizes on the next bit of information...

## Poor-man's NORid extraction ##

If you have the BSD subsystem and norz on your iPhone, then do this:
```
norz /tmp/f  ffff3000 1000 >/dev/null 2>&1 && dd if=/tmp/f bs=1 skip=3042 count=8 2>/dev/null  | hexdump -C
```

That's your NORid.

# Using your norID and HWID #

The norID and HWID aren't used directly.  They are assembled into a 32-byte structure that looks like: norID, 8 bytes of 0, HWID, 4 bytes of zero.  Then, the SHA-1 sum is computed over those 32-bytes.  The first 16 bytes of that SHA-1 sum is what you need, and what the program below expects you to give it.  It is unique for every iPhone.

# Source code #

```
/*
 * teasz - TEA seczone utility
 *
 * Written my MuscleNerd, with help from wikipedia
 *
 * Thanks to everyone at iphone-elite.org wiki and IRC channel
 * for providing a more friendly environment in which to participate.
 *
 * The SHA-1 hash value you need to supply is the first 16 bytes of the 
 * 20-byte SHA-1 hash of these 32 bytes:  norID, 8 zeros, HWID, 4 zeros
 *
 * Core TEA algorithm at http://en.wikipedia.org/wiki/Tiny_Encryption_Algorithm
 * TEA key is 16 bytes/128 bits, TEA block is 8 bytes/64 bits
 * iPhone uses TEA in CBC mode to encrypt/decrypt seczone areas
 */

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

char *whoami;
void usage(void) {
  fprintf(stderr, "Usage: %s <mode> <infile> <offset> <length> <sha>\n\n"\
          "   mode   - e to encrypt, d to decrypt\n"\
          "   infile - raw binary file input\n"\
          "   offset - offset into file\n"\
          "   length - how many bytes to process\n"\
          "   sha    - first 16 bytes of sha1 sum\n\n"\
          "   NOTE: The last three arguments must all be hex with 0x prefix\n"\
          ,whoami);
  exit(1);
}

void encrypt(unsigned long* v, unsigned long* k) {
  unsigned long v0, v1, sum, i, delta, k0, k1, k2, k3;
  delta=0X9E3779B9;                       /* a key schedule constant */
  k0=k[0]; k1=k[1]; k2=k[2]; k3=k[3];     /* cache key */
  v0 = v[0]; v1 = v[1]; sum=0;            /* set up */

  for (i=0; i < 32; i++) {                /* basic cycle start */
    sum += delta;
    v0 += ((v1<<4) + k0) ^ (v1 + sum) ^ ((v1>>5) + k1);
    v1 += ((v0<<4) + k2) ^ (v0 + sum) ^ ((v0>>5) + k3); /* end cycle */
  }
  v[0]=v0; v[1]=v1;
}

/* See http://upload.wikimedia.org/wikipedia/en/d/d3/Cbc_encryption.png
   for a depiction of general CBC encryption */
void encrypt_cbc(unsigned long *in, unsigned long len, unsigned long *key, unsigned long *out) {
  unsigned long x[2], tea[2], offset;

  /* initialization vector */
  x[0] = x[1] = 0xffffffff;

  for (offset=0; offset<len; offset+=8) {  /* block size of TEA is 8 bytes */
    int blen = 8;
    if ((len-offset)<8)
      blen = len-offset;

    int i;
    for (i=0; i<(blen>>2); i++)
      tea[i] = in[(offset>>2)+i];

    tea[0] ^= x[0];    tea[1] ^= x[1];
    encrypt(tea, key);
    x[0]    = tea[0];    x[1]  = tea[1];
    
    /* Ciphertext */
    for (i=0; i<blen; i++)
      ((unsigned char *)out)[offset+i] = ((unsigned char *)tea)[i];
  }
}

void decrypt(unsigned long* v, unsigned long* k) {
  unsigned long v0, v1, sum, i, delta, k0, k1, k2, k3;
  delta=0X9E3779B9;                       /* a key schedule constant */
  k0=k[0]; k1=k[1]; k2=k[2]; k3=k[3];     /* cache key */
  v0 = v[0]; v1 = v[1]; sum=0xC6EF3720;   /* set up */

  for (i=0; i<32; i++) {                               /* basic cycle start */
    v1 -= ((v0<<4) + k2) ^ (v0 + sum) ^ ((v0>>5) + k3);
    v0 -= ((v1<<4) + k0) ^ (v1 + sum) ^ ((v1>>5) + k1);
    sum -= delta;                                      /* end cycle */
  }
  v[0]=v0; v[1]=v1;
}

/* See http://upload.wikimedia.org/wikipedia/en/6/66/Cbc_decryption.png 
   for a depiction of general CBC decryption */
void decrypt_cbc(unsigned long *in, unsigned long len, unsigned long *key, unsigned long *out) {
  unsigned long x[2], nx[2], dec[2], tea[2], offset;

  /* initialization vector */
  /* Note: this is referenced at 0xa02e8320+0x30 by sub_A0234BB8 4.0.1 */
  x[0] = x[1] = 0xffffffff;

  for (offset=0; offset<len; offset+=8) {  /* block size of TEA is 8 bytes */
    int blen = 8;
    if ((len-offset)<8)
      blen = len-offset;

    int i;
    for (i=0; i<(blen>>2); i++)
      tea[i] = in[(offset>>2)+i];

    nx[0]  = tea[0];  nx[1]  = tea[1];  // save these for next CBC round
    decrypt(tea, key);
    dec[0] = tea[0] ^ x[0];
    dec[1] = tea[1] ^ x[1];
    x[0]   = nx[0];   x[1]   = nx[1];
    
    /* Plaintext */
    for (i=0; i<blen; i++)
      ((unsigned char *)out)[offset+i] = ((unsigned char *)dec)[i];
  }
}

int is_big_endian(void) {
  union {
    char     c[4];
    unsigned l;
  } u;
  u.c[0]=0x12; u.c[1]=0x34; u.c[2]=0x56; u.c[3]=0x78;
  if (u.l==0x12345678)
    return 1;
  else if (u.l==0x78563412)
    return 0;
  else {
    perror("can't determine endianness!\n");
    exit(1);
  }
}
#define BYTESWAP(x) \
    (((x)<<24) | (((x)&0xff00)<<8) | (((x)>>8)&0xff00) | ((x)>>24))

void memswp(unsigned long obuf[], unsigned long ibuf[], unsigned long length) {
  int i;
  for (i=0; i<length; i++) {
    obuf[i] = BYTESWAP(ibuf[i]);
  }
}
    
int main(int argc, char *argv[]) {
  FILE *f;
  void *ibuf, *buf;
  unsigned long offset, length, sha1hash[4];
  int i, decrypt = 1;
  int big_endian = is_big_endian();

  whoami = strrchr(argv[0], '/');
  whoami = (whoami == NULL) ? argv[0] : whoami+1;

  if (argc!=6)
    usage();
  
  if (*(argv[1])=='e')
    decrypt = 0;
  else if (*(argv[1])!='d')
    usage();
    
  if (sscanf(argv[3], "0x%x", &offset)!=1) {
    fprintf(stderr, "please use 0x notation for numbers\n");
    exit(1);
  }
  if (sscanf(argv[4], "0x%x", &length)!=1) {
    fprintf(stderr, "please use 0x notation for numbers\n");
    exit(1);
  }
  char *s = argv[5];
  if ((strlen(s)!=34) || (strncasecmp(s,"0x", 2)!=0)) {
    fprintf(stderr, "sha must be a 32-character hex string beginning with 0x\n");
    exit(1);
  }
  for (i=0; i<4; i++) {
    char s1[9];
    strncpy(s1,s+2+i*8,8);
    s1[8] = '\0';
    if (sscanf(s1, "%x", &sha1hash[i])!=1) {
      fprintf(stderr, "couldn't parse sha value\n");
      exit(1);
    }
  }

  ibuf = malloc(length); buf = malloc(length);
  if (!ibuf || !buf) {
    perror("malloc");
    exit(1);
  }
  if ((f = fopen(argv[2], "rb"))==NULL) {
    perror(argv[2]);
    exit(1);
  }
  if (fseek(f, offset, SEEK_SET)==-1) {
    perror("fseek");
    exit(1);
  }
  if (fread(ibuf, 1, length, f)!=length) {
    perror("fread");
    exit(1);
  }
  fclose(f);

  if (decrypt) {
    if (big_endian)  memswp(buf,ibuf,length);
    else             memcpy(buf,ibuf,length);
    decrypt_cbc(buf, length, &sha1hash[0], ibuf);
    if (big_endian)  memswp(buf,ibuf,length);
    else             memcpy(buf,ibuf,length);
  } else {
    if (big_endian)  memswp(buf,ibuf,length);
    else             memcpy(buf,ibuf,length);
    encrypt_cbc(buf, length, &sha1hash[0], ibuf);
    if (big_endian)  memswp(buf,ibuf,length);
    else             memcpy(buf,ibuf,length);
  }
  fwrite(buf, length, 1, stdout);

  exit(0);
}
```

# Example usage #

Here I am using teasz to decrypt the lock tables at offset 0xc88 of my seczone.  Encrypted, it looks like this:
```
% dd if=seczone.bin bs=1 skip=0xc88 count=0xe0 2>/dev/null | hexdump -C
00000000  9b 27 43 a9 f3 51 2f da  87 e7 8e f2 6a d1 63 1c  |.'C..Q/.....j.c.|
00000010  3f e7 1e 36 d2 e6 91 d0  6e a7 5d 98 a7 b9 ab e0  |?..6....n.].....|
00000020  c2 70 7e ad 65 35 90 d7  1f 8b 2e 45 8a f7 3e 7d  |.p~.e5.....E..>}|
00000030  95 2b f4 92 06 b1 6c e8  2e 21 5c 0c a7 8b 99 8c  |.+....l..!\.....|
00000040  ac ba 16 35 20 a8 a1 d3  89 d7 c3 42 7a 98 5e b4  |...5 ......Bz.^.|
00000050  50 8f df 0d cf ae 4a ac  e3 91 1a 70 92 31 0c 25  |P.....J....p.1.%|
00000060  0e 9a 72 4a 76 a8 db 5c  aa 8f 55 b7 5d 7d e4 00  |..rJv..\..U.]}..|
00000070  30 75 64 82 af bd fb a7  91 d3 88 52 1a cd ec 18  |0ud........R....|
00000080  a9 ce 40 db 58 6e 59 ad  69 ec 3a e0 ab c2 12 8b  |..@.XnY.i.:.....|
00000090  1f e1 03 ad 22 b9 f3 36  7e 45 f8 43 a6 fb b2 ce  |...."..6~E.C....|
000000a0  cd f9 8e 62 40 fe 9c e2  5e c0 a4 b4 d8 31 39 a4  |...b@...^....19.|
000000b0  33 e6 87 25 15 30 3b e1  32 a3 1f 27 d4 e4 c9 f2  |3..%.0;.2..'....|
000000c0  bb 70 8b 0e bb f3 f2 ad  3a e5 ee 24 85 1a dc 6f  |.p......:..$...o|
000000d0  63 c9 ba 7b 37 53 d1 67  d2 45 d9 20 f9 6d 5a b9  |c..{7S.g.E. .mZ.|
```

After passing that through teasz, I get:
```
% teasz d seczone.bin 0xc88 0xe0 $myhash | hexdump -C
00000000  00 01 05 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
00000010  00 00 00 00 01 00 00 00  01 01 05 01 00 00 00 00  |................|
00000020  09 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
*
00000040  05 05 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
00000050  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
00000060  00 00 00 00 00 00 00 00  05 05 00 00 00 00 00 00  |................|
00000070  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
*
00000090  05 05 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
000000a0  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
000000b0  00 00 00 00 00 00 00 00  05 05 00 00 00 00 00 00  |................|
000000c0  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
*
000000e0
```