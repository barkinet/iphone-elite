# Details #

Overview:

The lockdownd binary is	responsible for	several	tasks including: activation,
unlocking FairPlay certificates, delegating communications to other services,
and ensuring Apple's security logic is implemented to prevent people from using
their iPhones in an unintended manner. This binary is the one that is either
fooled or modified to allow activation on an iPhone that is not	considered to be
proper by Apple.


Activation:

The iPhone has been designed so	that it	must be	properly activated before it may
be used. lockdownd is responsible for maintaining and verifying the activation
state.	The current 1.1.2 lockdownd has	the following activation states:


-Unactivated:

This state prevents the	GUI on the iPhone from being accessed.	In
addition, it causes the	modem to have no signal	and only allows	iTunes
to attempt activation.


-Activated:

This state allows full access to the GUI and the modem.	 iTunes	allows			full functionality


-MissingSim:

This state causes iTunes on Windows to display a message box informing
the user that the iPhone may not be used with iTunes until a SIM card
is installed. Full access to the GUI is allowed.


-SoftActivation:

This state is used by AppleCare, and does not specify an ICCID.	 It
allows full access to the GUI and full access from iTunes. It may be
used with any SIM card.


-MismatchedICCID:

This state is entered when the current SIM card's ICCID	does not match
the Activation Token. It allows full access to the GUI but the modem
is disabled.


-FactoryActivated:

This state allows full access to the GUI and the modem.	 It is made for
quality	control	personell to inspect the functionality of the phone.


-MismatchedIMEI:

This state is never entered.


Certain	versions of iPHUC allow	an individual to retrieve the current Activation
state.	In order to do this, connect an	iPhone in standard mode	and issue the
command	"readvalue ActivationState". Versions compiled for Windows generally
attempt	to use CFShow to display the activation	state.	This function	is
documented to not work on the Windows OS. However, the	version	of
iphoneinterface	that accompanies iBrickr will correctly	display	the activation
state.

Activation Verification:

The following process outlines the steps that are used to authenticate an
account	token.

1.)An Activation Token is recieved	by the device, or the current
activation state is queried.

2.)The Activation Token's cryptographic signature is	checked.
3.)The Unique Device ID is	retrieved from the data	ark.
4.)The Activation State is	retrieved from the data	ark.

-If the	ActivationState	is not succesfully retrieved, then set
the new ActivationState toUnactivated, and set bEnableBrickState to
true, and set aPreviousActivationState to false.  Then	go to step 22.

5.)The	Account	Token is retrieved from	the data given to the function.

-If the	Account	Token is not successfully retrieved, then set the new
Activation State to Unactivated and set register no unlock code.
Finally, go to	step 21.

6.) Attempt to create a	property list from the XML data.

-If this fails,	then set new Activation	State to Unactivated and register
no unlock code.	Finally, go to step 21.

7.) Attempt to get the Unique Device ID	from the Account Token.

-If this fails,	then set new Activation	State to Unactivated, set the
Unlock Code to no Unlock Code, and go to step 21.

8.) Check to see if the	Unique Device IDs Match

-Go to step 9 if they	do not match.

9.) Check to see if lockdownd is running on an iPhone.	If it isn't then set
then new Activation State to Activated and go to Step	21.

10.) Check to see if the Activation Token has a FactoryActivation key set to
true or	if the Account Token was validated by a	Factory	Certificate.
If either is true, then set the new Activation State to FactoryActivated, set
the Unlock Code	to none, and go	to step	21.

11.) Get the IntegratedCircuitCardIdentity value from the data	ark.

-If this fails,	then set the new Activation State to MissingSIM	and go to
step 14.

12.) Get the IntegratedCircuitCardIdentity value from the Activation Token.

-If this fails, then set the new Activation State to SoftActivation.
Finally, go to Step 14.

13.) Compare the IntegratedCircuitCardIdentity values from steps 11 and 12.
If they match, then set the new Activation State	to Activated. If they do
not, then set the new Activation State to MismatchedICCID.

14.) Look up the InternationalMobileSubscriberIdentity from the data ark.

-If this fails,	then go	to step	17.

15.) Look up the InternationalMobileSubscriberIdentity from the Account Token.

-If this fails,	then go	to step	17.

16.) Compare the InternationalMobileSubscriberIdentity from steps 14 and 15.

-If they do not match set the new Activation State to MismatchedICCID.

17.) Check to see if the Account Token contains an UnlockCode, if it does
then register the unlock code and set new Activation State to Unlocked.

18.) Retrieve the InternationalMobileEquipmentIdentity from the data ark.

-On failure, go	to step	21.


19.) Retrieve the InternationalMobileEquipmentIdentity from the AccountToken.

-On failure, go	to step	21.


20.) Compare the InternationalMobileEquipmentIdentity from steps 18 and 19.

-If they do not	match, then set	the new	Activation State to Unactivated.

