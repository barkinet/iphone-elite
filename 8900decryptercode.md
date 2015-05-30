# Introduction #

This program will decode any 8900 Apple file.

# Details #

This is the code that decrypts any 8900 Apple file. The code was tested on the 1.1.1 ramdisk as well as on the 1.1.1 _applelogo.img2_ file. The header and footers are stripped from the outpute file.

**Update 1:** code has also been confirmed to work with 1.1.2 and 1.1.3 ramdisk.

**Update 2:** ZIP-file now contains the windows executable compiled under cygwin including the openssl file cygcrypto-0.9.8.dll as well as the iPhone executable. I have no Mac available so you will have to re-compile the source file on your Mac yourself.

**Update 3:** code will now also handle correctly un-encrypted 8900 files by only stripping the header and footer. An example of this is the _applelogo.img2_ file in the early firmwares.

.fls and .eep images can be found in _/usr/local/standalone/firmware/_



# Usage #

For usage on the iPhone, upload the file _8900decrypter_ to _/usr/bin/_ or to _/var/root_ and make this file executable with the _chmod +x_ command. Also upload the 8900 file that you wish to decrypt to the iPhone. Make sure you have enough space available (most ramdisks will need about 30-35MB). Then execute commands similar to the ones listed a little below.

For usage on Windows, make sure to put the _cygcrypto-0.9.8.dll_ from the ZIP-file in your working directory as well as the _8900decrypter.exe_ file (not the _8900decrypter_ file without the _.exe_). Then execute commands similar to the ones below.

```
# ./8900decryptor applelogo.img2 applelogo-decrypted.img2
# ./8900decryptor 022-3629-9.dmg 022-3629-9.dmg-decrypted.img2
```

# Typical output #

```
# ./8900decryptor 022-3748-284.dmg 022-3748-284-decrypted.dmg
iPhone 8900 decryptor by PmgR,
extended and modified from original file by aljen.
Thanks to hardware decrypt key found by by dev & elite teams.

[*] filename                    : 022-3748-284.dmg
[*] magic                       : 89001.0♥
[*] version                     : 1.0♥
[*] encrypted                   : 1
[*] start of data               : 0x800
[*] size of data                : 0x11e3000
[*] footer signature offset     : 0x11e3000
[*] footer certificate offset   : 0x11e3080
[*] footer certificate length   : 0xc0a
[*] header key 1                : f7 30 0b 2c fe cc 4b 52 88 19 c9 99 a7 1c a6 25 76 46 d2 23 9b 96 41 11 86 f5 02 26 71 b0 d5 2e
[*] header key 2                : e3 19 1e 73 9e 87 c3 6f aa 0f 26 5a 6c d6 54 bb
```

# Credits #

This code was extended and modified by PmgR from the original 8900decrypter.c file by aljen.

And of course not to forget the dev & elite teams who found the key!!

# C code #

