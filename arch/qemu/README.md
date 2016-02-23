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