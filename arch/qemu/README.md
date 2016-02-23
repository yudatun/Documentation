The Yudatun Open Source Project
========================================

Install QEMU:
----------------------------------------

```
$ cd thirdparty/qemu
$ ./configure --enable-sdl --audio-drv-list=alsa --target-list=arm-softmmu
$ make
$ sudo make install
```

Get sources:
----------------------------------------

```
$ mkdir ~/yudatun_qemu
$ repo init -u ssh://git@github.com/yudatun/manifest.git -m yudatun_qemu.xml
$ repo sync -j[num]
```

Build iamges:
----------------------------------------

```
$ . build/envsetup.sh
$ lunch 4
$ make -j[num]
```

Emulator:
----------------------------------------

### with original image

1.Downloading image:

https://www.raspberrypi.org/downloads/raspbian/

2.Start:

```
$ qemu-system-arm -kernel boot/kernel.img -cpu arm1176 -m 256 -M versatilepb -no-reboot -serial stdio -append "root=/dev/sda2 panic=1 rootfstype=ext4 rw init=/bin/bash" -hda 2015-05-05-raspbian-wheezy.img
```

### with system.img

```
$ simg2img system.img system.img.ext4
$ qemu-system-arm -kernel boot/kernel.img -cpu arm1176 -m 256 -M versatilepb -no-reboot -serial stdio -append "root=/dev/sda panic=1 rootfstype=ext4 rw init=/bin/bash" -hda system.img.ext4
```