The Yudatun Open Source Project
================================================================================

Main Page:
--------------------------------------------------------------------------------

[![image]](http://yudatun.strikingly.com)
[image]: https://avatars3.githubusercontent.com/u/5319183?s=400 "Yudatun"

Preparation:
--------------------------------------------------------------------------------

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

How To Get Sources?
--------------------------------------------------------------------------------

### Config Repo:

```
$ mkdir ~/bin
$ vim ~/.bashrc
add PATH=~/bin:$PATH to the end of ~/.bashrc
$ curl http://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
$ chmod a+x ~/bin/repo
```

### Sync Sources:

##### qemu

```
$ mkdir ~/yudatun_qemu
$ repo init -u https://github.com/yudatun/manifest.git -m yudatun_qemu.xml
```

##### raspberry-pi

```
$ mkdir ~/yudatun_rpi
$ repo init -u https://github.com/yudatun/manifest.git -m yudatun_raspberry-pi.xml
```

**Sync**:

```
$ repo sync -j4
```

### Make Image:

```
$ . build/envsetup.sh
$ lunch
$ make
```

Config Environment:
--------------------------------------------------------------------------------

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

Contribution:
--------------------------------------------------------------------------------

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
--------------------------------------------------------------------------------
* <yudatun@gmail.com>
* <shuntongzhang@gmail.com>
* <liminghao@xiaomi.com>

Bootloader Url:
--------------------------------------------------------------------------------
gotobootloader(https://github.com/gotobootloader)

Kernel Url:
--------------------------------------------------------------------------------
gotokernel(https://github.com/gotokernel)

Blog
--------------------------------------------------------------------------------
* http://bliterness.blogspot.com/
* http://blog.csdn.net/zeroboundary/article/details/12657215
* http://www.opensourceforu.com/2011/06/qemu-for-embedded-systems-development-part-1/
* http://blog.csdn.net/leisure512/article/details/6761073