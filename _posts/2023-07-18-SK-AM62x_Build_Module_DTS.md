---
title: SK-AM62x Build Moudule DTS
excerpt: >
  介紹SK-AM62x 編譯Linux Moudule DTS。
categories:
  - Embedded System
tags:
  - SK-AM62x
  - Linux
  - SBC
  - Embedded System
  - Moudule
  - Device Tree Source
---
## 前言
本篇內容介紹 SK-AM62x 編譯source code裡的Kernel。
## 前提準備
* Host PC (Development Host)
* Host PC OS: Ubuntu 18.04 LTS
P.S. 官方不建議使用虛擬機，這邊我是使用WSL

## 下載與安裝SDK
根據TI官方[下載頁面](https://www.ti.com/tool/PROCESSOR-SDK-AM62X)，依照你的需求選擇適合的SDK，這邊我是下載PROCESSOR-SDK-LINUX-AM62X內的
ti-processor-sdk-linux-am62xx-evm-08.06.00.42-Linux-x86-Install.bin

在下載等待的同時我們先安裝編譯source code所需要的一些套件，指令如下
```console
$ sudo apt update
$ sudo apt install build-essential bison flex libssl-dev libncurses-dev u-boot-tools
```

安裝好後ti-processor-sdk-linux-am62xx-evm-08.06.00.42-Linux-x86-Install.bin程式也下載好後，接下來要執行它，如下指令。
```console
$ chmod +x ti-processor-sdk-linux-am62xx-evm-08.06.00.42-Linux-x86-Install.bin
$ ./ti-processor-sdk-linux-am62xx-evm-08.06.00.42-Linux-x86-Install.bin
```

注意!如果這邊出現圖形化GUI錯誤訊息，請參考這篇文章內介紹的WSL使用GUI介面的介紹~

安裝過程確認路徑後就下一步完成~

Ref: 
* [Download and Install the SDK](https://software-dl.ti.com/processor-sdk-linux/esd/AM62X/08_06_00_42/exports/docs/devices/AM62X/linux/Overview/Download_and_Install_the_SDK.html)
* [Basic Setup Steps](https://dev.ti.com/tirex/explore/node?node=A__APVGH4iNvyKxG.AeLQAVgg__linux_academy_am62x__XaWts8R__LATEST&search=am62x)

## 編譯

待續...

## 結語
編譯source code 是嵌入式開發工程師基本的技能~