# Introduction #

Iphone 1.0.2 kernel memory map. Derived from pmaps.
Names from ioreg command output, IODeviceMemory properties.

# Details #
```
     virt     len         phys     name
 000 c0000000.07400000 -> 08000000
 001 e0000000.00001000 -> 3cc00000 uart0, S5L8900XSerial, CalloutDevice=/dev/cu.iap, DialinDevice=/dev/tty.iap
 002 e0097000.00002000 -> 38e00000 vic 
 003 e0099000.00001000 -> 3e200000 timer
 004 ee79d000.00c00000 -> 0f400000 vram
 005 ef39d000.00003000 -> 38e00000 vic, interrupt-controller
 006 ef3a0000.00001000 -> 3e400000 gpio, interrupt-controller
 007 ef3a1000.00001000 -> 39a00000 power
 008 ef3a2000.00001000 -> 39a00000 power, gpio
 009 ef3a3000.00001000 -> 3c500000 clkrstgen
 010 ef3a4000.00001000 -> 38100000 clkrstgen
 011 ef3a5000.00001000 -> 38200000 dmac0, DMA controller
 012 ef3a6000.00001000 -> 39900000 dmac1, DMA controller
 013 ef3a7000.00001000 -> 38000000 sha1, S5L8900XSHA1
 014 ef3a8000.00001000 -> 38400000 usb-otg, S5L8900XUSBWrangler
 015 ef3a9000.00003000 -> 38500000 amc
 016 ef3ac000.0002c000 -> 22000000 amc
 017 ef423000.00001000 -> 38800000 adm, S5L8900XFMC S5L8900XADM
 018 ef424000.00001000 -> 38c00000 aes, S5L8900XAES
 019 ef425000.00001000 -> 39000000 jpeg
 020 ef444000.01000000 -> 3b000000 mbx, 16Mb, PowerVR MBX 3D graphics processor
 021 f0444000.00001000 -> 3c300000 spi0, MerlotLCD
 022 f0445000.00001000 -> 3c400000 otgphyctrl, USBWrangler
 023 f0446000.00001000 -> 3c600000 i2c0, audio0, WM8758Audio, WM875XButtons HID, WM875xOutput, accelerometer LIS302DL, TSL2561 als, PCF50635PMU backlight&RTC&Power
 024 f0447000.00001000 -> 3e300000 wdt, watchdog timer s5l8900x
 025 f0448000.00001000 -> 3c900000 i2c1, camera0 Micron2020
 026 f0449000.00001000 -> 3ca00000 i2s0, audio0, WM8758Audio, WM875XButtons, WM875xOutput
 027 f044b000.00001000 -> 3cd00000 i2s1, baseband audio input/output
 028 f044c000.00001000 -> 3ce00000 spi1, lcd0 merlot
 029 f044d000.00001000 -> 3d200000 spi2, multi-touch z1, firmware based
 030 f044e000.00100000 -> 24000000 nor-flash, 1048576 bytes
 031 f0560000.00001000 -> 3cc04000 uart1, baseband, CalloutDevice=/dev/cu.baseband, DialinDevice=/dev/tty.baseband
 032 f0561000.00001000 -> 3cc0c000 uart3, bluetooth, CalloutDevice=/dev/cu.bluetooth, DialinDevice=/dev/tty.bluetooth
 033 f0576000.00001000 -> 3cc00000 uart0, S5L8900XSerial, CalloutDevice=/dev/cu.iap, DialinDevice=/dev/tty.iap
 034 f0578000.00001000 -> 3cc10000 uart4, debug,  CalloutDevice=/dev/cu.debug, DialinDevice=/dev/tty.debug
 035 f0579000.00140000 -> 18000000 edram, 1310720 bytes, iBEC loaded here
 036 f06e2000.00001000 -> 38900000 clcd
 037 f06e3000.00001000 -> 39600000 mpvd
 038 f06e4000.00070000 -> 39600000 mpvd
 039 f0754000.00001000 -> 39800000 h264bpd
 040 f0755000.01000000 -> 3b000000 mbx, 16Mb, PowerVR MBX 3D graphics processor
 041 f1755000.01000000 -> 3b000000 mbx
 042 f278d000.00001000 -> 38d00000 sdio, MRVL868X, airport en0
 043 f27e3000.00001000 -> 39700000 camin, H1CameraInterface
 044 f27e5000.00c00000 -> 0f400000 vram

 not kernel mapped, kexts not loaded ?

 physical-addr     name
 38a00000.1000  -> flash-controller0, NAND /dev/disk0, bsd minor 0, major 14, block size 2048
 38f00000.1000  -> flash-controller0, NAND /dev/disk0, bsd minor 0, major 14, block size 2048
 39100000.1000  -> tv-out
 39200000.1000  -> tv-out
 39300000.1000  -> tv-out
 39610000.1000  -> mpvd
 39630000.1000  -> mpvd
 39641000.1000  -> mpvd
 39650000.1000  -> mpvd
 39660000.1000  -> mpvd
 3e100000.1000  -> prng
 3d000000.1000  -> pke
 3e500000.1000  -> chipid
 20000000.10000 -> vrom, 65536 bytes
 24004000.2000  -> diagnostic-data, 8192 bytes
 24006000.2000  -> diagnostic-data, 8192 bytes
 240fc000.2000  -> nvram, 8192 bytes
 240fe000.2000  -> nvram, 8192 bytes
 24008000.f4000 -> raw-device, nor-flash, 999424 bytes

```