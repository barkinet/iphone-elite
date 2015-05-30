# Overview #

This is a description of how the secpack header can be decrypted and what it contains.

## I.  secpack .bin file structure ##
  * 0x000 - 0x07F RSA-encrypted header
  * 0x080 - 0x1FF zeros
  * 0x200 - 0x7FF secpack payload

## II.  RSA-encrypted header ##
The 0x80-byte header is encrypted by Apple's private RSA key and can be decrypted using
one (the first one) of Apple's two unique public RSA keys (modulus and exponent, discussed later). It contains the SHA-1 sums
of the secpack payload and also of the firmware image it is authorized
to upload to iPhone.

It can be broken down into sections H,I,0,J,K,L:
```
000:  HH HH II II II II II II  II II II II II II II II
010:  II II II II II II II II  II II II II II II II II
020:  II II II II II II II II  II II II II II II II 00
030:  JJ JJ JJ JJ JJ JJ JJ JJ  JJ JJ JJ JJ JJ JJ JJ JJ
040:  JJ JJ JJ JJ KK KK KK KK  KK KK KK KK KK KK KK KK
050:  KK KK KK KK KK KK KK KK  LL LL LL LL LL LL LL LL
060:  LL LL LL LL LL LL LL LL  LL LL LL LL LL LL LL LL
070:  LL LL LL LL LL LL LL LL  LL LL LL LL LL LL LL LL
```

```
 * H: Length  2 (0x02)  PKCS1v1.5 values. Always 00 01
 * I: Length 45 (0x2D)  Padding.  In 4.02.13 it's all FF's, but in other releases it isn't
 * 0: Length  1 (0x01)  End of padding marker.  Always 00
 * J: Length 20 (0x14)  SHA-1 sum of firmware this secpack is authorized for
 * K: Length 20 (0x14)  SHA-1 sum of secpack from 0x200-0x800 of secpack.bin
 * L: Length 40 (0x28)  Extra bytes that aren't checked by either the 3.9 or 4.6 Apple bootloader (thanks, geohot!)
```

For example, secpack40213.bin's header decrypts into this:
```
000:  00 01 ff ff ff ff ff ff  ff ff ff ff ff ff ff ff
010:  ff ff ff ff ff ff ff ff  ff ff ff ff ff ff ff ff
020:  ff ff ff ff ff ff ff ff  ff ff ff ff ff ff ff 00
030:  6f 3a ca b3 13 65 f4 dc  77 bd ef 3b 33 06 d8 a3
040:  8d b3 df bd 0a 05 b2 f0  1f f3 b8 ef 46 53 11 4c
050:  2d d4 fb bb 1a ef 0d df  ff ff ff ff ff ff ff ff
060:  ff ff ff ff ff ff ff ff  00 00 00 00 00 00 00 00
070:  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00
```

The SHA-1 fields need to be byte-swapped at each dword to reveal:
```
    J = SHA-1 sum of firmware  = b3ca3a6fdcf465133befbd77a3d80633bddfb38d
    K = SHA-1 sum of secpack   = f0b2050aefb8f31f4c115346bbfbd42ddf0def1a
```

## III. Explicitly computing the SHA-1 sums of secpacks and firmware payloads ##
If we were to manually and explicitly compute the SHA-1 sums of the various payloads, we'd find the secpack SHA-1 sums  to date are:
```
bash% for i in 31206 31408 40113 40213 ; do echo -n "  $i: "; \
                      dd if=secpack$i.bin skip=1 2>/dev/null |\
                      openssl sha1; done
  31206: e0b87e4986ee0c63381b259f97e853fe083adc57
  31408: 55711df989a9d1d5e674168ee3dd3834a9ce6ee1
  40113: ae9626f507fa22c7a96cb01b14351ee163a789f3
  40213: f0b2050aefb8f31f4c115346bbfbd42ddf0def1a
```

A firmware ICE.fls file has these fields in its header:
  * +0x1c: File offset of start of firmware image
  * +0x20: Length of firmware image in bytes

The file offset for the firmware releases so far is 0x9a4 (2468 - "who do we appreciate....APPLE...APPLE....Gooooo APPLE")

