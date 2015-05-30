# Instructions #


**Step 1: Bringing phone in recovery mode**

- With the iPhone turned on, hold down the POWER and HOME buttons simultaneously for ten seconds (until the screen goes dark). Then release POWER while CONTINUING TO HOLD DOWN HOME for another 15 seconds.

At this stage, the iPhone WILL APPEAR TO BE POWERED DOWN, but it is actually in a special type of recovery mode allowing software downgrades. If you see the “Connect to iTunes” icon, you’ve placed the phone into the wrong recovery mode, and will need to try again.



![http://www.glaciuz.com/iphone/images/step2.png](http://www.glaciuz.com/iphone/images/step2.png)



**Step 2: Downgrade the iPhone**

- Once iTunes is up, you can now release HOME. You will be prompted to restore your iPhone (if you are not, try step 2 again). Hold down the OPTION key (or SHIFT if you’re running Windows) and click RESTORE.

You will then be prompted with a file selection window allowing you to select a firmware file. Select the 'iPhone1,1\_1.0.2\_1C28\_Restore.ipsw’ (can be found [here](http://appldnld.apple.com.edgesuite.net/content.info.apple.com/iPhone/061-3823.20070821.vormd/iPhone1,1_1.0.2_1C28_Restore.ipsw), rename the extension from .zip to .ipsw). Ignore any errors itunes will give you.




**Step 3: Using AppInstaller to Install 1.0.2.**

- Use AppInstall.exe to install 1.0.2 on your iPhone. It might not work first time but just do it again it will work on the second time. AppInstall.exe can be found [here](http://www.glaciuz.com/iphone/files/AppTappInstaller.exe). Make sure iTunes is still running but not accessing the iPhone.

![http://www.glaciuz.com/iphone/images/step3.png](http://www.glaciuz.com/iphone/images/step3.png)




**Step 4: Cancelling the activation screen**

- Use the Application named PACAY. Can be found [here](http://www.glaciuz.com/iphone/files/PACAY.rar). Make sure you reboot the phone by holding the power button and then sliding the slide bar. Reboot your iPhone. It will now be booting 1.0.2 without the activation screen. You will now need to downgrade your baseband since there is no anySIM working on BB4. We will downgrade it later to BB3.



![http://www.glaciuz.com/iphone/images/step4.png](http://www.glaciuz.com/iphone/images/step4.png)



**Step 5: Connecting to the Wi-Fi network.**

- My router was mac filtered so I had to find out the mac address of the iPhone Wi-Fi device. You can find it in Settings -> General -> About.

Go to Settings -> Wi-Fi and make your connection working. I assume here you know what you are doing.




**Step 6: Installing applications on the iPhone**

- From the spring board, click on Installer. Go to the Sources section and install Community Sources. From the System section , install BSD Subsystem, Term-vt100 and also install OpenSSH. This may take a while. Now insall ncftp from the Network section.




**Step 7: Connect via SSH to your iPhone**

- You can find your iPhone IP Address by lunching Term-vt100 on your spring board and type # ifconfig en0 . It will say something like "inet 192.168.1.100". Open putty on your desktop (if you don't have it look for putty on google, it's a free software) and connect to your iPhone IP address. The default username and password is root / dottie. It will take more than 30 seconds the first time since the iPhone as to generate rsa keys.




**Step 8: Downgrading the baseband**

- You will need to take some files and put it on your device. I did put my files on a ftp server and downloaded them with ncftp. The tar file of those files can be found [here](http://www.glaciuz.com/iphone/files/archive.tar.gz).

Assuming you have those files in your home directory, I will continue as if.

Shell commands.

# cd
#

# cp ICE03.14.08\_G.eep**/usr/bin/
#**

# chmod 0777 ieraser; cp ieraser /usr/bin
#

Kill the CommCenter process
# launchctl remove com.apple.CommCenter
#

# chmod 0777 bbupdater ; cp bbupdater /usr/bin
# rehash

# bbupdater -v
Resetting target...
pinging the baseband...
issuing +xgendata...
firmware: DEV\_ICE\_MODEM\_04.01.13\_G
eep version: EEP\_VERSION:207
eep revision: EEP\_REVISION:8
bootloader: BOOTLOADER\_VERSION:3.9\_M3S2
Done
#

You can now see that you are running the 4.01.13G BB firmware. time to downgrade it to 3.

# ieraser
Baseband reset.
Get FlashID Request
.........
(a lot of junk)
.........
Hopefully the main flash was erased, wait for the next step...
#

# bbupdater -v
It should show you that you dont have any firmware installed.
baseband unresponsive to pinging
#

# bbupdater -e ICE03.14.08\_G.eep -f ICE03.14.08\_G.fls
Preparing to flash using /dev/tty.baseband at 750000 baud
Please reset target
......
(a lot of junk)
......
pinging the baseband...
issuing +cpwroff...
Done
#

# bbupdater -v
Resetting target...
pinging the baseband...
issuing +xgendata...
firmware: DEV\_ICE\_MODEM\_03.14.08\_G
eep version: EEP\_VERSION:207
eep revision: EEP\_REVISION:7
bootloader: BOOTLOADER\_VERSION:3.9\_M3S2
Done
#

Got it! Now back to 3.14.08\_G ! Time to install anySIM!




**Step 9: Installing anySIM**

- From the files downloaded before. Let's install anySIM.

Shell commands.

# cd
#

# chmod -R 0777 anySIM.app
#

# cp -R anySIM.app /Applications
#

Now reboot your iPhone. At this point it makes my computer freezes and I didn't figure out why.... (shame to myself)

Now run the Application anySIM on your spring board and follow the instructions. This process will take about 6-7 mins.

Reconnect to the SSH and delete the anySIM application.

# rm -rf /Applications/anySIM.app
#

Put your sim in and reboot your phone again and VOILA!

**Page taken from this site:
http://www.glaciuz.com/iphone/**