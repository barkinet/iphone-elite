**Note: this will not help those waiting to upgrade from anySim/iunlock/geohot hw unlocked to 1.1.1 without bricking. Stay tuned for an updated 1.0.2/1.1.1 unlock to allow you to upgrade and keep all functionality.**

# UNBRICKING 1.1.1 UPGRADE #

## **WARNING:** ##
### Some have reported that if this process is done out of order or in any way fails that it's possible to lose wifi, bluetooth, and GSM modem functionality which may be very difficult to restore.  USE AT YOUR OWN RISK. ###

Here's the procedure to downgrade after you upgraded (by mistake) to firmware 1.1.1 on an anySIM unlocked iPhone.

_For simpler instructions for Mac and Windows see [here](http://rdgaccess.com/iphone-elite/viewtopic.php?p=141#141)._

# HOW TO #

**0.** Download iPhone 1.0.2 firmware from Apple [Here](http://appldnld.apple.com.edgesuite.net/content.info.apple.com/iPhone/061-3823.20070821.vormd/iPhone1,1_1.0.2_1C28_Restore.ipsw)

**1.** Change **ipsw** to **zip** then unpack it.

**2.** Extract the ramdisk file from it by typing
```
dd if=009-7698-4.dmg of=ramdisk.dmg bs=512 skip=4 conv=sync
```
(or remove the first 2048 bytes by using a hex editor)

**3.** Mount the ramdisk by double-clicking it (on Mac). On Windows use some HFS tools to peek inside it or get the files from someone who extracted it already.

**4.** Put your phone into DFU mode and do option-restore in iTunes. This will reflash everything to 1.0.2. You will get an error at the end because it couldnt reflash the baseband. You will end up with a yellow triangle.

**5.** Quit iTunes, launch iNdependence then quit it again.  Now relaunch iTunes. Press the power button on the iPhone for 3-4 seconds. After about 10 seconds you end up on the activation screen.

**6.** Complete the baseband downgrade by jailbreaking/activating, installing SSH on to the iPhone etc.  There are tons of wiki's about that so I won't repeat. (Probably also true for step 4 and 5.)

**7.** Extract the baseband firmware and EEPROM files of 3.14 from the ramdisk of
firmware 1.0.2. The files are named  **ICE03.14.08\_G.eep** and **ICE03.14.08\_G.fls** and are
located under **/usr/local/standalone/firmware**.

**8.** Get the secpack of baseband firmware 4.0 (some people have that, I have no idea
how they got it but its needed). Name it "secpack". (maybe http://**********.com/files/61914114/secpack40113.bin will help)

**9.** Download iEraser2 [here](http://www.fink.org/ieraser/) or from Geohot's blog.

**10.** Install all the tools onto the iPhone (I use the location /usr/local/bin.)  You need to have SSH access to the 1.0.2 firmware iPhone and upload **iEraser2**, the **secpack**, **ICE03.14.08\_G.eep**, **ICE03.14.08\_G.fls** and **anySIM 1.0.2**.

**11.** SSH to the phone. Stop CommCenter by typing:
```
launchctl unload -w /System/Library/LaunchDaemons/com.apple.CommCenter.plist
```

**12.** Now run:
```
bbupdater -v
```
It will tell you you run version 4.01 of the baseband.  "bbupdater" is a tool by Apple which is also on the ramdisk.

**13.** Execute this in the console, to give iEraser executable rights:
```
chmod +x ieraser
```

**14.** Run iEraser2. This will **WIPE** your baseband, given a file "secpack" is in the same
directory and this is a version 4 secpack.

**15.** Run the bbupdater command again:
```
bbupdater -v
```
This time it will not find any baseband firmware

**16.** Now do:
```
bbupdater -e ICE03.14.08_G.eep -f ICE03.14.08_G.fls
```
This will flash the 3.14.08 baseband firmware back to the iPhone.

**17.** Now check to make sure it worked:
```
bbupdater -v
```
It should tell you the version is 3.14

At this point you will still have an IMEI number starting with 004999... and
its not of use yet. So still bricked but at least downgraded to version 3.14.

**18.** Now run anySIM Version 1.0.2 (**note:** older versions might not be good here as
1.0.2 has a lot of fixes for this kind of stuff).

**19.** Start commCenter again:
```
launchctl load com.apple.CommCenter
```

Now you have an unlocked 3.14 baseband with IMEI being your original one!

**Congratulations you now fully recovered from your botched update to 1.1.1 and are back to 1.0.2.**
| You can stop here if you want to remain unlocked with iPhone firmware 1.0.2 with working phone. |
|:------------------------------------------------------------------------------------------------|

## **Do you want to return to factory-locked state?** ##
**NOTE:**
**This will not fix the NOR seczone corruption problem caused by anySIM/iUnlock so will therefore not "virginize" your phone.  This is only useful to remove the anySIM patch from the baseband firmware.**

Simply do:
```
launchctl unload -w /System/Library/LaunchDaemons/com.apple.CommCenter.plist
```
To stop commCenter

then do:
```
bbupdater -e ICE03.14.08_G.eep -f ICE03.14.08_G.fls
```
This will reflash the "locked" version of the baseband.

Now restart commCenter:
```
launchctl load com.apple.CommCenter
```

### Enjoy! ###

**If you don't want to pay for iPhoneSIMFree to have a less ...bad... unlock solution then stay tuned as we are actively working on our own safe unlock for both 1.0.2 and 1.1.1!**

_A tool automating all this is in the workings...._


### Notes ###

A few clarifications:

If you have 1.1.1 factory, never unlocked. the above method allows you to downgrade to factory 1.0.2 if you stop after the ieraser & bbupdater step. ieraser step allows you to reflash any firmware which is accepted by the bootloader. Otherwise you can only put same or newer firmware.

If you have 1.0.2 unlocked with anySIM and then bricked by update 1.1.1, this method allows you to downgrade to 1.0.2 and baseband 3.14 firmware (probably also 3.12 if you want that one) and bring your phone back to life. If you do the first bbupdater step and then the anySIM step, your IMEI will be back to normal. The second bbupdater step will bring your phone to a virgin state with proper IMEI so you could run IPSF and then upgrade to 1.1.1 if you want this. If you leave it unlocked with anySIM it will brick again when updating to 1.1.1 of course. This is known. But so far 1.1.1 is not recommended for anyone with 3rd party apps and non AT&T sim anyway. In 1.1.1 so many things are much harder to get to.

**Discuss this here**: http://rdgaccess.com/iphone-elite/viewtopic.php?t=27

[![](http://digg.com/img/badges/180x35-digg-button.gif)](http://digg.com/apple/How_to_Downgrade_your_iPhone_s_Baseband)