The Yudatun Open Source Project
========================================

Get Sources
----------------------------------------

```
$ mkdir ~/Yudatun
```

### brillo-m10-release

```
repo init -u ssh://git@github.com/yudatun/brillo_manifest.git -b brillo-m10-release -m yudatun-v1-rpi-dev.xml
```

Sync Sources:

```
$ repo sync -j[num]
```

Build Iamges
----------------------------------------

```
$ . build/envsetup.sh
$ lunch brilloemulator_arm-eng
$ make -j[num]
```

Run
----------------------------------------

```
$ brilloemulator-arm -m 256 -- -redir tcp:8000::80 -redir tcp:8001::443
```

Connect
----------------------------------------

```
$ adb devices
List of devices attached
* daemon started successfully *
emulator-5554	device
$ adb shell
```
