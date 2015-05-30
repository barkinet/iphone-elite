# Environment #

iTunes (Ver 7.4.1.2), OSX, iPhone 1.1.1 (Virgin or Bricked)

# Software Needed #

iNdependence 1.2.1a: http://independence.googlecode.com/files/iNdependence_v1.2.1a_bin.dmg

Cyberduck:
http://cyberduck.ch/Cyberduck-2.8.dmg

Original 1.0.2 Firmware file:
Plenty of places to get it

# Downgrade Firmware #

1. Open iTunes and then connect your iPhone.

2. Hold for exactly 10 seconds, then let go of just the power button.(If you look very closely, after about 7 seconds the screen will power down and pixelate, this is the lead in to the hidden restore state.) After about another 15 Seconds of holding down the Home button, iTunes will find your iPhone and say it needs to restore. The restore screen will not come up, don’t worry this is normal.

3. Hold the Option (Alt) key and click the Restore button and go to the directory where you stored your 1.0.2 firmware. Your iPhone will start the restore process. At the end of the process you will see error “1013”, this is normal, don’t run the restore again. Quit iTunes. Your phone will remain on restore mode.

4. To take the phone out of restore mode, open iNdependence (make sure that iTunes is closed and that you have killed the ituneshelper process) let it try to connect to the iPhone for a couple of seconds. iNdependence will take the phone out of restore mode.

5. Now you have the Activate screen on you iPhone. Open iNdependence again and click on Activate, browse to the same firmware file that you have been using, the iPhone will be activated.

6. Now on the Jailbreak section, click Jailbreak (if it fails to enter restore mode just quit and open iNdependence).

7.Install SSH by moving to the SSH tab and clicking Install SSH/SFTP/SCP. Reboot you iPhone twice as instructed.

Now you have a 1.0.2 iPhone with a 1.1.1 baseband, you can use all the features but cannot make calls, SMS and other phone related functions.

# Baseband Downgrade #

1. Connect your iPhone to your wireless network, go to Settings, tap Wi-Fi, then tap the “>” next to the network you are connected to. You need that IP to SSH to your phone.

2. Open Cyberduck and click on Open Connection. Select SFTP from the dropdown for protocol. Type the iPhone’s IP from step 1. Enter “root” for Username and “dottie” for Password. If it doesn’t connect right away, on your iPhone go to Safari and just navigate to any page.

3. Copy ICE03.14.08\_G.eep, ICE03.14.08\_G.fls, bbupdater, ieraser and secpack to /usr/bin.

4. On your Mac open a Terminal session (Applications, Utilities, Terminal), and type “ssh –l root IPHONESIP” and hit enter. Password is “dottie”.

5. You are in the root directory of your iPhone. Type "launchctl remove com.apple.CommCenter" (capitals are required)

6. Type “cd /usr/bin” hit enter. Do a chmod on ieraser and bbupdater, “chmod 775 ieraser” and “chmod 775 bbupdater”

7. Run “bbupdater –v”, the result will be a baseband starting with 4. This is normal, you will downgrade it in the next couple of steps.

8. Type “ieraser”, this command will erase your 1.1.1 baseband completely (if you get access denied check step 6). Make sure that the “secpack” file is in the same directory as ieraser.

9. Run “bbupdater –v”. You will have no baseband, this is a normal part of the process.

10. Run “bbupdater -e ICE03.14.08\_G.eep -f ICE03.14.08\_G.fls”. This will load your 3X baseband.

11. Run “bbupdater –v” and it should output version 3x. You have completed the baseband downgrade process.

# Unlock #

1. Back on Cyberduck, copy anySIM.app to /Applications, restart your iPhone so the Springboard refreshes.

2. On your iPhone go to Settings, General and set the Autolock to “Never”.

3. Tap the anySIM application on the Springboard and follow the instructions. Now you have a fully unlocked iPhone version 1.0.2 with all the phone functions.

4. Copy the “lockdownd” file to /usr/libexec. When prompted to replace click “Continue”

5. In Cyberduck go back to your iPhone and delete the anySIM app from /Applications.

You now have a fully unlocked iPhone that will work with any SIM in the world (even if you change them)

Guide contributed by OBCR