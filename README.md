The Yudatun Open Source Project
========================================

Main Page:
----------------------------------------

[![image]](http://yudatun.strikingly.com)
[image]: https://avatars3.githubusercontent.com/u/5319183?s=400 "Yudatun"

Preparation:
----------------------------------------

### Dependencies

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

### Repo

```
$ mkdir ~/bin
$ vim ~/.bashrc
add PATH=~/bin:$PATH to the end of ~/.bashrc
$ curl http://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
$ chmod a+x ~/bin/repo
```

Platforms:
----------------------------------------

### qemu

https://github.com/yudatun/Documentation/tree/master/arch/qemu/README.md

### raspberry-pi

https://github.com/yudatun/Documentation/tree/master/arch/arm/raspberry_pi/README.md

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
