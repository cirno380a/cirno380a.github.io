---
layout:     post
title:      "小米设备刷机全过程记录"
subtitle:   "从解锁到刷机，自己动手，丰衣足食"
date:       2018-12-07 21:16:00
author:     "CRH380A-2722"
header-img: "img/bg-default.jpg"
tags:
    - 刷机
    - Android
    - 心得
---

为了让自己以后刷机不再蒙圈儿，于昨天凌晨亲手实践了一次不使用所谓第三方开发者工具刷机的全过程之后，决定把过程记录下来，以作备考。

###1. 解 BOOTLOADER 锁

现在新出的小米手机大多都已经把 BOOTLOADER 锁上了。要想刷任何的第三方 ROM ，前提是把 BOOTLOADER 锁给解掉。

小米已经推出了官方解锁工具，参见 http://www.miui.com/unlock/index.html . 按照网页上的和解锁工具上的操作步骤提示一步步来就好。

###2. 部署 Platform Tools

刷入 Recovery 所用到的 `fastboot`,  `adb` 等工具，都包含在了 Platform Tools 里面。这个工具包会捆扎到 Android Studio 上。如果没有安装 Android Studio ，也可以去下载使用纯命令行的工具包。

Google 也放出了 Platform Tools 的下载链接。参见：

https://dl.google.com/android/repository/platform-tools-latest-darwin.zip (MAC)

https://dl.google.com/android/repository/platform-tools-latest-linux.zip (LINUX)

https://dl.google.com/android/repository/platform-tools-latest-windows.zip (WIDNOWS)

（选择对应操作系统下载就好）

（最好添加一个环境变量，以后就不用频繁键入完整的 Platform Tools 路径了）

###3. 下载并刷入 TWRP Recovery

部署好 Platform Tools 之后，前往 https://twrp.me/Devices/ ，查找相应手机设备的 TWRP 镜像，下载到本地，备用。

把刚刚解锁好的手机用 USB 线连接到电脑，关机状态下按住`电源键` + `音量-键`，进入 Fastboot 模式。

在命令行界面输入以下指令：

`fastboot devices -l`

这条指令会给出一个连接到电脑的所有 Android 设备列表。如果看见一串短的散列字符，那就证明设备和电脑连接正常。如果没响应可以试着重新安装设备驱动（Windows 环境下）

小米也给出了手机驱动的下载链接：https://www.mi.com/c/service/download/

如果一切正常，就立马刷入 Recovery 吧：

`fastboot flash recovery twrp-3.2.1-0-chiron.img`

（我这里是刷入 `twrp-3.2.1-0-chiron.img` 这个镜像. 必须根据实际情况替换镜像文件的路径和文件名）

执行这条指令后，会把刚才的 TWRP 烧写到手机的 Recovery 分区，覆盖原有的官方 Recovery.

刷写成功之后按理来说是完工了，但现在重启之后还是会替换成官方的 Recovery（官方 ROM 会自动更新 Recovery ，然后把刚才刷入的 TWRP 给覆盖掉）。 所以**不要关机/重启，直接使用刚刚的 TWRP 镜像启动：**

`fastboot boot twrp-3.2.1-0-chiron.img`

此时手机会自动进入 TWRP Recovery.

刷写 Recovery 步骤结束。根据 TWRP 的提示操作就好。

注意！此时未刷入第三方系统的情况下，重启之后官方 ROM 还是会把你刚刷的 TWRP Recovery 给更新成官方 Recovery. 所以**在刷入第三方 ROM 之前，尽量避免关机/重启操作，否则又得重新刷入 TWRP 了。**

###4. 使用 TWRP 清除手机数据

进入 TWRP 的**“清除”**菜单，点按**“高级清除”**，在要清除的分区内，把`"Dalvik/ART Cache"`, `"Cache"`, `"System"`, `"Data"`打上勾。内置存储的话，如果打算把整台机子的数据都抹掉的话，也可以打上。一般还是不要打了。然后滑动滑块，开始清除数据。清除数据成功后，才能开始下一步操作。

###5. 使用 ADB Sideload 刷写第三方ROM

之前看见友人开发 Android 系统时使用了这一种刷入方法，于是去查了一下。发现真的很方便。

这种方法不需要把 ROM 包复制到手机存储/ SD 卡，而是直接通过 USB 推送 ROM 到 TWRP ，然后自动刷写，但效果和卡刷无异。这次我尝试使用这种方法刷机。

在 TWRP 的**“高级选项”**中，点按`"ADB Sideload"`，看情况勾选清除 Cache ，然后滑动滑块开始 Sideload.

通过 USB 线把手机和电脑连在一起，然后在命令行中输入：

`adb sideload lineage-15.1-20180514-nightly-chiron-signed.zip`

（我这里是刷入`lineage-15.1-20180514-nightly-chiron-signed.zip`这个 ROM. 必须根据实际情况替换 zip 文件的路径和文件名）

执行这条命令，然后等待刷入完成即可。

如果刷入成功了，那可以大胆的点击重启进系统了。

###Extra: 刷入 OpenGapps
这个是可选操作。需要 Google 套件的可以一试。

OpenGapps 可以在 https://opengapps.org/ 获取，下载对应 CPU 架构、 Android 版本的包即可。

刷入方法和**“使用 ADB Sideload 刷写第三方 ROM ”**这个部分一模一样。但**刷入完成之后必须回到 TWRP 的“清除”菜单，恢复一次出厂设置，才能重启进系统。**

###THE · 碎碎念
有些设备在系统设置锁屏密码后，开机进入系统 / 进入 TWRP 必须输入正确的锁屏密码。这点不用担心，记住密码就好。

顺便好像高通 9008 模式能绕过 BOOTLOADER 锁直接刷机。目前我还没尝试过，得找个时间买条工程线试一下。
