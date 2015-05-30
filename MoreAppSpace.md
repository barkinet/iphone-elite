# Introduction #

As all of you might be aware by now, after installing quite a few applications, installer.app starts complaining how you're running out of space.. the fix is simple, all you do is move applications to a bigger disk (/dev/disk0s2) and point a symlink to it..


# The Fix #

connect to your iPhone via SSH and execute the following command:
```
# cat /etc/fstab
/dev/disk0s1 / hfs rw 0 1
/dev/disk0s2 /private/var hfs rw 0 2
```
note the rw in: /dev/disk0s2 /private/var hfs **rw** 0 2

If your /dev/disk0s2 line looks like the following
```
# cat /etc/fstab
/dev/disk0s1 / hfs rw 0 1
/dev/disk0s2 /private/var hfs r 0 2
```
or like the following
```
# cat /etc/fstab
/dev/disk0s1 / hfs rw 0 1
/dev/disk0s2 /private/var hfs rw,noexec 0 2
```

then you will have to download /etc/fstab via SCP and change it to look like the following:
```
/dev/disk0s1 / hfs rw 0 1
/dev/disk0s2 /private/var hfs rw 0 2
```

once that's settled..
```
# mkdir /private/var/Applications
# cp -Rp /Applications/* /private/var/Applications 
# mv /Applications /Applications.backup && ln -s /private/var/Applications/ /Applications
# reboot
```

once your iphone has rebooted, the problem should no longer persist.