# Introduction #

Issue these commands in minicom  to read SMSs stored on the SIM card. If PDU mode is being used, all retrieved SMS will be in human-unreadable hexadecimal format (TPDU encoded).  To be able to read the retrieved SMS you can switch to text mode using +CMGF as illustrated below.


# Details #


//check SMS mode: 0 is PDU Mode, 1 is Text Mode

**AT+CMGF?**


//set SMS mode to text

**AT+CMGF=1**


//read all SMSs stored on SIM card

**AT+CMGL**