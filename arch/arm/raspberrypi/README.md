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
