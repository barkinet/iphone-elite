# Instructions #

Executing the commands below will remove your application space limitation so that you're only limited by the device's drive.. Your apps will share the same space available to your media files. The approach is to create a symbolic link to the Application folder and move the actual folder to the media partition.

**NOTE:** This guide assumes you have jailbreaked your iPod touch (or iPhone) and installed the necessary Unix binaries, either manually or by using some sort of semi-automated/automated jailbreak method.

**WARNING:** Although this seems to work just as intended, it's not been tested for more than a number of hours, meaning that it's entirely possible it could somehow break in the future. Therefore, only do this at your own risk!

**1)** SSH into your iPod touch (or iPhone) using the SSH client of your choice; I recommend putty, which you can download [here](http://www.chiark.greenend.org.uk/~sgtatham/putty/).

**2)** Move your applications folder to the /private/var folder by typing the following:
```
mv /Applications /private/var/Applications
```

**3)** Change directory to / by typing
```
cd /
```

**4)** Create a symbolic link to the Applications folder's new location:
```
ln -s /private/var/Applications Applications
```

**5)** Type the following to get a directory list of /, so we can make sure the symbolic link was created correctly:
```
ls -la
```

In the list that comes up, you should have an entry similar to this one:
```
lrwxr-xr-x 1 root admin 25 Oct 12 22:31 Applications -> /private/var/Applications
```

**6)** Alright, seems like it worked.. Now all you have to do is reboot your iPod touch (or iPhone)! Do that now.

**7)** To further make sure it's working correctly for you, enter the Installer app and check the available space at the bottom. It should be higher than it was previously, as you've moved away all space-intensive apps. Try installing an app larger than 1MB (MobileChat for example) to see that the amount of available space stays the same.

**8)** Done! Install away!

Guide originally found [here](http://www.hackint0sh.org/forum/showpost.php?p=85073&postcount=1).