#How to fix crashing MobilePhone.app and MobileSMS.app on firmware version 1.1.2

## Introduction ##

After updating to 1.1.2, replacing lockdownd with proper one to get activation and signal, you might end up in having everything right except when you try to dial or send an SMS, the application crashes. This page explains why it crashes and how to fix it.


## Crash of MobileSMS.app and MobilePhone.app ##

When dialing a number or sending an SMS and entering its number,  both applications
did crash in the same manner at the same locations in the code. The reason is it tries
to format a dial-string to look nice and crashes because there is no format string found.

Here's my interpretation on what happens in Apple's code does:

1. A SIM is inserted

2. MNC/MCC are read out

3. Symlinks are being created

> `/private/var/root/Libraries/Preferences/com.apple.carrier.plist`
> `/private/var/root/Libraries/Preferences/com.apple.operator.plist`

will point to

> `/System/Library/Frameworks/CoreTelephony.framework/Support/`**carriers-name**`.plist`

or if the carrier is not found to

> `/System/Library/Frameworks/CoreTelephony.framework/Support/UnknownCarrier.plist`


There is also a symlink from the MNC/MCC codes to the carrier in the same directory.

> `/System/Library/Frameworks/CoreTelephony.framework/Support/`

&lt;mnc&gt;



&lt;mcc&gt;



also points to

> `/System/Library/Frameworks/CoreTelephony.framework/Support/`**carriers-name**`.plist`


Now if you dial, the number formatting string is being pulled out by looking up MCC/MNC in

> `/System/Library/Frameworks/UIKit.framework/PhoneFormats/UIMobileCountryCodes.plist`

to find the country code. Then the country code is being looked up in

> `/System/Library/Frameworks/UIKit.framework/PhoneFormats/UIPhoneFormats.plist`


for the string formatting rules.

When `UnknownCarrier.plist` is being used because of unknown MNC/MCC,
the lookup in `UIMobileCountryCodes.plist` seems to fail and no country is found
and then the lookup in `UIPhoneFormats.plist` returns NULL which then makes the
formatting routine crash.

Fix: create your own **MCCMNC**`.plist` file (see http://en.wikipedia.org/wiki/Mobile_Network_Code#International for known MNC/MCC values)

So for example for a Swisscom Mobile file you create

> `SwisscomMobile.plist`

edit it to include the right APN's etc.
and then create a symlink `22801` to point to `SwisscomMobile.plist`

All this in directory /System/Library/Frameworks/CoreTelephony.framework/Support/

Then swap Simcard with other SIM (your unused AT&T SIM for example) and back. This will recreate the symlinks in `/private/var/root/Library/Preferences`. Now the code should remember 22801 as code and the mapping should properly work.