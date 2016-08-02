The Yudatun Open Source Project
========================================

Get sources:
----------------------------------------

```
$ mkdir ~/Yudatun
$ repo init -u ssh://git@github.com/yudatun/manifest.git -m yudatun-v1-rpi-dev.xml
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
$ flash ptable out/target/product/raspberrypi/MBR.bin -H /dev/mmcblk0
$ flash boot out/target/product/raspberrypi/boot.img -H /dev/mmcblk0p1
$ flash system out/target/product/raspberrypi/system.img -H /dev/mmcblk0p2
```

SSH login:
----------------------------------------

```
sudo ssh pi@ip
```

### scan ip in local network

https://github.com/yudatun/Documentation/tree/master/arch/arm/raspberry_pi/nmap.md
