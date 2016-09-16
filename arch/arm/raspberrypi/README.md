The Yudatun Open Source Project
========================================

Get sources:
----------------------------------------

```
$ mkdir ~/Yudatun
$ repo init -u ssh://git@github.com/yudatun/brillo_manifest.git -m yudatun-v1-rpi-dev.xml
$ repo sync -j[num]
```

Build images:
----------------------------------------

```
$ . build/envsetup.sh
$ lunch
$ make -j[num]
```

Flash images:
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
