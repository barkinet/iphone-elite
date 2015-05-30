# Introduction #

Here will live a list of only **UNDOCUMENTED** AT Commands for the baseband.


# Commands #

| **AT+ipr=XXXX** | This one sets the terminal speed. Example: at+ipr=230400. | |
|:----------------|:----------------------------------------------------------|:|
| at+xgendata     | Writes the baseband version and EEP version and revision  | |
| at+xtransportmode | Leaves AT mode and enters TRANSPORT mode (binary)         | |
| at+xlog=0       | Shows firmware Version                                    | |
| at+xpincnt      | Show SIM PIN Counters (attempts left)                     | |
| at+ccid         | Shows SIM ICCID                                           | |
| at+xl1set="command" | various commands see note 1                               | |
| at+xrrset="command" | various commands see note 2                               | |
| at+xgcntset=?!?!? | various commands see note 3                               | |
| at+xgcntrd=?!?!? | various commands see note 3                               | |

## Notes ##

  1. "help", "iroff", "iron", "ir\_opt\_off", "ir\_opt\_on", "iroff"

  1. "help", "psvoff", "psvon", "sev", "mon:", "ms\_error\_log", "HO\_FAIL\_ON", "HO\_FAIL\_OFF", "scell", "balist", "pdch\_filter"

  1. Unknown parameters at the moment. GPRS related.

### To be inspected ###

**SYS COMMANDS**

at+xdiag

at+xgendata

at+xmer

at+xceer

at+xcind

at+xconfig

at+xctmdr

at+xctms

at+xsln

at+xals

at+xhandsfree

at+xselfrxstat

at+csq

at+ctzu

at+ctzr

at+calm

at+crsl

at+clvl

at+cmut

at+xalsblock

at+xlin

at+xhomezr

at+xciph

at+cged

at+xrel

at+xcspaging

at+trace

at+xservice

at+xtdev

at+xtfilter

at+xtraceip

at+cmec

at+xmagetkey

at+xmagetblock

at+xpow

at+xtracesystime

**TEST COMMANDS**

at+xdevice

at+xmultislot

at+xrlcset

at+xrrset

at+xsimloopback

at+xloopback

at+xppp