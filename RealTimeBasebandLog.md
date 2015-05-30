# How to log the baseband in realtime. #

From SSH (or terminal) issue these commands:

```
rm /dev/console
mknod /dev/console c 4 0
kill -USR1 `ps ax|grep omm|grep ys|xargs|cut -d ' ' -f 1`
kill -HUP `ps ax|grep omm|grep ys|xargs|cut -d ' ' -f 1`
```

## How to stop the log. ##

```
rm /dev/console
mknod /dev/console c 1 0
kill -USR1 `ps ax|grep omm|grep ys|xargs|cut -d ' ' -f 1`
```