The lengths of the firmware releases to date are:
```
bash% len[31206]=0x302218; len[31408]=0x303cb4; len[40113]=0x304468; len[40213]=0x302400  # OS X "dd"
bash% len[31206]=3154456 ; len[31408]=3161268 ; len[40113]=3163240 ; len[40213]=3154944   # Not OS X "dd"
```
So, let's go ahead and manually compute the SHA-1 sums of the firmware images.
```
bash% for i in 31206 31408 40113 40213 ; do echo -n "  $i: ";   \
  dd if=fw$i.fls bs=2468 skip=1 of=fw$i.bin 2>/dev/null ;\
  dd if=fw$i.bin bs=${len[$i]} count=1 2>/dev/null | openssl sha1 ; done
  31206: e4c860ae4dd4a24ba4eae9178b4bb5642c82cd1d
  31408: 0d4b09f5772ac2307e3367b948b5ac3b3bb63738
  40113: d13f0f1ae4496508d51c7b10501f5efb06c16a30
  40213: b3ca3a6fdcf465133befbd77a3d80633bddfb38d
```

### IV.  Apple's public keys ###

  * 4 RSA public modulus values are contained in the 4.6 BL.
  * The same modulus values are contained in the 3.9 bootloader, but they're at other locations.
  * The 128 bytes of each modulus must be completely reversed (as discovered by ghost\_000) to be useful.
    * Q: Is this obfuscation or just some endianness artifact?
    * A: Fred said this modulus reversal is common in embedded devices because the specific algorithms that use it are more efficient (faster) when it's ordered that way.
  * Directly before the modulus values are Apple's public exponent value (0x3) and key length (0x400, 1024 bits)
  * Notice that mod1==mod3, and mod2==mod4
```
bash% modloc[1]=0xc898; modloc[2]=0xcaa4; modloc[3]=0x249c; modloc[4]=0x26a8  # OS X "dd"
bash% modloc[1]=51352 ; modloc[2]=51876 ; modloc[3]=9372  ; modloc[4]=9896    # Not OS X "dd"
bash% for i in 1 2 3 4; do echo -n "mod$i="; \
    dd if=BOOT04.06_M3S2.bin bs=1 skip=${modloc[$i]} count=128 2>/dev/null |\
    perl -0777e 'print unpack("H*",join("",reverse(split(//,<>)))),"\n\n"'; done
mod1=ea663021b3eaedb5fd495fbccb1dad65c7575360accfe296f710d6007157d5d1aa150cbd3c2e8c879e4bb4b5c4669f463dd15efc0573b17872aced1e8089e54fe0c5576b9aad88374a5704c58527a44fe13480b27929b5e3db72c210c634cb4ff2d17a180ea143860122ae0b084d323f54b28214d819dad8a2f90c874e9fbc05

mod2=c7b74b61376c083fc949a2653fe1339ffbc02b8081b45ece23b6388efd26534d264f33571b7a16cfe2167c4bd8bab9af4dc3da78212fdef92eebd0cb84d86b41a35d0fc2a8e3062473c11cb22694dffb95d61727a0cee9c0064c513931fdcf126ecb827a4e91f4686ca037645706f973b0cc53cf8c5844ce9d7b75e3baae230b

mod3=ea663021b3eaedb5fd495fbccb1dad65c7575360accfe296f710d6007157d5d1aa150cbd3c2e8c879e4bb4b5c4669f463dd15efc0573b17872aced1e8089e54fe0c5576b9aad88374a5704c58527a44fe13480b27929b5e3db72c210c634cb4ff2d17a180ea143860122ae0b084d323f54b28214d819dad8a2f90c874e9fbc05

mod4=c7b74b61376c083fc949a2653fe1339ffbc02b8081b45ece23b6388efd26534d264f33571b7a16cfe2167c4bd8bab9af4dc3da78212fdef92eebd0cb84d86b41a35d0fc2a8e3062473c11cb22694dffb95d61727a0cee9c0064c513931fdcf126ecb827a4e91f4686ca037645706f973b0cc53cf8c5844ce9d7b75e3baae230b
```



### V.  Putting it all together ###

---


