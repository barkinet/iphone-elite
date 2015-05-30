# Overview #

This page details the CFI Query Response, and how it is used to reveal the norID of your iPhone's NOR chip.

# Details #

To see how norID is retrievable through CFI query, I'll be referring to the [Intel W18 datasheet](http://download.intel.com/design/flcomp/datashts/29070114.pdf)

I took my captured Query Response in the LTOKEN of my IPSF transaction.  I removed every other byte, because
only the low byte of the 16-bit read at each address is meaningful in those first 0x100 bytes.  This results in a query response that looks like:

```
0000  89 62 03 03 89 cf 03 03  89 62 03 03 89 cf 03 03  |.b.......b......|
0010  51 52 59 03 00 39 00 00  00 00 00 17 19 b4 c6 04  |QRY..9..........|
0020  00 0a 00 04 00 03 00 16  01 00 00 00 02 3e 00 00  |.............>..|
0030  01 07 00 20 00 00 00 00  00 50 52 49 31 33 e6 03  |... .....PRI13..|
0040  00 00 01 03 00 18 c0 01  80 00 03 03 03 04 01 02  |................|
0050  03 07 02 07 00 11 00 00  01 07 00 00 01 64 00 01  |.............d..|
0060  03 01 00 11 00 00 02 06  00 00 01 64 00 01 03 07  |...........d....|
0070  00 20 00 64 00 01 03 00  00 ff ff ff ff ff ff ff  |. .d............|
```

Table 42 of the W18 datasheet says: 0x39+0xe(=0x47) is the # of Protected Register fields (01).
  * (That same table has a typo you can see...the Add: columns for the following 4 bytes are correct but the (P+x) expressions on the first column are off by 1)

Anyway, the bytes at 0x48 through 0x4e are the "Protection Field 1"
  * 80 00 03 03   means jedec-plane physical address 0080 has 15 bits of "Protection Register lock byte" followed by 8 bytes of OTP and 8 bytes of user programmable

"jedec-plane physical address 0080" is multiplied by 2 due to 16-bit bus so that's offset 0x100 from the query response

"Protection Register lock" is shown in Figure 34.  In a captured CFI query response beginning at offset 0x100, you see FFFE (in little endian). That means the factory OTP really is read-only and the user programmable
is writeable.

Then the 8 bytes of factory OTP follow. That's the "norID"

Other interesting parts of the query response:


|  Off |  Val | Meaning |
|:-----|:-----|:--------|
|  2C  |  02  | Device has two regions with different size erase blocks in each |
|      |      |         |
|  2D  |  3E   | Region 1 has 0x003F erase blocks |
|  2E  |  00  |         |
|  2F  |  00   | Region 1 erase block size is 0x0100 x 256-byte (0x10000, 64KByte) |
|  30  |  01  |         |
|      |      |
|  31  |  07   | Region 2 has 0x0008 erase blocks |
|  32  |  00  |         |
|  33  |  20  |   Region 2 erase block size is 0x0020 x 256-byte (0x02000, 8KByte) |
|  34  |  00  |         |



All of this is consistent with the W18 baseband NOR being:

  * 4MBytes (32MBits)
  * 71 erase "blocks" (first 63 are 64KB, last 8 are 8KB)
  * 8   x 4MBit   "partitions" with independent erase/program state machines
  * iPhone uses a "Top" -T parameter device
  * See memory map in Table 2 of W18 datasheet
  * 88-ball QUAD+ SCSP package