21.) If the new Activation State is Unactivated or MismatchedICCID, then
set bEnableBrickState to true, otherwise set it	to false.

22.) Check to see if lockdownd is running on an iPhone.	If it isn't then go to
step 25.

23.) Get the current BrickState from the data ark.

24.) If the current BrickState is different from bEnableBrickState, then set
the new	BrickState to bEnableBrickState	in the data ark.

25.) Check to see if aPreviousActivationState is true.

-If not, then goto step	30:

26.) Check to see if the new Activation State is unlocked.

-If not, then goto step	28:

27.) Attempt to unlock	the modem with the supplied lock code and set the
new ActivationState to Activated.

28.) Set the Activation State to the new Activation State in the data ark and
in the lockdown	cache.

29.) If bEnableBrickState is true, then enable the brick state in the
CoreTelephonyServer

30.) Finished.



Apple Security Logic:

Another	task lockdownd is concerned with is ensuring that the iPhone is	in a
state that Apple deems proper. One of these checks ensures that the modem's
firmware version is consistent with the	version	that is	distributed alongside
the iPhone software. If these versions	differ,	then the modem is put into brick
mode.  Brick mode causes the modem to not register to any providers and	it will
display	no signal within the GUI.

Additionally, there are	some references	inside lockdownd to voiding the
warranty.  It appears that iTunes may send messages to the iPhone telling it
that its warranty is invalid.  It is an	assumption that	this is	used because it
is hard	for Apple to determine if an iPhone is outside of its warranty when it
has its	software completely reloaded.


Prior Work by Others:

dvdjon created an activation method that worked	with older versions of the
iPhone firmware. dvdjon's method patched iTunes to use HTTP instead of	HTTPS
for the	activation server, and redirected activation requests to the
PhoneActivationServer.	The PhoneActivationServer would	then send a valid
Account	Token to the iPhone.	However, the Account Token was for a different
IMEI, ICCID, and DeviceID. This method	left the phone in the MismatchedICCID
state, but allowed access to the user interface.

An activation method called iASign was released	by the iPhone dev team.	 This
activation method would	change the certificates	on the iPhone and would	send an
Activation Token that was signed by the	new certificates.  This	allowed	fully
compliant tokens, however, it required a lengthy process to activate the iPhone.

Several	patches	have been distributed that modify the lockdownd	binary directly.
These patches generally	modify the routine that	checks the activation state of
the iPhone and cause it	to always believe it has been Factory Activated.
The way	they do	this is	they change step 4 to read:

Set the new ActivationState to FactoryActivated, and set
bEnableBrickState to false, and set aPreviousActivationState to false.

Then	go to	step 22.


Improvements Upon Prior	Work:

dvdjon's activation allows use of the iPhone GUI but it	does not allow use of	the
phone.	iASign allows use of both the GUI and the phone, but it	does not allow
changing the SIM card without generating a new Account Token.  As well,	neither
of the aforementioned activations will allow use of the	iPhone with iTunes if a
SIM card is not	installed and they leave the security logic intact.

The current patches to lockdownd circumvent the	previous issues, however they
don't bypass Apple's security logic. As well, they will not allow a person who
has installed these patches to unlock their phones via an Apple	supplied unlock
code without reinstalling lockdownd. Finally, they are incompatible with
alternate or additional	unlock methods such as iASign.


Current	Work:

The patch that has been	developed to modify the	Verification Process so	that it
never enables brick mode, and all references to	Unactivated, MissingSIM,
MismatchedICCID, and MismatchedIMEI have been changed to FactoryActivated. This
allows people who have valid Account Tokens to use the iPhone in the state that
they have activated, allows people who receive unlock codes from Apple to apply
them without reinstalling lockdownd, and allows	people to use iASign in	addition
to this	patching method.

In addition, parts of Apple's security logic have been circumvented. All parts
of lockdownd that attempt to void the iPhone's warranty	have been removed. Also,
lockdownd has been modified to allow all baseband versions without bricking the
phone. This allows experimentation with	different modem	firmwares without having
to modify lockdownd.


Changes	made by	the Current Work:

The following shows the	location and purpose of	the modifications the patcher
makes to the stock lockdownd binary:

-
-
-



Lockdownd 1.0.0	Modifications:

Offset		Original Byte		New	Byte	Reason

0x8CF8		0x01                       0x00	Change enable brick mode to disable.

0x90A4		0x01			   0x00	Change enable brick mode to disable.

0x90A8		0x3C                       0x68	Change Unactivated to FactoryActivated

0x9178		0x84                       0x98	Change MismatchedIMEI toFactoryActivated

0x91B8		0x01	                   0x00	Change enable brick mode to disable.

0x91D8		0x2C	                   0x38	Change MismatchedICCID toFactoryActivated

0x91E0		0x28	                   0x30	Change MissingSIM to FactoryActivated

0x9258		0x01	                   0x00	Change enable brick mode to disable.

-
-
-

Lockdownd 1.0.1	Modifications:

