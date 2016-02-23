The Yudatun Open Source Project
========================================

Get sources:
----------------------------------------

```
$ mkdir ~/yudatun_rpi
$ repo init -u ssh://git@github.com/yudatun/manifest.git -m yudatun_raspberry-pi.xml
$ repo sync -j[num]
```

Make images:
----------------------------------------

```
$ . build/envsetup.sh
$ lunch 4
$ make -j[num]
```

Flash images:
----------------------------------------

```
$ flash ptable out/target/product/MBR.bin -H /dev/mmcblk0
$ flash boot out/target/product/boot.img -H /dev/mmcblk0p1
$ flash system out/target/product/system.img -H /dev/mmcblk0p2
```

SSH login:
----------------------------------------

```
sudo ssh pi@ip
```

### scan ip in local network
