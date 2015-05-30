# Introduction #

This process is designed to rebuild your lock table in the seczone area and repair the damage done by the original anySIM unlock.  In essence, it re-virginizes your phone.

## FAQ ##

| I got "bricked" when I upgraded to 1.1.1 after using anySIM/iUnlock on 1.0.2.  Will this allow me to safely upgrade to 1.1.1 without further problems? |
|:-------------------------------------------------------------------------------------------------------------------------------------------------------|

  * Yes.

| Will this reset the NCK counter? |
|:---------------------------------|

  * Yes.

| Can I use this method to re-virginize my iUnlock/anySIM'd phone in either 1.0.2 or 1.1.1 firmwares? |
|:----------------------------------------------------------------------------------------------------|

  * Yes.  However, this process involves reflashing the 3.14 baseband firmware so it is safer to downgrade to 1.0.2 OS firmware prior to proceeding.

| Can I use this tool if my phone was unlocked with IPSF? |
|:--------------------------------------------------------|

  * No. Only IPSF can restore the baseband seczone to factory-default.

| I attempted to virginize using geohot's server but it failed.  Is it safe to use this method now? |
|:--------------------------------------------------------------------------------------------------|

  * Maybe.  If the previous attempt to re-virginize accidentally wrote zeroes in the baseband seczone then this method will likely not work.  However, if the failure was non-damaging then you are welcome to try and see if this works.  Be sure to backup your seczone first!

**This guide assumes you've already established ssh access to your phone.  For added peace-of-mind it is advisable to install the vt100 terminal application or MobileTerminal prior to running through this process just in case anything fails and you lose wifi access.**

  * All references to IP address **192.168.1.100** should be replaced by your phone's address.
  * Extract **virginizer\_pack.zip** and copy the directory **virginizer\_pack** to your phone:
```
scp -r virginizer_pack root@192.168.1.100:/usr/bin
```
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
  * Now do:
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

  * While still in the working directory (/usr/bin/virginizer\_pack) do:
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
ProgressUpdated: 5
ProgressUpdated: 7
ProgressUpdated: 8
ProgressUpdated: 10
ProgressUpdated: 11
ProgressUpdated: 13
ProgressUpdated: 15
ProgressUpdated: 16
ProgressUpdated: 18
ProgressUpdated: 19
ProgressUpdated: 21
ProgressUpdated: 22
ProgressUpdated: 24
ProgressUpdated: 25
ProgressUpdated: 27
ProgressUpdated: 29
ProgressUpdated: 30
ProgressUpdated: 32
ProgressUpdated: 33
ProgressUpdated: 35
ProgressUpdated: 36
ProgressUpdated: 38
ProgressUpdated: 40
ProgressUpdated: 41
ProgressUpdated: 43
ProgressUpdated: 44
ProgressUpdated: 46
ProgressUpdated: 47
ProgressUpdated: 49
ProgressUpdated: 50
ProgressUpdated: 52
ProgressUpdated: 54
ProgressUpdated: 55
ProgressUpdated: 57
ProgressUpdated: 58
ProgressUpdated: 60
ProgressUpdated: 61
ProgressUpdated: 63
ProgressUpdated: 65
ProgressUpdated: 66
ProgressUpdated: 68
ProgressUpdated: 69
ProgressUpdated: 71
ProgressUpdated: 72
ProgressUpdated: 74
ProgressUpdated: 75
ProgressUpdated: 77
ProgressUpdated: 79
ProgressUpdated: 80
ProgressUpdated: 82
ProgressUpdated: 83
ProgressUpdated: 85
ProgressUpdated: 86
ProgressUpdated: 88
ProgressUpdated: 90
ProgressUpdated: 91
ProgressUpdated: 93
ProgressUpdated: 94
ProgressUpdated: 96
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
ProgressUpdated: 5
ProgressUpdated: 6
ProgressUpdated: 7
ProgressUpdated: 8
ProgressUpdated: 9
ProgressUpdated: 10
ProgressUpdated: 11
ProgressUpdated: 12
ProgressUpdated: 13
ProgressUpdated: 14
ProgressUpdated: 15
ProgressUpdated: 16
ProgressUpdated: 17
ProgressUpdated: 18
ProgressUpdated: 19
ProgressUpdated: 20
ProgressUpdated: 21
ProgressUpdated: 22
ProgressUpdated: 23
ProgressUpdated: 24
ProgressUpdated: 25
ProgressUpdated: 26
ProgressUpdated: 27
ProgressUpdated: 28
ProgressUpdated: 29
ProgressUpdated: 30
ProgressUpdated: 31
ProgressUpdated: 32
ProgressUpdated: 33
ProgressUpdated: 34
ProgressUpdated: 35
ProgressUpdated: 36
ProgressUpdated: 37
ProgressUpdated: 38
ProgressUpdated: 39
ProgressUpdated: 40
ProgressUpdated: 41
ProgressUpdated: 42
ProgressUpdated: 43
ProgressUpdated: 44
ProgressUpdated: 45
ProgressUpdated: 46
ProgressUpdated: 47
ProgressUpdated: 48
ProgressUpdated: 49
ProgressUpdated: 50
ProgressUpdated: 51
ProgressUpdated: 52
ProgressUpdated: 53
ProgressUpdated: 54
ProgressUpdated: 55
ProgressUpdated: 56
ProgressUpdated: 57
ProgressUpdated: 58
ProgressUpdated: 59
ProgressUpdated: 60
ProgressUpdated: 61
ProgressUpdated: 62
ProgressUpdated: 63
ProgressUpdated: 64
ProgressUpdated: 65
ProgressUpdated: 66
ProgressUpdated: 67
ProgressUpdated: 68
ProgressUpdated: 69
ProgressUpdated: 70
ProgressUpdated: 71
ProgressUpdated: 72
ProgressUpdated: 73
ProgressUpdated: 74
ProgressUpdated: 75
ProgressUpdated: 76
ProgressUpdated: 77
ProgressUpdated: 78
ProgressUpdated: 79
ProgressUpdated: 80
ProgressUpdated: 81
ProgressUpdated: 82
ProgressUpdated: 83
ProgressUpdated: 84
ProgressUpdated: 85
ProgressUpdated: 86
ProgressUpdated: 87
ProgressUpdated: 88
ProgressUpdated: 89
ProgressUpdated: 90
ProgressUpdated: 91
ProgressUpdated: 92
ProgressUpdated: 93
ProgressUpdated: 94
ProgressUpdated: 95
ProgressUpdated: 96
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

### **Congratulations, you have virginized your phone!** ###
Now Reboot and be happy.
You backed up your seczone, right?  :-)