If we combine the information from the above sections, we get:
```
bash% for i in 31206 31408 40113 40213 ; do echo "secpack$i decrypted header:"; \
                               dd if=secpack$i.bin bs=128 count=1 2>/dev/null  |\
                               perlrsa -k=3 -n=$mod1 | hexdump -Cv; done

secpack31206 decrypted header:
00000000  00 01 e4 b3 e1 77 f3 a3  21 80 90 fe 59 40 fa 0b  |.....w..!...Y@..|
00000010  01 2d 0f a9 08 8b e9 47  e5 75 04 64 4b 04 68 13  |.-.....G.u.dK.h.|
00000020  c1 17 43 f7 5e 28 24 a8  a7 aa f2 9e 44 ce 7a 00  |..C.^($.....D.z.|
00000030  ae 60 c8 e4 4b a2 d4 4d  17 e9 ea a4 64 b5 4b 8b  |.`..K..M....d.K.|
00000040  1d cd 82 2c 49 7e b8 e0  63 0c ee 86 9f 25 1b 38  |...,I~..c....%.8|
00000050  fe 53 e8 97 57 dc 3a 08  ff ff ff ff ff ff ff ff  |.S..W.:.........|
00000060  ff ff ff ff ff ff ff ff  00 00 00 00 00 00 00 00  |................|
00000070  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
00000080
secpack31408 decrypted header:
00000000  00 01 14 8b d6 31 b2 a0  c8 ca 69 a4 0f fd 22 e1  |.....1....i...".|
00000010  1e 74 85 6e 53 0c 23 91  3e 4c 01 c7 1f 97 2c 74  |.t.nS.#.>L....,t|
00000020  b6 c8 f6 a6 22 8b b6 88  80 11 66 cb 95 df d2 00  |....".....f.....|
00000030  f5 09 4b 0d 30 c2 2a 77  b9 67 33 7e 3b ac b5 48  |..K.0.*w.g3~;..H|
00000040  38 37 b6 3b f9 1d 71 55  d5 d1 a9 89 8e 16 74 e6  |87.;..qU......t.|
00000050  34 38 dd e3 e1 6e ce a9  ff ff ff ff ff ff ff ff  |48...n..........|
00000060  ff ff ff ff ff ff ff ff  00 00 00 00 00 00 00 00  |................|
00000070  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
00000080
secpack40113 decrypted header:
00000000  00 01 bb 13 44 b7 b6 f1  be 0d b6 14 8d 87 25 bc  |....D.........%.|
00000010  03 23 61 f2 41 4d 63 dc  16 34 e6 2d 7e c0 ee 08  |.#a.AMc..4.-~...|
00000020  e1 ca 8f b1 d1 8c fb 58  9f fa f1 62 c8 a2 2f 00  |.......X...b../.|
00000030  1a 0f 3f d1 08 65 49 e4  10 7b 1c d5 fb 5e 1f 50  |..?..eI..{...^.P|
00000040  30 6a c1 06 f5 26 96 ae  c7 22 fa 07 1b b0 6c a9  |0j...&..."....l.|
00000050  e1 1e 35 14 f3 89 a7 63  ff ff ff ff ff ff ff ff  |..5....c........|
00000060  ff ff ff ff ff ff ff ff  00 00 00 00 00 00 00 00  |................|
00000070  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
00000080
secpack40213 decrypted header:
00000000  00 01 ff ff ff ff ff ff  ff ff ff ff ff ff ff ff  |................|
00000010  ff ff ff ff ff ff ff ff  ff ff ff ff ff ff ff ff  |................|
00000020  ff ff ff ff ff ff ff ff  ff ff ff ff ff ff ff 00  |................|
00000030  6f 3a ca b3 13 65 f4 dc  77 bd ef 3b 33 06 d8 a3  |o:...e..w..;3...|
00000040  8d b3 df bd 0a 05 b2 f0  1f f3 b8 ef 46 53 11 4c  |............FS.L|
00000050  2d d4 fb bb 1a ef 0d df  ff ff ff ff ff ff ff ff  |-...............|
00000060  ff ff ff ff ff ff ff ff  00 00 00 00 00 00 00 00  |................|
00000070  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
00000080
```

If you refer back to section II to determine the SHA-1 keys in the above dumps,
and refer back to section III to see the computed SHA-1 keys on the actual
secpack and firmware files, you'll see they match.

  * Notice that offset 0x2f is always 0.  That marks the end of the padding.
    * The bootloader expects two SHA-1 sums follow the 00 marker.
    * The 4.6 BL requires at least 10 FFs to have been seen before the 00 marker.
  * The padding does matter to both 3.9 and 4.6 bootloaders
  * As geohot points out, those last 0x28 may be the key (no pun intended) to software unlocking 4.6 bootloader iPhones