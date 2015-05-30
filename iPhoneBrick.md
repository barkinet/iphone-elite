## Want to know why your phones got bricked? ##

AnySIM and iUnlock were patched to make a routine exit with  0 (successful) to unlock the phone.  Only problem was that that routine is not only called by NCK but rather by about six routines total because the baseband code is very well optimized.  An analogy was made in IRC that it was basically like patching memcpy.  The other five didn't expect 00 to be there and were therefore spammed across your BB during upgrade.

For those of you clammoring to see some code, here it is.  You can read anySIM's source if you want further proof.

We see here the disassembly of the baseband where the patch was made:

```
Zibri: ROM:A0237B38                 MOV     R0, R6
Zibri: ROM:A0237B3C                 BL      IMPORTANT_ROUTINE
Zibri: ROM:A0237B40                 CMP     R0, #0
Zibri: ROM:A0237B44                 BNE     exit            ; NOP
Zibri: ROM:A0237B48                 ADR     R0, a00000000   ; "00000000"
Zibri: ROM:A0237B4C                 LDMIA   R0, {R0,R1}
Zibri: ROM:A0237B50                 STR     R0, [R5,#0x28]
Zibri: ROM:A0237B54                 STR     R1, [R5,#0x2C]
Zibri: ROM:A0237B58                 B       loc_A0237B98
```

Here we see the actual patch:
```
Zibri: ROM:A0235148                 ; PATCH: MOV R0, 0
Zibri: ROM:A0235148                 MOV     R0, R4
```
They just changed **"MOV [R0](https://code.google.com/p/iphone-elite/source/detail?r=0), [R4](https://code.google.com/p/iphone-elite/source/detail?r=4)"** to **"MOV [R0](https://code.google.com/p/iphone-elite/source/detail?r=0), 0"**

IMPORTANT\_ROUTINE is what would be memcpy in the analogy.  The patch was made to cause IMPORTANT\_ROUTINE exit 0 (successful) by putting 'MOV [R0](https://code.google.com/p/iphone-elite/source/detail?r=0), #0' at the end.  The coder wanted that routine to go to the "00000000".  Possibly attempts were made to NOP at the comment above but it was unsuccessful.  Possibly other routines were NOPed, but no luck.  So the person put 0 inside the routine and bam! it unlocked.  But as was stated above, this is the wrong routine.  So again, imagine you'd patched memcpy to behave this way.

Now that the secpak for 1.1.1 has been recovered we have provided a tutorial [here](http://code.google.com/p/iphone-elite/wiki/DowngradingBaseband) that will allow safe downgrade to 1.0.2's baseband so you can use your phone again.  iPhoneSIMFree claims to be able to reverse anySIM's damage and unlock a 1.1.1 phone that was "bricked" by the update.  However, it is yet unclear whether they're actually rebuilding the NOR seczone or merely patching out the integrity checks.  However, Zibri is hopeful that he can find a better place to patch but it requires extensive testing.   Meanwhile, our reverse-engineering of IPSF's method continues to offer helpful insight into how this should be done properly.

If you have 1.0.2 and a 3.14.08 baseband, locked and activated (no matter if legit or not) and are willing to test (and are not completely clueless) please contact us in IRC (link on the frontpage.)