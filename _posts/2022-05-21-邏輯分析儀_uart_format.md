---
title: 用邏輯分析儀抓取Console Port傳送的資料格式與波形
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
    - minicom/putty/Tera Term(序列埠軟體)
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

#### 給予執行權限

```
$ chmod +x Logic-2.3.53-master.AppImage
```
#### 開始執行

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

# 樹梅派環境建制

這邊我假定你已經做好了以下準備。
* 準備SD Card並燒入Raspberry Pi的OS image檔案。
* 使用raspi-config將serial console enable。[參考](https://dumbcatnote.blogspot.com/2020/04/raspberry-pi-enable-serial-port.html)
* 樹梅派已經可以開機進入桌面。

# 線路連接

### Rpi接至CP2102
依照Raspberry Pi的pin define如[連結](https://github.com/raspberrypi/documentation/blob/develop/documentation/asciidoc/computers/os/using-gpio.adoc)所示。拿起兩端母頭杜邦線將Rpi的Vcc、Gnd、Tx、Rx接至CP2102接法如下所示。

| Rpi | CP2102 |
|:---:|:---:|
| 5v | 5v |
| GND | GND |
| Tx | Rx |
| Rx | Tx |

比較要注意的是Tx是接Rx，而Rx是接Tx，資料才能由Tx端送給Rx端接收，反之亦然。
### Logic接至Rpi
這邊只要注意GND接至Rpi的GND，讓他們共地[參考電壓](https://www.quora.com/Why-it-is-necessary-to-common-all-the-ground-in-circuit)才有基準，其他的Tx,Rx與Vcc接至任一channle0~7都可以。

# 上電錄製資料

線路連接好之後，把Rpi接上電源，再把CP2102與Logic的USB端接上Host端並開啟序列埠軟體與Saleae Logic Analyzer Software。確認序列埠軟體可以與Rpi溝通下達指令之後，我們使用Saleae Logic Analyzer Software點選右上角綠色按鈕開始錄製，並在序列埠軟體上輸入j字元。
![input_j](/assets/images/minicom_j.png)
應當立即看到Saleae Logic Analyzer Software接收到data如下圖所示。
![0x6a](/assets/images/0x6a.png)
我們再去參考[ASCII的表](https://zh.wikipedia.org/wiki/ASCII)發現j字元所對應的十六進制數值就是6A如圖所示。
![ASCII_j](/assets/images/ascii_j.png)

# 結語

這邏輯分析儀可以到露天或蝦皮購買，三百塊以內卻可以用來分析很多data，像是UART, I2C等等，便宜又好玩^_^
