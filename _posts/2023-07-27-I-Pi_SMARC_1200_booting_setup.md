---
title: I-Pi SMARC 1200 燒入 Image 並使用 UFS 開機
excerpt: >
  I-Pi SMARC 1200 燒入 Image 並使用 UFS 開機紀錄。
categories:
  - Embedded System
tags:
  - I-Pi SMARC 1200
  - Linux
  - SBC
  - Embedded System
  - Yocto
  - UFS
---
## 前言
本篇是紀錄I-Pi SMARC 1200 燒入 Image 並使用 UFS 開機的過程。
## 前提準備
* 一塊I-Pi SMARC 1200開發版
* USB Type-C
* Power Supply
* Host PC/WSL (Ubuntu 22.04 LTS)

## 安裝 AIoT 工具

請參考[此網址](https://docs.ipi.wiki/SMARC/ipi-smarc-1200/AIoTToolInstallation.html)按照他的步驟下指令。

如果你跟我一樣使用WSL的話，到燒入Image那個步驟的話，需要使用USB/IP來連接Windows的USB裝置到WSL內，才能順利把Image透過USB燒入進去。詳細WSL USB/IP可以參考我[這篇文章]({% link _posts/2023-07-21-Windows_mount_Linux_partition_wsl.md %})。

這邊要特別注意的是，燒入過程中板子會進入recovery，這時候要再重新usbip attach，再次連上USB設備。

## 結語
研究原理探究跟因~