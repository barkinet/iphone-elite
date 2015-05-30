# Details #

What are the iPhone-elite lockdownd tools?

This is a collection of iphone-compiled tools that allow an individual to easily
install the iPhone-elite patch and ensure that brick mode is disabled.  The
following components are included in the current release:
> -bricktool

> -iPatcher

More information regarding individual tools may be found at the iPhone-elite
wiki.

How to use the iPhone-elite lockdownd tools:

> Prerequisites:
  1. The BSD Subsystem (patricularly tar and gzip)
  1. A way to copy files to your phone
  1. A way to run commands in a terminal on your phone

Steps:
  1. Copy ipe-lockdownd-tools.tgz to the root directory ( / ) on your
iPhone.
  * In a terminal, type the following lines:
```
				cd /
				cp /usr/libexec/lockdownd /usr/libexec/lockdownd.bak
				tar -xvzf ipe-lockdownd-tools.tgz
				cd /ipetools
				./ipatcher -a
```
  * Reboot your iPhone.
  * In a terminal, type the following lines:
> > cd /ipetools
> > ./bricktool

If you experience either success or failure, please post a note on the iphone-elite
wiki.  If you can add details such as version of lockdownd patched, iphone firmware
version, and baseband firmware version it would be extremely helpfull.  In the case
of a problem, a description of the problem would be nice as well.

The following people have made this possible:

> -79b1caf2c95695e0af0e6216216eec54
> -BlaCkBirD
> -Zibri
> -b1llyb0y
> -kiwi66
> -rdh