---
title: SK-AM62x Build RootFS
excerpt: >
  介紹SK-AM62x 編譯Linux RootFS。
categories:
  - Embedded System
tags:
  - SK-AM62x
  - Linux
  - SBC
  - Embedded System
  - RootFS
---
## 前言
本篇內容介紹 SK-AM62x 編譯source code裡的Kernel。
## 前提準備
* Host PC (Development Host)
* Host PC OS: Ubuntu 18.04 LTS
P.S. 官方不建議使用虛擬機，這邊我是使用WSL

## 進到RootFS路徑
```console
$ cd /home/caspar/ti-processor-sdk-linux-am62xx-evm-08.06.00.42/filesystem
$ ls
tisdk-base-image-am62xx-evm.tar.xz         tisdk-docker-rootfs-image-am62xx-evm.tar.xz
tisdk-base-image-am62xx-evm.wic.bmap       tisdk-docker-rootfs-image-am62xx-evm.wic.bmap
tisdk-base-image-am62xx-evm.wic.xz         tisdk-docker-rootfs-image-am62xx-evm.wic.xz
tisdk-bootstrap-image-am62xx-evm.tar.xz    tisdk-thinlinux-image-am62xx-evm.tar.xz
tisdk-bootstrap-image-am62xx-evm.wic.bmap  tisdk-thinlinux-image-am62xx-evm.wic.bmap
tisdk-bootstrap-image-am62xx-evm.wic.xz    tisdk-thinlinux-image-am62xx-evm.wic.xz
tisdk-default-image-am62xx-evm.tar.xz      tisdk-tiny-image-am62xx-evm.tar.xz
tisdk-default-image-am62xx-evm.wic.bmap    tisdk-tiny-image-am62xx-evm.wic.bmap
tisdk-default-image-am62xx-evm.wic.xz      tisdk-tiny-image-am62xx-evm.wic.xz
```

底下這邊有不同rootfs官網的解釋，我就不多做說明。

![am62x_rootfs_diff](/assets/images/am62x_rootfs_diff.png)

待續...


## 結語
編譯source code 是嵌入式開發工程師基本的技能~