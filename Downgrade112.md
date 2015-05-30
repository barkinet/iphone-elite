# Instructions #

GO TO DFU MODE
(power+home for 10 seconds then home for 10 seconds)
Phone will remain completely BLACK.
If not repeat previous step.

Then select 1.0.2 firmware for restoring.

At the end you will have an error.

THAT's NORMAL!

Now get IPHUC and issue:

**cmd setenv auto-boot true**

**cmd saveenv**

**cmd fsboot**

You just downgraded!

You also can, after jailbreaking 1.0.2, issue these commands:

**cd /var/root/Media**

**mknod disk c 14 1**

**chmod 666 disk**

and then UPDATE to 1.1.2 directly.

## NOTE from mike.maccana ##

ERRORS AT THE START OF THE RESTORE

For people who get Error (1) at the stat of the downgrade:

When you hold down the buttons to put the iPhone into restore mode, you should gte a

black screen, not the itunes symbol and the cable connector.

If you get the itunes symbol, you're holding the buttons down wrong. Hold them both down,

count for ten seconds, let go of the power, hold for ten more seconds.

The iPhone will remain black but iTunes will detect a phone in restore mode.

WHAT SOFTWARE DO I USE? HOW DO I RUN THE COMMANDS?

Google iPhuc. The package you want is the one 'without external dependencies'. Open a

terminal, run ./iphuc-universal. Enter the commands there.