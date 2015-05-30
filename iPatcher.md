# Details #

Disclaimer:
iPatcher comes without any warranty, expressed or implied.  iPatcher may void
the warranty on your iPhone, and may cause damage to the device.

It is not known that it will cause such damage, but your use of the program is at your
own risk.

What is iPatcher?
iPatcher is a program that runs on MacOS X, Win32-Cygwin, and the iPhone.

It will automatically patch lockdownd to make the iPhone appear activated,
regardless of the actual activation state.  It has been designed to patch the
following firmware versions:
> -1.1.2
> -1.1.1
> -1.0.2
> -1.0.1
> -1.0.0
> -As many alternate lockdownd patches as we could find.

Why Use iPatcher?
Here are a few reasons you might want to use iPatcher:
  1. ) All code inside lockdownd that disabled the baseband has been disabled

> 2.) All modifications to lockdownd have been documented

> 3.) Distributing copies of lockdownd doesn't feel right

> 4.) iPatcher lockdownds shouldn't interfere with the use of iASign

> 5.) The lockdownd distributed with 1.1.2 allows Apple to give you a baseband
> > unlock code.  iPatcher lockdownds will allow you to apply these unlock
> > codes.

> 6.) Standardized lockdownd modifications will lead to easier solutions to
> > problems.

> 7.) Ipatcher lockdownds shouldn't care about your baseband version.

How to use it on windows32:
> -You must have cygwin installed.
> -From cygwin:
> > ./ipatcher -l <path to lockdownd file>

> -From the command prompt:
> > ipatcher -l <path to lockdownd file>

How to use it on MacOSX:

> -From a terminal window:
> > ./ipatcher -l <patch to lockdownd file>

How to use it on an iPhone:

> -From a command prompt, to patch the current lockdownd:
> > ./ipatcher -a

Known Issues with iPatcher:

iPatcher probably doesn't recognize all the modified lockdownd versions, if this
is the case then you must revert to the stock lockdownd version in order for
iPatcher to function, or wait for the programmers to find the specific version
of lockdownd you're using and generate patch data for it.

Sometimes, brick mode has been enabled on an iPhone by a lockdownd that supports
brick mode.  When this happens the user will experience no signal bars on their
iPhone even though the baseband software and sim combination should work.
iPatcher lockdownds are only capable of disabling brick mode.  However, if a bad
lockdownd enables brick mode and doesn't keep good records of having done so,
then iPatcher lockdownds will not disable brick mode.  This can be remedied by
using the iPhone-elite's brick tool.

If there are any issues, or successes please comment on the iPhone-elite wiki so
that we can know that it's working or maybe work on the problem that you're
experiencing.

The following people have made this possible:

> -79b1caf2c95695e0af0e6216216eec54
> -BlaCkBirD
> -Zibri
> -b1llyb0y
> -kiwi66
> -rdh
