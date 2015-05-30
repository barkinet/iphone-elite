# How IPSF works #
_This was originally posted, then deleted, from The iPhone Dev Wiki...as spam._

## A Note on NVRAM ##
People frequently refer to "NVRAM". The baseband doesn't have any NVRAM, and everything(lockstate, IMEI, NCK) is stored encrypted in the NOR at 0xA03FA000-0xA03FC000

## The following things were observed by Zibri and/or others: ##
  * IPSF does not make a permanent patch to baseband like the other SW unlockers do.

  * When their application is run a small chunk of NOR seczone is extracted from the device and uploaded to the IPSF server.  This packet is sent to backoffice.iphonesimfree.com.

  * The packet is sent back after being modified where the LOCK is saved.

  * A custom baseband bootblock is used to flash the 'unlock' patch to NOR seczone.

- Then they restore the baseband with the original firmware again.

## The following is the main thread in the IPSF software ##
```
0000D8F4 MAIN_LOOP                               ; CODE XREF: DO_THINGS+50j
0000D8F4                                         ; DO_THINGS+A0j
0000D8F4                 LDR     R3, [SP,#0xA0+var_C]
0000D8F8                 LDR     R3, [R3,#0x1C]
0000D8FC                 CMP     R3, #3
0000D900                 BEQ     Prepare_UNLOCK
0000D904                 LDR     R3, [SP,#0xA0+var_C]
0000D908                 LDR     R3, [R3,#0x1C]
0000D90C                 CMP     R3, #4
0000D910                 BEQ     Create_Fingerprint
0000D914                 LDR     R3, [SP,#0xA0+var_C]
0000D918                 LDR     R3, [R3,#0x1C]
0000D91C                 CMP     R3, #5
0000D920                 BEQ     Comunicate_Server
0000D924                 LDR     R3, [SP,#0xA0+var_C]
0000D928                 LDR     R3, [R3,#0x1C]
0000D92C                 CMP     R3, #6
0000D930                 BEQ     Unlock___
0000D934                 LDR     R3, [SP,#0xA0+var_C]
0000D938                 LDR     R3, [R3,#0x1C]
0000D93C                 CMP     R3, #7
0000D940                 BEQ     Cleanup___
0000D944                 LDR     R3, [SP,#0xA0+var_C]
0000D948                 LDR     R3, [R3,#0x1C]
0000D94C                 CMP     R3, #8
0000D950                 BNE     Success
0000D954                 MOV     R3, #9
0000D958                 LDR     R2, [SP,#0xA0+var_C]
0000D95C                 STR     R3, [R2,#0x1C]
0000D960                 LDR     R3, [SP,#0xA0+var_C]
0000D964                 LDRB    R3, [R3,#0x26]
0000D968                 CMP     R3, #1
0000D96C                 BNE     check_unlock
0000D970                 LDR     R0, [SP,#0xA0+var_C]
0000D974                 STR     R0, [SP,#0xA0+var_78]
0000D978                 LDR     R3, =do_things_0
0000D97C                 LDR     R1, [R3]
0000D980                 BL      _objc_msgSend
0000D984                 B       exit1
```

## Explaination: ##

| **Prepare\_UNLOCK** is the routine that flashes custom firmware to the baseband.|
|:--------------------------------------------------------------------------------|
| **Create\_Fingerprint** extracts a small chunk of NOR seczone and creates a packet. |
| **Communicate\_Server** sends that packet to backoffice.iphonesimfree.com and receives it back modified. |
| **Unlock** writes the now modified chunk back to NOR seczone.                   |
| **Cleanup** removes the custom baseband firmware and replaces the original.     |

## Token Structure ##