Offset		Original Byte		New	Byte	Reason

0x9158		0x01	                0x00	Change enable brick mode to disable.

0x94C4		0x01			0x00	Change enable brick mode to disable.

0x94C8		0x3C			0x68	Change Unactivated to FactoryActivated

0x9598		0x84			0x98	Change MismatchedIMEI to FactoryActivated

0x95D8		0x01			0x00	Change enable brick mode to disable.

0x95F8		0x2C			0x38	Change MismatchedICCID to FactoryActivated

0x9600		0x28			0x30	Change MissingSIM to FactoryActivated

0x9678		0x01			0x00	Change enable brick mode to disable.


-
-
-


Lockdownd 1.0.2	Modifications

Offset		Original Byte		New	Byte	Reason

0x9184		0x01			0x00	Change enable brick mode to disable.

0x94F0		0x01			0x00    Change enable brick mode to disable.

0x94F4		0x3C			0x68	Change Unactivated to FactoryActivated

0x95C4		0x84			0x98	Change MismatchedIMEI to FactoryActivated

0x9604		0x01			0x00	Change enable brick mode to disable.

0x9624		0x2C			0x38	Change MismatchedICCID to FactoryActivated

0x962C		0x28			0x30	Change MissingSIM to FactoryActivated

0x96A4		0x01			0x00	Change enable brick mode to disable.


-
-
-


Lockdownd 1.1.1	Modifications:

Offset 	        Original Byte		New	Byte	Reason

0x482F        	0x1A     		0xEA	Changed	to ignore baseband version.

0xAF5C		0x01			0x00	Change enable brick mode to disable.

0xB814		0x24			0x54	Change Unactivated to FactoryActivated

0xB818		0x01			0x00	Change enable brick mode to disable.

0xB838		0x00			0x30	Change Unactivated to FactoryActivated

0xB858		0xE0 0x14		0x10 0x15 Change Unactivated to	FactoryActivated

0xB884		0xB4			0xE4	Change Unactivated to FactoryActivated

0xB958		0x00			0x10	Change MismatchedICCID to FactoryActivated

0xB970		0xEC			0xF8	Change MissingSIM to FactoryActivated

0xB9E0		0x58			0x88	Change Unactivated to FactoryActivated

0xBA58		0x01			0x00	Change enable brick mode to disable.


-
-
-



Lockdownd 1.1.2	Modifications:

Offset		Original Byte		New	Byte	Reason

0x4B3B		0x1A			0xEA	Changed	to ignore baseband version.

0x79FC		0xD7 0xFF		0x00 00	Disallows enabling of Voided Warranty.

0x79FE		0xFF 0x1A		0xA0 0xE1 Part of  patch at 0x79FC

0x7E0B		0x0A			0xEA	Disallows enabling of Voided Warranty.

0xAC73		0x0A			0xEA	Disallows enabling of Voided Warranty.

0xBC40		0x01			0x00	Change enable brick mode to disable.

0xC5CC		0x01			0x00	Change enable brick mode to disable.

0xC5D4		0x88			0xEC	Change Unactivated to FactoryActivated

0xC614		0x48			0xAC 	Change Unactivated to FactoryActivated

0xC640		0x1C			0x80	Change Unactivated to FactoryActivated

0xC6F0		0x90			0xD0	Change MissingSIM to FactoryActivated

0xC74C		0x44			0x74    Change MismatchedICCID to FactoryActivated

0xC7DC		0xB4			0xE4	Change MismatchedICCID to FactoryActivated

0xC8AC		0xB0 0x33		0x14 0x34 Change Unactivated to	FactoryActivated

0xC904		0x01			0x00	Change enable brick mode to disable.


-
-
-


Issues with the	Current	Work

If brick mode has been enabled on the iPhone, usually lockdownd	stores a record
that it	has been enabled.  However, it has been	observed that a	different
lockdownd can enable brick mode	without	storing	this record. When this happens,
the current lockdownd is unaware of the	brick mode state and won't disable brick
mode.  GUI access will still be	allowed	to the iPhone but the phone will appear
to get no signal.  This	is a limitation	in the original	design of the software.
A piece	of software is available called	bricktool. This will allow an
individual to enable or	disable	brick mode outside of lockdownd	and fixes this
issue.

The patcher source should be extended and made slightly	more robust. In
particular, the	patcher	should be capable of patching all binaries on the iPhone
that might require patching. This includes SpringBoard, and anything else that
is generally patched on	a modified iPhone. Another thing the code should do is
enable the user	to revert their	binaries back to factory state.	 In the	future,
a more robust hashing algorithm	should be implemented, such as MD5 so that files
may be identified by their hash	alone. As well, any issues that pop up with the
use of this tool should	be taken care of in the	software if it makes sense.

The following people have made this possible:
> -79b1caf2c95695e0af0e6216216eec54
> -BlaCkBirD
> -Zibri
> -b1llyb0y
> -kiwi66
> -rdh