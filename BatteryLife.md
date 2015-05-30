So you've just finished hacking your iPhone and installed every application you could find, but what the heck happened to your battery life? In the process of installing various things, you've likely installed OpenSSH, the tool which allows remote login to your iPhone from a computer. The problem is, SSH requires that a listener called SSHD constantly runs, waiting for a remote login attempt. This in turn drains your battery. The solution is to disable SSHD when you don't need it. Read on for the how-to.

For most seasoned iPhone hackers, disabling SSHD is a piece of cake. Unfortunately, it's not very easy or convenient. Instead, us mortals need a simple tool

In this guide, I'm assuming that you've installed Nullriver's Installer.app and OpenSSH. Here's how it's done:

  * Step 1: From Installer.app, download Community Sources if you haven't already.
  * Step 2: Under Utilities in Installer.app, install Services
  * Step 3: Go back to the home screen and wait for the iPhone to reload the Springboard. Slide to unlock and launch Services.
  * Step 4: From Services, toggle SSH off.

That's it! Your battery life should now return to its original state. If you ever need to use SSH (including SFTP) again, simply use Services again to turn SSH back on.

Enjoy!

Found on iphonealley