LTOKEN is 0x2820 bytes long
  * 0x10=[IMEI](IMEI.md)
  * 0x10=LTOKEN1.0
  * 0x200=HWID
  * 0x600=[CFIQueryResponse](http://code.google.com/p/iphone-elite/wiki/CFIQueryResponse) (originally this was thought to be a copy of the first 0x600 bytes of the bootloader)
  * 0x2000=seczone(0xA03FA000-0xA03FC000)
  * HWID+bootloader+seczone are encrypted

UTOKEN is 0x2090 bytes long
  * 0x10=UTOKEN1.0
  * 0x2000=seczone(0xA03FA000-0xA03FC000)
  * 0x80=hash
  * only seczone is encrypted

three "crypto" fxns
  * genkey(&key, "Iphonesimfree.com", 0x11);
  * decryptme(&key, &data, length);
  * encryptme(&key, &data, length);

# Theories about how to perform **true** unlock #
## **Terms** ##
  * **NCK:** Unlock code
  * **IMEI:** The _International Mobile Equipment Identity_ is a 15 digit code used to identify the GSM/DCS/PCS phone to the network. [source](http://www.cellular.co.za/cellularterms.htm)
  * **Baseband:** This is the GSM modem along with a few other things.  It has it's own co-processor, memory cache and operating system (Nucleus real-time OS.)
  * **NOR seczone:** This is a protected area of the baseband NOR which contains encrypted data relating to whether the phone is locked or not.  This area is commonly called the "NVRAM" -- which is incorrect.

## **How a phone is normally true unlocked** ##

  * Manufacturer generates a random NCK for IMEI.
  * They do hash(NCK) and store it on the phone.
> hash(NCK) is **one way**.  It is not possible to retrieve the NCK from the hash stored on the phone.
  * User enters an NCK into the phone to unlock it.
  * The phone then does hash(NCK) and compares it to the hash already stored.
  * If they match, then you're unlocked.
  * This is how NCK is verified.

**The trick is: How does the phone actually perform the unlock?**

## **METHOD 1** ##

  * Phone verifies hash(NCK) above and says "OK, I should unlock now."
  * Phone then jumps to a routine that generates an unlocked NOR seczone and writes it.
  * User is unlocked.
  * In this case it could be possible to skip the NCK verification by patching the baseband to jump directly to the unlock routine, write the unlock, then unpatch.

## **METHOD 2** ##

  * hash(NCK) is required to be put into NOR seczone to trigger a successful unlock routine.
  * This sounds more secure but it's not since it would indicate that the phone can do unhash(NCK) in order to validate.
  * That means that the NCK must be already stored somewhere on the phone and that all we have to do is find it.
  * Something **must** be written to NOR seczone to indicate unlock or not.
  * The question is does that something depend on NCK?

In the NOR seczone there is an encrypted simlock data section based on IMEI.  It's not just 0 = unlocked 1 = locked.  Phone A will have a different bit of data than phone B.  If the phone receives the correct NCK then it can generate the correct piece of data that indicates UNLOCKED.  In this case we should forget about writing directly to NOR seczone ourselves since it will be different for each phone.  We've only to learn exactly what the _phone_ wants to write, and trigger that.

  * How does the phone know what to write into NOR seczone? Is it based on the NCK or is the NCK irrelevant and it's just an access code to execute unlock routine?  If it's irrelevant we've only to jump the verification and execute the unlock.  Once we've triggered it we remove our baseband patch and the phone is virgin again.

## **Theory about what IPSF _actually does_** ##
  * They read the encrypted simlock area on the user's phone.  They create a packet including this data and upload it to their server where they put it into one of their phones.  They then trigger the routine _on their phone_ but rather than write out the unlock to their device they send it back to the _user's phone_ and write it in their baseband.
  * This would indicate that every phone is already capable of unlocking itself.  Their method is only useful so as to require the server.
  * It is NOT believed that they have "official" NCKs, or other insider information aside from possibly a baseband datasheet or they work with an Infineon engineer.

_This page is updated as discoveries are made_

**Feel free to comment but in order to keep things tidy we must delete those which aren't technically relevant to the above content.  If you want to participate please read the front page for IRC and forums information.**