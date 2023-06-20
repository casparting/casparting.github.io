---
title: RPi CM4 解決USB不通的問題 PCIE 轉 USB 3.0 uPD72020x
excerpt: >
  公司base on RPi CM4做了一塊開發板，板子拿到當然要先燒入image再做hardware驗證，這邊將介紹怎麼解決USB 3.0不通的問題。
categories:
  - Embedded System
tags:
  - Raspberry Pi
  - Linux
  - SBC
  - Embedded System
  - CM4
  - IoT
  - USB
  - uPD72020x
---
## 前言
本篇內容將base on RPi CM4來帶各位一步一步瞭解硬體驗證的流程與步驟，主要針對PCIE轉USB的啟用。
## 前提準備
在啟用USB 3.0之前我們要先把RPi架設好環境，請參考這篇文章->[Base on RPi CM4 開發板 硬體驗證流程 教學 步驟 硬體驗證工程師 BSP工程師](https://casparting.github.io/embedded%20system/Base-on-RPi-CM4-%E9%96%8B%E7%99%BC%E6%9D%BF-%E7%A1%AC%E9%AB%94%E9%A9%97%E8%AD%89%E6%B5%81%E7%A8%8B/)<-

## 找出USB與Ethernet不通的問題
這邊要開啟USB 3.0的功能主要是針對我公司板子的硬體，跟CM4 IO Board不完全一樣，經過實驗我發現同樣的firmware之下CM4 IO Board可以使用USB，到公司的板子卻無法使用，所以我就開始查公司提供的硬體電路圖，我從電路圖下去搜尋找到USB接腳位置，發現USB的接腳接去一個PCIE轉USB的晶片，我在搜尋PCIE接腳，又發現PCIE接腳接去CM4，礙於公司保密協定，所以我不能把公司硬體電路圖放上來，我們拿CM4 IO Board電路圖來說如下所示
![CM4_IOBOARD_IO_PIN](/assets/images/CM4_IOBOARD_IO_PIN.png)

上圖這是Module1B Compute Module 4(CM4)的PCIE接腳的部分

我們來看看PCIE從CM4接去了哪邊，請在電路圖文檔搜尋任一PCIE接腳名稱，如PCIE_RX_P。

搜尋後發現另一個出現PCIE_RX_P是PCIE的Connector如下圖所示
![CM4_IOBOARD_PCIE](/assets/images/CM4_IOBOARD_PCIE.png)

上圖是CM4 IO Board上的PCIE Connector

所以我從電路圖比對得知，CM4 IO Board有一個PCIE插槽，在我公司的線路圖上刪除了PCIE插槽，而直接把PCIE轉USB的晶片放上去做連接轉換。

那此時我們可以用lspci指令來看PCI是否有偵測到
```
# lspci |grep -i usb
```

ref: [usb 3.0 pci card not working](https://bbs.archlinux.org/viewtopic.php?id=195950)

經過確認後是有發現USB3.0的PCIE，但是lsusb沒有抓到。

有了大概的方向之後依照過去的經驗，有新增晶片元件，我們就要確認是否有上晶片的驅動程式，所以我開始看Linux kernel的config檔(驅動程式組態設定檔)，通常config可以在/boot/config-$kernel_version可以找到，而CM4我在這邊沒有看到config，所以我們用另一個方式如下。
```
$ sudo modprobe configs
$ zcat /proc/config.gz > .config
$ cat .config
```
ref:

[不會交叉編譯也可以自己編譯核心](https://ithelp.ithome.com.tw/articles/10238927)

[What is /proc/config.gz?](https://blog.fpmurphy.com/2015/10/what-is-procconfig-gz.html)

然後再使用grep搜尋文檔內的關鍵字
```
cat .config | grep PCI
cat .config | grep XHCI
```
經過確認後kernel option是有選到的，我們再來看一下kernel message如下指令
```
# dmesg |grep pci
pci 0000:01:00.0: xHCI HW not ready after 5 sec (HC bug?) status = 0x801
```
發現了錯誤訊息，經網路搜尋得知[USB3 stopped working with 5.13.6-arch1-1 again](https://bbs.archlinux.org/viewtopic.php?id=268407)，這個晶片需要安裝firmware。

燒入的方式請參考[此連結](https://github.com/markusj/upd72020x-load)，firrmware下載請參考[此連結](https://github.com/denisandroid/uPD72020x-Firmware)

這邊安裝完後就順利抓到USB3.0，並且連同Ethernet也起來了，用同樣的方式去查找電路圖，發現Ethernet是USB3.0下去轉的，所以USB3.0通了網路也通了。

## 結語
硬體驗證就是這麼簡單有趣~