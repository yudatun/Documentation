The Yudatun Open Source Project
========================================

Get Sources:
----------------------------------------

```
$ mkdir ~/Yudatun
```

### master

```
$ repo init -u ssh://git@github.com/yudatun/brillo_manifest.git -m yudatun-v1-rpi-dev.xml
```

### brillo-m10-release

```
repo init -u ssh://git@github.com/yudatun/brillo_manifest.git -b brillo-m10-release -m yudatun-v1-rpi-dev.xml
```

Sync Sources:

```
$ repo sync -j[num]
```

Build Images:
----------------------------------------

```
$ . build/envsetup.sh
$ lunch raspberrypi3-eng
$ make -j[num]
```

Flash Images:
----------------------------------------

```
$ sudo dd if=MBR.bin of=/dev/mmcblk0 bs=512
$ sudo dd if=rpi-boot.img of=/dev/mmcblk0p1 bs=4096
$ simg2img system.img system.img.ext4
$ sudo dd if=system.img.ext4 of=/dev/mmcblk0p2 bs=4096
$ simg2img userdata.img userdata.img.ext4
$ sudo dd if=userdata.img.ext4 of=/dev/mmcblk0p3 bs=4096
```

Connection:
----------------------------------------

### eth0

##### HOST

```
$ avahi-browse -ar
+ wlx00e04c0915de IPv4 7d3ca73b-1e7a-47dc-b890-23bd6c88e127          _privet._tcp         local
= wlx00e04c0915de IPv4 7d3ca73b-1e7a-47dc-b890-23bd6c88e127          _privet._tcp         local
   hostname = [linux.local]
   address = [192.168.1.6]
   port = [80]
   txt = ["https=443" "flags=CA" "mmid=AAAAA" "id=94539ec6-1c7c-4e09-9b70-74fe210c58c5" "services=vendor" "ty=Developer device" "txtvers=3"]
- wlx00e04c0915de IPv4 7d3ca73b-1e7a-47dc-b890-23bd6c88e127          _privet._tcp         local
$ adb connect 192.168.1.6
```

##### TARGET

```
# ifconfig
eth0      Link encap:Ethernet  HWaddr b8:27:eb:8d:f0:6d
          inet addr:192.168.1.11  Bcast:192.168.1.255  Mask:255.255.255.0
          inet6 addr: fe80::ba27:ebff:fe8d:f06d/64 Scope: Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:1093 errors:0 dropped:1 overruns:0 frame:0
          TX packets:530 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:84228 TX bytes:56417
```

### wlan0

##### TARGET

```
# shill_setup_wifi --ssid=ssid --passphrase=password
[0101/000203:INFO:dbus_client.cc(82)] Sleeping now. Will check wifi status in 100 ms.
[0101/000203:INFO:main.cc(91)] Process exiting.
# ifconfig
wlan0     Link encap:Ethernet  HWaddr b8:27:eb:d8:a5:38
          inet addr:192.168.1.13  Bcast:192.168.1.255  Mask:255.255.255.0
          inet6 addr: fe80::ba27:ebff:fed8:a538/64 Scope: Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:211 errors:0 dropped:201 overruns:0 frame:0
          TX packets:17 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:84810 TX bytes:2831
```

##### HOST

```
$ adb connect 192.168.1.13
```

curl
----------------------------------------

### privet/info

```
$ curl -H "Authorization: Privt anonymous" -k https://10.232.33.37/privet/info
{
   "authentication": {
      "anonymousMaxScope": "user",
      "crypto": [ "p224_spake2" ],
      "mode": [ "anonymous", "pairing" ],
      "pairing": [ "embeddedCode" ]
   },
   "basicModelManifest": {
      "modelName": "Brillo Starter Board",
      "oemName": "Yudatun, Inc.",
      "uiDeviceKind": "vendor"
   },
   "description": "Controls Car",
   "endpoints": {
      "httpPort": 80,
      "httpUpdatesPort": 80,
      "httpsPort": 443,
      "httpsUpdatesPort": 443
   },
   "gcd": {
      "id": "",
      "oauth_url": "https://accounts.google.com/o/oauth2/",
      "service_url": "https://www.googleapis.com/weave/v1/",
      "status": "unconfigured",
      "xmpp_endpoint": "talk.google.com:5223"
   },
   "id": "37D535F6-8ED1-4E31-B46F-8EE5EAE007BB",
   "modelManifestId": "AAw7x",
   "name": "Smart Car",
   "services": [ "vendor" ],
   "sessionId": "533187446:1",
   "time": 1479872246356.14502,
   "version": "3.0",
   "wifi": {
      "capabilities": [ "2.4GHz" ],
      "ssid": "",
      "status": "online"
   }
}
```

### privet/v3/auth

```
$ curl -H "Authorization: Privet anonymous" -H 'Content-Type: application/json' \
  -X POST \
  --data '{ "mode": "anonymous", "requestedScope": "owner" }' \
  -k https://10.232.33.37/privet/v3/auth
{
   "error": {
      "code": "accessDenied",
      "debugInfo": [ {
         "code": "accessDenied",
         "debugInfo": "HandleAuth@external/libweave/src/privet/privet_handler.cc:717",
         "message": "Scope 'user' is not allowed"
      } ],
      "message": "Scope 'user' is not allowed"
   }
}

$ curl -H "Authorization: Privet anonymous" -H 'Content-Type: application/json' -X POST --data '{ "mode": "anonymous", "requestedScope": "user" }'     -k https://10.232.33.37/privet/v3/auth
{
   "accessToken": "WCuFRggaH8fv4UIBDkQJQjIAQgpARgUaH8f98VADjblgXFyuQq8u7Aktm1yj",
   "expiresIn": 3600,
   "scope": "user",
   "tokenType": "Privet"
}
```

### privet/v3/commands/list

```
$ curl -H "Authorization: Privet WCuFRggaH8fv4UIBDkQJQjIAQgpARgUaH8f98VADjblgXFyuQq8u7Aktm1yj" -k https://10.232.33.37/privet/v3/commands/list
{
   "commands": [  ]
}
```

### privet/v3/pairing/start

```
$ curl -H "Authorization: Privet anonymous" -H 'Content-Type: application/json' \
>     -X POST \
>     --data '{ "pairing": "embeddedCode", "crypto": "p224_spake2" }' \
>     -k https://10.232.33.37/privet/v3/pairing/start
{
   "deviceCommitment": "APfyX0HQQ69iGS7w//7TfkrIzLr0hEk9VxceJXXM2pEeR/z6zN6LrpBSfdC39Qq4HA87AoxbPW4=",
   "sessionId": "B5C3AD6C-406F-4491-9647-495E27840CE3"
}
```

### privet/v3/commands/execute

```
$ curl -H "Authorization: Privet WCuFRggaH8fv+0IBDkQJQjEAQgpARgUaH8f+C1D2s9N5dS2SGrDHA8AZ2Qzm" -H 'Content-Type: application/json' -X POST --data '{ "deviceId": "37D535F6-8ED1-4E31-B46F-8EE5EAE007BB", "name": "_smartcar.action", "component": "smartcar", "parameters": {"duration": "20", "type": "forward"} }'     -k https://192.168.1.10/privet/v3/commands/execute
{
   "id": "1",
   "name": "_smartcar.action",
   "parameters": {
      "duration": "20",
      "type": "forward"
   },
   "progress": {

   },
   "results": {

   },
   "state": "queued"
}
```