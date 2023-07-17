---
title: SK-AM62x E3 boot mode 設定 開機模式選擇
excerpt: >
  介紹SK-AM62x E3開發版開機模式設定原理。
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
本篇內容介紹 SK-AM62x E3 ，開機模式的設定方式，以及設定的原理，以便換了E2/E1板子也能知道如何設定。
## 前提準備
* 一塊SK-AM62x E3開發版
* SD Card
* USB Type-C
* 5V-15V / 3A Power Supply

## 嵌入式系統架構
嵌入式系統的定義其實很廣泛，從較小遙控器內的微控制晶片microcontroller(MCU)，到一台電視機上盒的System-on-Chip(SoC)這些都是嵌入式系統，簡單來理解就是將一個小電腦嵌入到一些設備或裝置中的系統。而這些MCU或SoC或是MPU搭配了周邊的IO和記憶體組合成的電路板就叫做單板電腦single board computer(SBC)

來說說SBC的定義~根據chatgpt的說明如下
```
SBC (Single Board Computer) 是指將一個完整電腦系統集成在一個單一的電路板上的電腦。一個基本的SBC將包括一個處理器（CPU）、記憶體（RAM）、儲存介面（如 SSD、SD 卡插槽或 eMMC）、以及其他多種接口和連接器（如 USB、HDMI、GPIO、Ethernet 等）。

事實上，SBC 可以包含任何形式的處理單元，不論是 MPU、SoC，或是更專門的處理單元，如微控制器 (MCU)。這完全取決於該 SBC 的設計目標和需求。
```
所以說常見的SBC有哪一些?例如Raspberry Pi、BeagleBoard、Arduino UNO等~

那我們再來問問chatgpt SoC和MPU的差異
```
SOC（System-on-a-Chip）和 MPU（Microprocessor Unit）是兩種不同的半導體產品和設計方式。他們之間的主要差異在於集成度和功能範疇。

SOC (System-on-a-Chip)：如其名，SOC將整個系統的所有關鍵部分集成在一個單一的晶片上。這通常包括處理器核心、記憶體（RAM）、存儲（例如，Flash、ROM）、I/O介面，以及其他可能的功能區塊（例如GPU、DSP、硬體加速器等）。這種高度的集成度使得SOC能在體積小且能耗低的情況下提供大量的功能，這使得它特別適合移動設備、嵌入式系統和物聯網（IoT）設備。

MPU (Microprocessor Unit)：MPU，一般來說，指的是一個包含一個或多個處理器核心的單晶片，但是並不包括記憶體和其他系統必需的元件。這意味著與SOC相比，MPU通常需要連接到一個外部的記憶體（例如DRAM）和其他元件（例如I/O控制器、儲存介面等）。這種設計提供了更大的靈活性，允許系統設計師選擇適合他們需求的特定元件（例如，具有特定速度、容量或功耗特性的記憶體）。然而，這也導致了較高的系統複雜性和體積。
```

瞭解之後我們要回到今天的主角SK-AM62 starter kit (SK) Evaluation module (EVM)，其實它就是SBC，但各家的都會有一些他們自己的專業名詞定義~那這塊SK-AM62 SBC的主晶片就是SoC AM62x processors，包含了quad-core 64-bit Arm®-Cortex®-A53 microprocessor, single-core Arm Cortex-R5F microcontroller (MCU) and an Arm Cortex-M4F MCU。

這顆AM62x SoC裡面比較特別包含了兩個MCU如圖所示

![am62x_block_diagram](/assets/images/am62x_block_diagram.png)

我們再看一下其他常見的SoC它的架構圖如AM335x block diagram

![am335x_block_diagram](/assets/images/am335x_block_diagram.png)

AM335x圖裡可以看到這顆SoC有64KB的RAM跟176KB的ROM~
AM62x圖裡可以看到有426KB的RAM，而ROM這邊猜測是使用MCU的ROM~

當然還有其他周邊功能這邊先不介紹~

講了這麼多其實就是想要說SoC裡面都會有ROM跟RAM而且大小都很小，這些都是在為後面要講的內容做鋪陳!

