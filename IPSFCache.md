# Overview #

IPSF users can probably recover their original seczone token value before IPSF zeroed it out.

# Details #

## Saving the cache ##

**IPSF users should do the following**

  * Make sure you have the BSD subsystem on your iPhone
  * Log into your iPhone and type: `cp $(find /var/root/Library/Caches/bbsimfree -name "*.cache") /ipsf.cache`
    * If you get an error like "missing destination file" then you either have no cache or you typed something wrong
  * Copy that cache off of your iPhone and save it!  It contains very valuable data.

The existence of this important file was reported by sh1n1gam1 on the [iPhone Elite forums](http://rdgaccess.com/iphone-elite/viewtopic.php?p=803)

## Using the cache ##

To recover your token manually, do the following:

  * Using a hex editor, find the LTOKEN1.0 string in the cache and note its starting offset (call this value "a").   In my cache, a=0x1e7.
  * Compute the offset of encrypted seczone, which will be 0x810 bytes after the start of that string: b = a + 0x810.  So for my cache, b = 0x9f7
    * See [HowIPSFWorks](http://code.google.com/p/iphone-elite/wiki/HowIPSFWorks) for details about that 0x810 constant
  * Extract the 0x2000 bytes beginning at that offset into a file called "en"
  * Run geohot's deipsf program to produce the "de" file.  That is your original seczone.
    * Note that deipsf works only on little-endian architectures like x86 or ARM
    * Sanity check the "de" file.  It should begin with 0x100 bytes of "ff", and then non-ff bytes.  If you don't see that, then something went wrong...try again.
  * Use the decrypted seczone in a flow like this one: http://rdgaccess.com/iphone-elite/viewtopic.php?t=158