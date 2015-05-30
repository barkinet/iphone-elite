# Introduction #
The tool _sendmodem_ is a tool that directly accesses the modem/baseband through /dev/tty.debug without the need to disable the commcenter.


# Details #

Normally, the modem/baseband is accessed through minicom which issues commands to /dev/tty.baseband. In order for minicom to work, the commcenter first needs to be disabled, then your can run minicom, and after you are done with minicom you again need to enable the commcenter.
```
# cd /usr/bin
# launchctl unload -w /System/Library/LaunchDaemons/com.apple.CommCenter.plist
# minicom
# launchctl load -w /System/Library/LaunchDaemons/com.apple.CommCenter.plist
```

With _sendmodem_ commands to the modem/baseband can be directly executed from the commandline.

In order to use _sendmodem_ on your iPhone, do the following:
  1. Upload the file _sendmodem_ to your iPhone in /usr/bin (use your favorite program for this e.g. winscp, ibrickr, etc.)
  1. On your iPhone execute the following commands (e.g. from the terminal window or through ssh):
```
  # cd /usr/bin
  # chmod +x sendmodem
```

To try _sendmodem_ out, you can execute on your iPhone the following commands:
  * To see the help of _sendmodem:
```
  # sendmodem
```
> which will respond with:
```
  usage: sendmodem <at command>
  examples:       sendmodem "AT+XSIMSTATE=1"
                  sendmodem "AT+XGENDATA"
                  sendmodem "AT+CLCK=\"SC\",2"
```
  * To query the baseband:
```
  # sendmodem "AT+XGENDATA"
```
> which will respond with (depending on your firmware and bootloader version):
```
  Sending command to modem: AT
  --.+
  AT
  OK
  Sending command to modem: AT+XGENDATA
  -..+
  AT+XGENDATA
  +XGENDATA: "
  ",
             "DEV_ICE_MODEM_04.01.13_G",
  "EEP_VERSION:207",
  "EEP_REVISION:8",
  "BOOTLOADER_VERSION:3.9_M3S2",1,0
 
  OK
```_

## Notes ##

  1. _sendmodem_ always initiates communication to the modem/baseband with the "AT" command and waits till it receives the response "OK". While waiting it will write "-" to your screen. Be patient, the amount of dashes can very from a few to many.
  1. The original modem state is saved before any commands are send and restored after executing your commands.
  1. The original code was obtained from the _iPhone-sms_ application source code, available from http://code.google.com/p/iphone-sms and was slightly modified in its screen output format
  1. Make sure not to send any commands to the modem of which you don't know what its effect is (you don't want a brick right?)

# Another example #

Have you ever wondered how to make iTunes display your phone number if it is not showing? Here are the commands to enter:
  * We first check what addressbook-mode the SIM card is in:
```
  # sendmodem "AT+CPBS?"
```
> Usually it will respond with "SM" (general addressbook). If the response is not "ON", we change the addressbook to "ON" (SIM card owner specific numbers):
```
  # sendmodem "AT+CPBS=\"ON\""
```
  * Next we check what is currently in the addressbook item 1 (this is the location for your own phone number)
```
  # sendmodem "AT+CPBR=1"
```
  * If you are fine with overwriting the current content (it is probably empty), you can (re)write the first entry with your own phone number (indicated by _xxx_ below, e.g. "+18885559999") and a description (indicated by _yyy_ below, e.g. "your name here"). Btw, note the double _,,_ (this is not a mistake) and that the "+" sign in the number will disappear upon query.
```
  # sendmodem "AT+CPBW=1,\"xxx\",,\"yyy\""
```
  * Next you can query the results with the command below. It will respond back with what you entered plus the entry type, either 129 or 145. (129 fits a phone number with a national pattern, and 145 one with an international pattern):
```
  # sendmodem "AT+CPBR=1"
```
  * And if you are happy with what you see, you can issue the following command (this is how iTunes queries the "Owner Number"):
```
  # sendmodem "AT+CNUM"
```
  * Now put the SIM back to its original addressbook with the response answer from the first step (AT+CPBS?). In this example we assume it responded with "SM":
```
  # sendmodem "AT+CPBS=\"SM\""
```
  * And verify that it worked (it should respond with "SM"):
```
  # sendmodem "AT+CPBS?"
```
  * Now reboot your phone and after it is done rebooting, start up iTunes.
```
  # reboot
```

And voila, iTunes should be displaying your phone number under the summary tab and also if you scroll to the top of your contact list on the phone, you should now see your phonenumber.