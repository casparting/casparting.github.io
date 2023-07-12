---
title: SK-AM62x E3 boot flow boot sequence
excerpt: >
  介紹SK-AM62x E3開發版開機原理與流程。
categories:
  - Embedded System
tags:
  - SK-AM62x
  - Linux
  - SBC
  - Embedded System
  - boot mode
---
## 前言
本篇內容介紹 SK-AM62x E3 ，開機流程，以及設定的原理。
## 前提準備
* 一塊SK-AM62x E3開發版
* SD Card
* USB Type-C
* 5V-15V / 3A Power Supply

## ROM Boot 開機流程 boot flow (boot sequence)
根據TI官方文件，嵌入式系統的開機流程大致上分成四個階段如下圖所示
![TI_BOOT_FLOW](/assets/images/TI_BOOT_FLOW.png)

當上電或重置 power-on reset (POR) 之後ROM裡面的程式會開始執行

待續...
## 結語
研究原理探究跟因~