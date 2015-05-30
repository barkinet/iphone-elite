# How to redirect syslogd output to a file. #

1. create usual /etc/syslog.conf e.g.
```
*.* /var/log/syslog 
```

2. add the following lines to `/System/Library/LaunchDaemons/com.apple.syslogd.plist`
right after `<string>/usr/sbin/syslogd</string>`:
```
<string>-bsd_out</string>
<string>1</string>
```

3. reboot & enjoy your kernel and other messages

Note:

Once enabled, syslog will grow in size really fast...
To disable it without changing the plist, here is a quick way:

  * Disable syslog:
```
  # rm /var/log/syslog; mknod /var/log/syslog c 3 2
```

  * Re-Enable syslog:
```
  # rm /var/log/syslog; touch /var/log/syslog
```