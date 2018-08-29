---
layout:     post
title:      "Ubuntu通过RSA密钥实现无密码登录SSH"
subtitle:   "才不想敲那些又臭又长的密码呢"
date:       2018-08-30 02:39:00
author:     "CRH380A-2722"
header-img: "img/bg-default.jpg"
tags:
    - Ubuntu
    - 心得
---

自从换了 Ubuntu 之后，每次通过 SSH 连接远程服务器都要手打密码。对于密码设的特别长的我就比较麻烦了。为了增加效率（偷懒），我决定换一种方式登录服务器。

利用 RSA 密钥可以实现无密码登录。（不设置 passphrase 就行）那么就先在本地机子生成一个密钥好了：

`ssh-keygen -t rsa`

然后敲3下回车就可以生成 `id_rsa` 私钥和 `id_rsa.pub` 公钥了。

（第一下回车是键入密钥保存的路径，第二下和第三下是输入 passphrase. 因为 passphrase 需留空所以直接敲回车跳过就好）

然后进入 `~/.ssh` 目录，把 `id_rsa.pub` 上传到远程服务器的 `~/.ssh` 目录中，并且重命名为 `authorized_keys` . 这个操作可通过 **scp** 命令完成：

`scp ~/.ssh/id_rsa.pub root@192.168.1.114:~/.ssh/authorized_keys`

因为之前未在远程服务器上部署公钥，所以这个 scp 操作需要输入远程服务器的登录密码。

上传完成之后登录远程服务器，把 `~/.ssh/authorized_keys` 的权限改为 `600`：

`chmod 600 ~/.ssh/authorized_keys`

登出服务器再登入：

`ssh root@192.168.1.114`

就可以不用输入密码直接登录了。

THE · 补充
听 @tcdw 说可以利用 **ssh-copy-id** 来代替scp，于是去查了一下相关资料。果然，ssh-copy-id 比上面的操作更方便。

只需要敲这么一行指令：

`ssh-copy-id -i ~/.ssh/id_rsa.pub root@192.168.1.114`

就行了！