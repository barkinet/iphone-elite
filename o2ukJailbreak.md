## Credits ##

Based on directions at http://www.iphonealley.com/news/unofficial-1-1-2-jailbreak-instructions

Only parts by me are those pertaining to the UK iPhone setup.

**THIS IS NOT AN OFFICIAL DEV TEAM RELEASE, THERE WILL BE A MORE USER FRIENDLY VERSION IN THE FUTURE**

## Before you start ##

Before you start you will need;
  * The 1.1.1 ipsw firmware file
  * The 1.1.2 ipsw firmware file
  * iPhuc utilities
  * Access to the internet by wifi

## The Jailbreak ##

Jailbreak as per instructions at http://www.iphonealley.com/news/unofficial-1-1-2-jailbreak-instructions, instead of using the Installer.app from AppTapp on Nullriver's site use the Installer.app and libarmfp.dylib contained within http://jailbreakme.com/files/root.zip

## O2 Specific ##

If you're using O2 you'll now have an activated, jailbroken iPhone with 1.1.2 firmware (again) and a working phone.

However, VisualVoicemail and EDGE will not work properly - this is easily fixed, simply install BSD Subsystem and OpenSSH using Installer.app and then SSH into the iPhone and type the following two commands;

`rm /var/root/Library/Preferences/com.apple.carrier.plist`

`ln -s /System/Library/Frameworks/CoreTelephony.framework/Support/O2_UK.plist /var/root/Library/Preferences/com.apple.carrier.plist`

`rm -f /var/root/Library/Preferences/SystemConfiguration/preferences.plist`

Reboot your iPhone, and all going well you'll now have EDGE and VisualVoicemail working again

You now have a 1.1.2 O2-UK iPhone with all the third party apps you could want. Enjoy :)