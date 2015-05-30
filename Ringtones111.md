# Introduction #
FW 1.1.1 doesn't recogzine .m4a files as ringtones anymore. The .m4r ringtone files are  protected, so simple renaming .m4a to .m4r just doesn't work.
The following workaround will allow you to install and use your own .m4a ringtones. After patching the MeCCA, all the .m4r files which have space as the last character of the filename (right before the .m4r part) will be treated as regular .m4a files.


# Details #

1. Patch /System/Library/Frameworks/MeCCA.framework/MeCCA:
```
00014458: 05 06
0001445C: 6C 03
0001445D: 10 30
0001445E: 9F 43
0001445F: E5 E0
00014462: 8F 50
00014463: E0 E5
00014464: 6D 20
00014465: 28 00
00014466: 03 51
00014467: EB E3
00014468: 00 02
0001446A: 50 00
0001446B: E3 0A
0001446C: 01 03
0001446D: 30 10
0001446E: A0 90
0001446F: 03 E5
00014470: 00 72
00014472: 00 51
00014473: 0A E3
00014474: 00 01
00014477: E3 03

```

2. Reboot your iphone. You only need to do it once after patching the MeCCA.

3. Put your .m4a ringtone into /Library/Ringtones.

4. The tricky part. You need to rename your ringtone so it will end with ' .m4r'. Note the blank before the dot. It will indicate that no special .m4r treatment is needed for this file. So normal .m4r files will look like 'Tone1.m4r' or 'Tone2.m4r'. Custom .m4a files will look like 'Tone1 .m4r' or 'Tone2 .m4r'.

5. Go to setting/sounds, select your rigntone and enjoy.