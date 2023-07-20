---
title: SK-AM62x E3 Hardware 驗證
excerpt: >
  SK-AM62x E3開發版硬體驗證過程紀錄。
categories:
  - Embedded System
tags:
  - SK-AM62x
  - Linux
  - SBC
  - Embedded System
  - Hardware Verifly
---
## 前言
本篇內容介紹 SK-AM62x E3 ，開機流程，以及設定的原理。
## 前提準備
* 一塊SK-AM62x E3開發版
* SD Card
* USB Type-C
* 5V-15V / 3A Power Supply

## 開發板燒入firmware且可開機

請參考我寫的這篇[SK-AM62x_E3_boot_mode_設定_開機模式選擇]({% link _posts/2023-07-11-SK-AM62x_E3_boot_mode_設定_開機模式選擇.md %})文章~

## LED點亮驗證

請輸入以下指令確認
```console
# ls /sys/class/leds/
am62-sk:green:heartbeat  mmc0::  mmc1::  mmc2::
```


待續...
## 結語
研究原理探究跟因~