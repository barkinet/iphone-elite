# What is oneSIM ? #



**Note:**  this has nothing to do with the upcoming 'revirginator'.  But stay tuned for that!

oneSIM is an "undo" of anySIM 1.1.  It reverses the patches made by anySIM 1.1.  _It's useful to developers, researchers, and testers_.   **BUT end users should read the next section if you haven't yet applied anySIM 1.1.  (It will save you one or more unlock counts!  Those counts add up...to "5" specifically)**

oneSIM un-patches the bytes that anySIM changes, back to their original values :)  It's a useful tool to quickly undo anySIM 1.1, to help further testing.

oneSIM 1.1 is an "undo" **ONLY** for iPhones patched with anySIM 1.1 owned by developers, researchers, and testers (_thank you, testers!_)

# Are you about to use anySIM 1.1? #

**ATTENTION: The issue with NCK in anySIM 1.1 is being further investigated. Please don't rush with your judgments.**

Zibree found an oversight with anySIM 1.1.  It needlessly increases your NCK unlock attempt count.  Given how early we are in the iPhone "cat and mouse" game, you probably don't want to waste those counts.

If you are about to unlock using anySIM 1.1, you may want to make a patch first!   anySIM 1.1 still executes the command ` AT+CLCK="PN",0,"00000000"`.  That command was needed by anySIM 1.0x because of the specific patch used.  But the new patch no longer requires it.   The command will make your NCK unlock counter increment -- why do that if you don't need to?.

To patch it, take an HEX editor and search for the AT+CLCK command above.  Patch it to be "ATZ" followed by 0x0d \x00.

That's all folks!