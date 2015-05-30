# Details #

For those with dropbear loaded by au.asn.ucc.matt.dropbear:

1.) Install BSD Subsystem using Installer.

2.) install UIctl from Installer.

3.) Run UIctl (be careful with this application). Tap au.asn.ucc.matt.dropbear and choose "unload -w."

4.) Install OpenSSH with Installer.

5.) Connect to your iPhone using WinSCP, username/password of root/alpine.

6.) Using WinSCP delete the following files and folders on the iPhone:
/etc/dropbear/dropbear\_rsa\_host\_key
/etc/dropbear/dropbear\_dss\_host\_key
/etc/dropbear (folder)
/System/Library/LaunchDaemons/au.asn.ucc.matt.dropbear.plist
/usr/bin/dropbear

7.) Restart iPhone. Verify in UIctl that au.asn.ucc.matt.dropbear is gone, if so dropbear is not loaded (which is what we want!).

8.) You may delete UIctl if desired (using Installer).




For those with dropbear loaded by com.apple.update.plist:

1.) Install the BSD Subsystem using Installer.

2.) Install OpenSSH with Installer.

3.) Copy com.apple.update.plist.orig from your sshkit folder to: /System/Library/LaunchDaemons/com.apple.update.plist

(Delete com.apple.update.plist beforehand, and make sure com.apple.update.plist.orig is named com.apple.update after copying.)

4.) Restart iPhone.

5.) Connect to your iPhone using WinSCP, username/password of root/alpine.

6.) Using WinSCP delete the following files and folders on the iPhone:
/etc/dropbear/dropbear\_rsa\_host\_key
/etc/dropbear/dropbear\_dss\_host\_key
/etc/dropbear (folder)
/etc/hackinit.sh
/etc/init.d/dropbear.sh
/etc/init.d (folder)
/usr/bin/dropbear

7.) Restart iPhone again.

## Originally Found Here ##

Written by Tinman
http://hackint0sh.org/forum/showpost.php?p=84868&postcount=6