Ref:
* [AM62x Sitara™ Processors datasheet (Rev. B)](https://www.ti.com/lit/ds/symlink/am623.pdf?ts=1689052297134&ref_url=https%253A%252F%252Fwww.ti.com%252Fproduct%252Fko-kr%252FAM623%253FkeyMatch%253DAM623%2526tisearch%253Dsearch-everything%2526usecase%253DGPN-ALT)
* [AM3358 Product details](https://www.ti.com/product/AM3358?utm_source=google&utm_medium=cpc&utm_campaign=epd-null-null-prodfolderdynamic-cpc-pf-google-tw_int&utm_content=prodfolddynamic&ds_k=DYNAMIC+SEARCH+ADS&dcm=yes&gclid=CjwKCAjw2K6lBhBXEiwA5RjtCYMJmdRC7dxp2HWxFUMx4H3_q9a3HExM82jFwtwUrQHWWEVI3-amqRoC83AQAvD_BwE&gclsrc=aw.ds#features)

## 分辨開發版型號 第幾版
對嵌入式系統有基礎了瞭解接下來，就真的要實戰開始使用SBC SK-AM62。

一般來說拿到開發板最快的方式就是去參考官方的技術文件Quick Guide~

從官方文件中得知要讓SBC開機有分很多種模式，而且板子的版本不同設定方式也不一樣，所以在設定開機模式之前先確認自己的開發版是哪一個版本，參考相關技術文件時要對應正確的版本。

最快簡單的方式直接看板子上的型號來確認版本~如下圖所示
![SK_AM62x_E2](/assets/images/SK_AM62x_E2.png)

PROC114E2就是E2版本其他以此類推~

## 開機模式的選擇
根據官方的Technical Reference Manual(TRM)的說明，開機模式有以下幾種如圖所示
![SK_AM62x_Boot_mode](/assets/images/SK_AM62x_Boot_mode.png)

常見的storage開機方式有eMMC、SD Card和USB開機。這邊將以E3板子的SD card開機為範例~
那開機模式有這麼多種，該如何選擇與設定?官方文件提到需要從板子上的switch開關來做設定如圖所示

![am62x_e2_sd_boot_setting](/assets/images/am62x_e2_sd_boot_setting.png)

但是這個圖是for E2板子使用的，我們要看的是E3，但是E3沒有範例，所以我們只好查他的設定原理~

圖中可以得知switch的開關就是用來設定bit的0、1值，我查了TRM找到了這個表格如圖所示。

![am62x_boot_mode_mapping](/assets/images/am62x_boot_mode_mapping.png)

竟然知道開關就是設定這些bit的數值，那我們要看我們E3板子的電路圖得知開關線路的接法如圖所示。

![am62x_boot_mode_switch](/assets/images/am62x_boot_mode_switch.png)

圖裡寫得很清楚SW1從最左邊的開關1開始就是設定BOOTMODE0bit，其餘以此類推~

那我們從低位元開始看起，PLL Config如圖所示。

![am62x_pll_clock](/assets/images/am62x_pll_clock.png)

這邊我直接設定手冊的default值25MHz (011)。B2~B0

再來Primary Boot Mode設定如圖所示。

![am62x_Primary_Boot_Mode_Selection](/assets/images/am62x_Primary_Boot_Mode_Selection.png)

![am62x_sd_card_boot](/assets/images/am62x_sd_card_boot.png)

SD Card的設定是Port=1 FS/Raw=0 1x01000 x代表don't care，你要1或0都可以。

最後備援開機模式，我選擇關閉如圖所示。

![am62x_Backup_Mode_Selection](/assets/images/am62x_Backup_Mode_Selection.png)

所以是000的設定。

所以全部合起來從高位元到低位元依序是~x0001x01000011 x是don't care。

![am62x_switch_sdcard_boot](/assets/images/am62x_switch_sdcard_boot.png)

Ref: [SK-AM62: FAQ: How do I set the DIP switches to get a certain boot mode?](https://e2e.ti.com/support/processors-group/processors/f/processors-forum/1126872/faq-sk-am62-faq-how-do-i-set-the-dip-switches-to-get-a-certain-boot-mode)

## 結語
研究原理探究跟因~