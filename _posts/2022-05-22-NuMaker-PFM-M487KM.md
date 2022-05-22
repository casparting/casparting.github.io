---
title: 執行NuMaker-PFM-M487KM開發板範例程式
excerpt: >
  執行NuMaker-PFM-M487KM開發板範例程式。
categories:
  - Embedded System
tags:
  - Linux
  - Debian
  - MCU
  - Nuvoton
  - M487
---
# 前言
利用假日空閒時間我兼了外包案子，首先要先很感謝這位Steven資深工程師給我機會實做M487專案，這邊紀錄我開發的過程。
# 準備
請先準備下面軟硬體配備。
* 硬體
    - NuMaker-PFM-M487KM開發板
    - Host: PC/NB
* 軟體
    - NuEclipse_V1.01.019_Linux
    - M480BSP

### NuMaker-PFM-M487KM開發板

建議到新唐官方網站購買->[連結在這裡](https://direct.nuvoton.com/tw/numaker-pfm-m487km?search_query=M487&results=9)
記得先選好國家在填寫地址國內運送運費是120元。

### Host: PC/NB

* OS: Linux
* Distro: Debian 11

### NuEclipse_V1.01.019_Linux

Nuvoton官方網站有提供Keil/Eclipse的IDE solution，因我使用的Host是Linux所以我這邊使用NuEclipse->[載點在這裡](https://www.nuvoton.com/tool-and-software/ide-and-compiler/)
請確認官方的說明！你如果不是使用M487晶片，請確認你所要使用的IDE有支援你的晶片，如下圖所示。
![nueclipse](/assets/images/nueclipse.png)

下載好後我參考了[此篇NuEclipse Guide](https://www.nuvoton.com/resource-files/UM_NuEclipse_EN_Rev1.01.014.pdf)安裝步驟與環境設定如下：
```
$ tar xfva NuEclipse_V1.01.018_Linux_Setup.tar.gz
$ cd NuEclipse_V1.01.018_Linux_Setup/
$ ./install.sh 
```
請注意執行install.sh使用一般使用者權限前面不加sudo，在這支install.sh裡面已經有sudo的指令了，所以執行過程會要你輸入密碼請記得！

安裝好之後執行NuEclipse如下指令：
```
$ cd eclipse/
$ ./eclipse
```
預設已經設定好Global Tools Paths與OpenOCD Nu-Link，這邊要注意的是文件中OpenOCD Nu-Link的路徑寫錯了，應該是放在自己的家目錄/local/OpenOCD，而不是在/usr/底下！

### 持續撰寫中。。。

