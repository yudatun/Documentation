ADB
========================================

```
$ adb forward tcp:5555 tcp:5555
$ adb connect 10.235.228.181
unable to connect to 10.235.228.181:5555: Connection refused
$ adb tcpip 5555
restarting in TCP mode port: 5555
$ adb connect 10.235.228.181
connected to 10.235.228.181:5555
$ adb devices
List of devices attached
10.235.228.181:5555	device
c0d9b895	device
```