```
#include <inttypes.h>
#include <stdio.h>
#include <string.h>
#include <sys/types.h>
#include <unistd.h>
#include <openssl/sha.h>
#include <openssl/aes.h>

struct {
	unsigned char magic[ 4 ];
	unsigned char version[ 3 ];
	unsigned char encrypted;
	unsigned char unknownNull[4];
	unsigned int sizeOfData;
	unsigned int footerSignatureOffset;
	unsigned int footerCertOffset;
	unsigned int footerCertLen;
	unsigned char key1[ 32 ];
	unsigned char unknownVersion[ 4 ];
	unsigned char key2[ 16 ];
	unsigned char unknow3[ 1968 ];
} header8900;

char inbuf[ 65536 ];
char outbuf[ 65536 ];

void convert_hex(char *str, uint8_t *bytes, int maxlen) {
  int slen = strlen(str);
  int bytelen = maxlen;
  int rpos, wpos = 0;

  for(rpos = 0; rpos < bytelen; rpos++) {
    sscanf(&str[rpos*2], "%02hhx", &bytes[wpos++]);
  }
}

void print_hex( char* text, uint8_t* data, uint32_t len )
{
	uint32_t ctr;
	char* sep;
	if( len > 64 )
		len = 64;
	printf( "%s", text );
	for( ctr = 0; ctr < len; ctr++ )
	{
		printf( "%02x ", data[ ctr ] );
	}
	printf( "\n" );
}

int main( int argc, char** argv )
{
	FILE *infile, *outfile;
	int encrypted;
	off_t data_begin, data_current, data_end, data_len;
	AES_KEY ctx, aes_decrypt_key;
	uint8_t aes_key[16];

	unsigned char ramdiskKey[32]="188458A6D15034DFE386F23B61D43774";
	unsigned char ramdiskiv[1];

	unsigned char keybuf[ 16 ];
	unsigned char iv[ AES_BLOCK_SIZE ];
	
	if( argc != 3 )
	{
		fprintf( stderr, "Usage: %s infile outfile\n", argv[ 0 ] );
		return 1;
	}

	infile = fopen( argv[ 1 ], "r" );
	if( !infile )
	{
		perror( "infile fopen" );
		return 1;
	}

	outfile = fopen( argv[ 2 ], "w+" );
	if( !outfile )
	{
		perror( "outfile fopen" );
		return 1;
	}

	if( sizeof( header8900 ) != fread( &header8900, 1, sizeof( header8900 ), infile ) )
	{
		fprintf( stderr, "Can't read header\n" );
		return 1;
	}

	if( ( header8900.magic[ 0 ] != 0x38 ) && // 8
	    ( header8900.magic[ 1 ] != 0x39 ) && // 9
	    ( header8900.magic[ 2 ] != 0x30 ) && // 0
	    ( header8900.magic[ 3 ] != 0x30 ) )  // 0
	{
		fprintf( stderr, "Bad magic. This file is not an 8900 Apple file\n" );
		return 1;
	}

	if( header8900.encrypted == 0x03 )
		encrypted = 1;
	else if( header8900.encrypted = 0x04 )
		encrypted = 0;

	data_begin = sizeof( header8900 );
	data_len = header8900.sizeOfData;

	printf( "iPhone 8900 decryptor by PmgR,\n");
	printf( "extended and modified from original file by aljen.\n");
        printf( "Thanks to hardware decrypt key found by by dev & elite teams.\n\n");
	printf( "[*] filename	\t\t: %s\n", argv[ 1 ] );
	printf( "[*] magic	\t\t: %s\n", header8900.magic );
	printf( "[*] version	\t\t: %s\n", header8900.version );
	printf( "[*] encrypted	\t\t: %d\n", encrypted );
	printf( "[*] start of data    \t\t: 0x%02x\n", data_begin );
	printf( "[*] size of data\t\t: 0x%02x\n", header8900.sizeOfData );
	printf( "[*] footer signature offset\t: 0x%02x\n", header8900.footerSignatureOffset );
	printf( "[*] footer certificate offset\t: 0x%02x\n", header8900.footerCertOffset );
	printf( "[*] footer certificate length\t: 0x%02x\n", header8900.footerCertLen );
	print_hex( "[*] header key 1\t\t: ", header8900.key1, sizeof( header8900.key1 ) );
	print_hex( "[*] header key 2\t\t: ", header8900.key2, sizeof( header8900.key2 ) );

	//memset( keybuf, 0, 32 );
        //memcpy( keybuf, header8900.key1, 32 );
	convert_hex(ramdiskKey, aes_key, 16);
	AES_set_decrypt_key( aes_key, 128, &aes_decrypt_key );
	memset( iv, 0, AES_BLOCK_SIZE );
	//memcpy( iv, header8900.key2, 16 );

	fseek( infile, data_begin, SEEK_SET );
	//printf("dataend: %d\n", data_len);

	data_current=0;
	while( fread( &inbuf, 1, AES_BLOCK_SIZE, infile ) > 0 & data_current<data_len)
	{

	  if ( encrypted ) {
	    AES_cbc_encrypt( inbuf, outbuf, AES_BLOCK_SIZE, &aes_decrypt_key, iv, AES_DECRYPT );
	    fwrite( outbuf, 1, AES_BLOCK_SIZE, outfile );
	  } else {
	    fwrite( inbuf, 1, AES_BLOCK_SIZE, outfile );
	  }	    
	  data_current = data_current + AES_BLOCK_SIZE;
	}

	if( infile )
		fclose( infile );
	if( outfile )
		fclose( outfile );
	return 0;
}

```