The Yudatun Open Source Project
========================================

Main Page:
----------------------------------------

[![image]](http://yudatun.strikingly.com)
[image]: https://avatars3.githubusercontent.com/u/5319183?s=400 "Yudatun"

Preparation:
----------------------------------------

```
$ sudo apt-get install git-core gnupg flex bison gperf build-essential zip curl
$ sudo apt-get install git-cola
$ sudo apt-get install gitk
$ sudo apt-get install libpci-dev
$ sudo apt-get install dh-autoreconf
$ sudo apt-get install libglib2.0-dev
$ sudo apt-get install autoconf
$ sudo apt-get install libsdl1.2-dev libsdl1.2debian
$ sudo add-apt-repository ppa:gezakovacs/ppa
$ sudo apt-get update
$ sudo apt-get install unetbootin
```

Get Sources:
----------------------------------------

### Config Repo:

```
$ mkdir ~/bin
$ vim ~/.bashrc
add PATH=~/bin:$PATH to the end of ~/.bashrc
$ curl http://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
$ chmod a+x ~/bin/repo
```

### Sync Sources:

#### qemu

```
$ mkdir ~/yudatun_qemu
$ repo init -u ssh://git@github.com/yudatun/manifest.git -m yudatun_qemu.xml
$ repo sync -j[num]
```

#### raspberry-pi

```
$ mkdir ~/yudatun_rpi
$ repo init -u ssh://git@github.com/yudatun/manifest.git -m yudatun_raspberry-pi.xml
$ repo sync -j[num]
```

### Make Image:

```
$ . build/envsetup.sh
$ lunch 4
$ make -j[num]
```

Config Environment:
----------------------------------------

### Install QEMU:

```
$ cd thirdparty/qemu
$ ./configure --enable-sdl --audio-drv-list=alsa --target-list=arm-softmmu
$ make
$ sudo make install
```

### Starting:

```
$ cd yudatun/out/target/product/qemu/
$ start kernel initramfs.img
or
$ start-nographic kernel initramfs.img
```

### Running on raspberrypi

```
$ flash ptable out/target/product/MBR.bin -H /dev/mmcblk0
$ flash boot out/target/product/boot.img -H /dev/mmcblk0p1
$ flash system out/target/product/system.img -H /dev/mmcblk0p2
```

Contribution:
----------------------------------------

### make patch

```
Edit to some files your want and make patch as follows:
$ git add file1, file2
$ git commit -s
Input some message as following:
> Line 1：Commit Message Sample
> Line 2：
> Line 3：N/A(or bugid#id)
> Line 4：
> Line 5：This is a sample of yudatun commit message. Please follow this.
> Line 6：Signed-off-by: liminghao <vyudatun@gmail.com>
$ git format-patch yudatun/master -o out_patch/
Use a email-box by yourself and attach the patch and send to us:
```

### push to gerrithub for review

```
$ git push ssh://yudatun@review.gerrithub.io:29418/yudatun/projectname HEAD:refs/for/branch
```

or

```
$ yudatun_upload projectname branch
```

Mail Lists:
----------------------------------------

* <yudatun@gmail.com>
* <shuntongzhang@gmail.com>
* <liminghao@xiaomi.com>
