# What it is #

This tool is designed to rebuild your lock table in the seczone area to repair the damage done by the original anySIM 1.0x unlockers (and its cousins, including the hardware unlock).  In essence, it re-virginizes your phone.

| **NOTE:  DO NOT POWER OFF YOUR PHONE (or allow it to auto-lock...see Settings->General->Auto-Lock) WHILE THE RE-VIRGINIZER IS RUNNING.  WAIT FOR IT TO COMPLETE!**  Wait for bbupdater to respond with "baseband unresponsive to pinging" when eliteloader.bin is the baseband.|
|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|

Before the public release, this method was personally confirmed by Zibri who intentionally bricked an iPhone (wrong IMEI, Invalid SIM) then ran through this process....it successfully restored the device to a working condition. Since the release, it's also been confirmed by the many commenters below and on the forums.  **It works perfectly...just follow the guide or use the scripts.**

# What it isn't #

The re-virginizer is neither intended for, nor necessarily required by, iPhones unlocked via IPSF.  See [TEASecZone](http://code.google.com/p/iphone-elite/wiki/TEASecZone) for further explanation.

It's also not required by an iPhone that has never run anySIM 1.0x unlockers, or the hardware unlock.  If your iPhone has seen none of these, or if it has only run anySIM 1.1x unlockers, then you don't need to become re-virginized.  Again, see  [here](http://code.google.com/p/iphone-elite/wiki/TEASecZone) for further details.

# Scripts to make life easier #

| **[Click here](http://iphone-elite.googlecode.com/files/virginator-0.4.zip) for an automated script that will give the user the option to just backup the seczone or both backup and revirginize.  This script also performs decent sanity checking prior to executing anything. NOTE: Requires BSD Subsystem be installed.** |
|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **_daurnimator_** has also written a script that automates this whole process.  Please download it [here](http://sharebee.com/31994895).                                                                                                                                                                                      |

# Forum #

See [here](http://rdgaccess.com/iphone-elite/viewtopic.php?t=98) for an iphone-elite discussion of this tool.

# FAQ #

| I got "bricked" when I upgraded to 1.1.1 after using anySIM/iUnlock on 1.0.2.  Will this allow me to safely upgrade to 1.1.1 without further problems? |
|:-------------------------------------------------------------------------------------------------------------------------------------------------------|

  * Yes.

| Will this reset the NCK counter? |
|:---------------------------------|

  * Yes.

| Can I use this method to re-virginize my iUnlock/anySIM'd phone in either 1.0.2 or 1.1.1 firmwares? |
|:----------------------------------------------------------------------------------------------------|

  * Yes.  However, this process involves reflashing the 3.14 baseband firmware so it is safer to downgrade to 1.0.2 OS firmware prior to proceeding.

| Is this tool useful if my phone was unlocked with IPSF? |
|:--------------------------------------------------------|

  * Only if you did a full NOR dump of your iPhone before doing the IPSF unlock.  (Note: if you did do that, please leave a comment below...you can be helped).  If you didn't do that, then IPSF _may_ be able to restore the baseband seczone to factory-default (because they _may_ have saved the RSA value they overwrote with 0's).  IPSF users please remember that until/unless Apple fixes the RSA bug in a future firmware release, then you have no need to undo IPSF.

| I attempted to virginize using geohot's server but it failed.  Is it safe to use this method now? |
|:--------------------------------------------------------------------------------------------------|

  * Maybe.  If the previous attempt to re-virginize accidentally wrote zeroes in the baseband seczone then this method will likely not work.  However, if the failure was non-damaging then you are welcome to try and see if this works.  Be sure to backup your seczone first!

**This guide assumes you've already established ssh access to your phone.  For added peace-of-mind it is advisable to install the vt100 terminal application or [MobileTerminal](http://code.google.com/p/mobileterminal) prior to running through this process just in case anything fails and you lose wifi access.**

  * All references to IP address **192.168.1.100** should be replaced by your phone's address.
  * Extract [virginizer\_pack.zip](http://rapidshare.com/files/64647223/virginizer_pack.zip.html) and copy the directory **virginizer\_pack** to your phone:
```
scp -r virginizer_pack root@192.168.1.100:/usr/bin
```

# Enough already, let's just do it! #

## Backup your seczone ##
Please backup your seczone flash page before running through this process.
  * Connect to your phone:
```
ssh root@192.168.1.100
```
  * Change to the working directory:
```
cd /usr/bin/virginizer_pack
```

  * Stop CommCenter:
```
launchctl unload /System/Library/LaunchDaemons/com.apple.CommCenter.plist
```

  * Make **norz** executable:
```
chmod +x ./norz
```

  * Make a seczone backup:
```
./norz seczone.backup 0x3FA000 0x2000
```

  * You should see:
```
# ./norz seczone.backup 0x3FA000 0x2000
geohot's nor dumper
all your norz are belong to us
super fast...just the way i like it
Dumping: A03FA000-A03FC000
Waiting for data...
Got Header: 77 0b cc
Increasing baud rate...
02 00 82 00 04 00 00 10 0E 00 A4 00 03 00 
02 00 01 08 14 00 00 00 00 00 A4 00 03 00 09 00 
00 00 33 2E 39 5F 4D 33 53 32 C3 0A 03 00 
02 00 84 00 00 01 01 00 00 00 89 00 00 00 62 88 
00 00 03 00 00 00 00 00 00 00 01 00 00 00 02 00 
00 00 00 00 40 00 3F 00 00 00 00 00 01 00 08 00 
00 00 00 20 00 00 00 00 00 00 00 00 00 00 00 00 
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
00 00 00 00 00 00 A6 03 03 00 
02 00 85 00 02 00 FF FF 85 02 03 00 
02 00 02 08 06 00 00 00 01 02 03 00 0E 08 03 00 
Wrote: 0xa03fa000
Wrote: 0xa03fb000
Dumped
resetbaseband
```
This will create a file called **seczone.backup** in the working directory.  Please copy this file to somewhere safe.

## Virginize ##

  * Make **iUnlock** executable:
```
chmod +x ./iUnlock
```

  * Now do:
```
./iUnlock ./ICE03.14.08_G.fls ./eliteloader.bin
```

  * You should see:
```
iUnlock v42.PROPER -- Copyright 2007 The dev team


Credits: Daeken, Darkmen, guest184, gray, iZsh, pytey, roxfan, Sam, uns, Zappaz, Zf

* Leet Hax not for commercial uses
Punishment: Monkeys coming out of your ass Bruce Almighty style.

Sending Begin Secpack command
Sending Erase command
Waiting For Erase Completion...
Sending Write command
00%
05%
15%
25%
35%
45%
55%
65%
75%
80%
90%
Sending End Secpack command
Validating the write command
FW are equal!
Completed.
Enjoy!
```

  * Make **bbupdater** executable:
```
chmod +x ./bbupdater
```

  * Now check the baseband status:
```
./bbupdater -v
```

  * You should see:
```
Resetting target...
pinging the baseband...
baseband unresponsive to pinging
Done
```

**Your seczone should now be repaired.**

  * Now reflash the default 3.14.08 baseband firmware:
```
./bbupdater -f ./ICE03.14.08_G.fls -e ./ICE03.14.08_G.eep
```

  * You should see:
```
Preparing to flash using /dev/tty.baseband at 750000 baud
Please reset target
Resetting target...
ProcessDetailUpdated: Boot-loader is active
ProcessDetailUpdated: EBL version: 3.9_M3S2 3..9
ProcessDetailUpdated: Boot mode is: CC
ProcessDetailUpdated: Baud rate set to 750000
ProcessDetailUpdated: Get flash id.
ProcessDetailUpdated: CFI stage 1
ProcessDetailUpdated: Flash ID is: 88620089
ProcessDetailUpdated: CFI stage 2
ProcessDetailUpdated: Boot process finished
ProcessOutlineUpdated: Reading SW version data
ProcessDetailUpdated: Receiving data.
ProgressUpdated: 100
ProcessDetailUpdated: Upload OK
ProcessOutlineUpdated: Process time was 133 msec.
Upgrade from  to 
Downloading EEP
ProcessOutlineUpdated: Start downloading from file ICE03.14.08_G.eep.
ProcessDetailUpdated: Sending sec-pack.
ProcessDetailUpdated: Load region 0
ProcessDetailUpdated: Sending end-pack.
ProcessDetailUpdated: Checksum OK.
ProcessDetailUpdated: Verify OK
ProcessOutlineUpdated: Process time was 1795 msec.
Downloading FLS
ProcessOutlineUpdated: Start downloading from file ICE03.14.08_G.fls.
ProcessDetailUpdated: Erasing the dynamic eeprom area
ProgressUpdated: 100
ProcessDetailUpdated: Sending sec-pack.
ProcessDetailUpdated: Load region 0
ProcessDetailUpdated: Sending data.
ProgressUpdated: 0
ProgressUpdated: 2
ProgressUpdated: 4
...
...
ProgressUpdated: 97
ProgressUpdated: 99
ProgressUpdated: 100
ProcessDetailUpdated: Load region 1
ProcessDetailUpdated: Sending data.
ProgressUpdated: 0
ProgressUpdated: 1
ProgressUpdated: 2
ProgressUpdated: 3
ProgressUpdated: 4
...
...
ProgressUpdated: 97
ProgressUpdated: 98
ProgressUpdated: 99
ProgressUpdated: 100
ProcessDetailUpdated: Sending end-pack.
ProcessDetailUpdated: Checksum OK.
ProcessDetailUpdated: Verify OK
ProcessOutlineUpdated: Process time was 1 min 52  sec.
Resetting target...
pinging the baseband...
issuing +cpwroff...
Done
```

  * Now check the baseband again:
```
./bbupdater -v
```

  * You should see:
```
Resetting target...
pinging the baseband...
issuing +xgendata...
    firmware: DEV_ICE_MODEM_03.14.08_G
 eep version: EEP_VERSION:207
eep revision: EEP_REVISION:7
  bootloader: BOOTLOADER_VERSION:3.9_M3S2
Done
```

  * Restart CommCenter:
```
launchctl load -w /System/Library/LaunchDaemons/com.apple.CommCenter.plist
```

### **Congratulations, you have virginized your phone!** ###
Now Reboot and be happy.
You backed up your seczone, right?  :-)