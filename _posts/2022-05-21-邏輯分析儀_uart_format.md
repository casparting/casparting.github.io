---
title: 用邏輯分析儀抓取console port傳送的資料格式與波形
excerpt: >
  用邏輯分析儀分析Console Port傳送字元的資料格式與波形。
categories:
  - Embedded System
tags:
  - Linux
  - Debian
  - Saleae Logic
  - Raspberry Pi
  - Embedded System
  - UART
---
# 前言
用邏輯分析儀分析console port傳送字元的資料格式與波形。
# 準備
須準備一些小零件與安裝一些軟體如下所列：
* 硬體
    - Host: PC/NB
    - USB Saleae Logic Analyzer,24M 8CH
    - CP2102 USB To TTL Convertor Module
    - Target: Raspberry Pi 2 Mode B (或其他有console port的SBC)
* 軟體
    - minicom/putty/Tera Term
    - Saleae Logic Analyzer Software
    - CP2102 Driver

# 安裝軟體
請在Host端安裝以下軟體。
## minicom
&emsp;&emsp;在終端機使用超級使用者權限下達下面指令。
```
# apt-get install minicom -y
```
## Saleae Logic Analyzer Software
1. 請到[此連結](https://www.saleae.com/zh-tw/downloads/)下載for Linux的邏輯分析儀軟體。
2. 在終端機使用一般使用者下達下面指令。

### 給予執行權限

```
$ chmod +x Logic-2.3.53-master.AppImage
```
### 開始執行

```
$ ./Logic-2.3.53-master.AppImage
```
### 你的邏輯分析儀USB接上Host端
出現connecting表示成功如下圖所示
![logic connecting](/assets/images/logic_connect.png)
如果無法連接成功請參考[此篇文章](https://casparting.github.io/linux/saleae_logic_usb_connect/)來解決問題。

## CP2102 Driver
在Linux上通常已經支援CP2102 USB to TTL
而Windows上則需要安裝Driver請至[此連結](https://www.silabs.com/developers/usb-to-uart-bridge-vcp-drivers)下載與安裝
