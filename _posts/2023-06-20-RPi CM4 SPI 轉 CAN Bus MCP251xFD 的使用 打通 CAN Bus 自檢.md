---
title: RPi CM4 SPI 轉 CAN Bus MCP251xFD 的使用 打通 CAN Bus 自檢
excerpt: >
  公司base on RPi CM4做了一塊開發板，板子拿到當然要先燒入image再做hardware驗證，這邊將介紹怎麼開啟SPI使用CAN Bus，並以MCP251xFD為例。
categories:
  - Embedded System
tags:
  - Raspberry Pi
  - Linux
  - SBC
  - Embedded System
  - CM4
  - IoT
  - CAN Bus
  - SPI
  - MCP251xFD
---
## 前言
本篇內容將base on RPi CM4來帶各位一步一步瞭解硬體驗證的流程與步驟，主要針對SPI與CAN Bus MCP251xFD 的啟用。
## 前提準備
在啟用CAN Bus之前我們要先把RPi架設好環境，請參考這篇文章->[Base on RPi CM4 開發板 硬體驗證流程 教學 步驟 硬體驗證工程師 BSP工程師](https://casparting.github.io/embedded%20system/Base-on-RPi-CM4-%E9%96%8B%E7%99%BC%E6%9D%BF-%E7%A1%AC%E9%AB%94%E9%A9%97%E8%AD%89%E6%B5%81%E7%A8%8B/)<-
## CAN BUS打通的方法
請先看硬體電路圖來確認CAN BUS的接線與使用到的compoment有哪些，確認後知道有使用了一顆SPI轉CAN BUS的晶片叫MCP251xFD，然後這邊我也確認過Linux Kernel Option(CONFIG_CAN_MCP251XFD)有開啟了，再來就是RPi這邊要啟用SPI interface，一樣進入到/boot/config.txt檔內，新增底下語法
```
dtparam=spi=on
```
或是下達底下指令
```
$ sudo raspi-config
```
然後選擇3 Interface Options再選擇I4 SPI，選擇Yes Enable SPI。

兩個方法二擇一，設定完成後都要重新開機。

重新開機後我們下達指令，正常來說會出現底下訊息
```
$ ls /dev/*spi*
/dev/spidev0.0 /dev/spidev0.1
```
但因為我剛剛前面再這篇文章開通USB時已經把SPI0的function換成了UART3、UART4，所以我這邊沒有看到。

而從電路圖上看SPI轉CAN Bus的晶片腳位，可以知道SPI使用的是CM4的SPI1_CE0_N(SPI1 chip select(Enable) 0)，而我們在看到下面這張圖

!!補充: SPI1表示bus 1，CE0=chip enable 0，又稱CS0=chip select 0。

![CM4_Alternative_function_assignments](/assets/images/CM4_Alternative_function_assignments.png)

找到SP1和SPI1_CE0的腳位，在GPIO18~GPIO21的ALT4，一樣接下來我們要修改/boot/config.txt來使GPIO18~21腳位功能定義為SPI1。

一樣我們先找尋設定SPI有哪些參數如下指令。
```
$ dtoverlay -a |grep spi
  anyspi
  enc28j60-spi2
  jedec-spi-nor
  mipi-dbi-spi
  sc16is752-spi0
  sc16is752-spi1
  sh1106-spi
  si446x-spi0
  spi-gpio35-39
  spi-gpio40-45
  spi-rtc
  spi0-0cs
  spi0-1cs
  spi0-2cs
  spi1-1cs
  spi1-2cs
  spi1-3cs
  spi2-1cs
  spi2-2cs
  spi2-3cs
  spi3-1cs
  spi3-2cs
  spi4-1cs
  spi4-2cs
  spi5-1cs
  spi5-2cs
  spi6-1cs
  spi6-2cs
  ssd1306-spi
  ssd1331-spi
  ssd1351-spi
```
我們再下達該指令來繳解spi1-1cs與spi1-2cs的定義與差異
```
$ dtoverlay -h spi1-1cs
Name:   spi1-1cs

Info:   Enables spi1 with a single chip select (CS) line and associated spidev
        dev node. The gpio pin number for the CS line and spidev device node
        creation are configurable.
        N.B.: spi1 is only accessible on devices with a 40pin header, eg:
              A+, B+, Zero and PI2 B; as well as the Compute Module.

Usage:  dtoverlay=spi1-1cs,<param>=<val>

Params: cs0_pin                 GPIO pin for CS0 (default 18 - BCM SPI1_CE0).
        cs0_spidev              Set to 'disabled' to stop the creation of a
                                userspace device node /dev/spidev1.0 (default
                                is 'okay' or enabled).
```
```
$ dtoverlay -h spi1-2cs
Name:   spi1-2cs

Info:   Enables spi1 with two chip select (CS) lines and associated spidev
        dev nodes. The gpio pin numbers for the CS lines and spidev device node
        creation are configurable.
        N.B.: spi1 is only accessible on devices with a 40pin header, eg:
              A+, B+, Zero and PI2 B; as well as the Compute Module.

Usage:  dtoverlay=spi1-2cs,<param>=<val>

Params: cs0_pin                 GPIO pin for CS0 (default 18 - BCM SPI1_CE0).
        cs1_pin                 GPIO pin for CS1 (default 17 - BCM SPI1_CE1).
        cs0_spidev              Set to 'disabled' to stop the creation of a
                                userspace device node /dev/spidev1.0 (default
                                is 'okay' or enabled).
        cs1_spidev              Set to 'disabled' to stop the creation of a
                                userspace device node /dev/spidev1.1 (default
                                is 'okay' or enabled).
```

從原文我們可以得知，spi1-1cs這表代表sp1 bus, 開啟使用1個chip select，所以params只有cs0_pin可以使用，而spi1-2cs則代表sp1 bus, 開啟使用2個chip select分別是cs0與cs1，所以params就有cs0_pin與cs1_pin來設定他們的GPIO腳位。

瞭解之後我們開啟/boot/config.txt，寫入以下內容開啟功能
```
# Change SPI Chip Select Pin
dtoverlay=spi1-1cs,cs0_pin=18
```
上面的意思表示開啟spi1 bus並使用一個cs，唯一的cs0他的pin腳位是用GPIO18(這邊端看電路圖怎麼接線的)

重新開機再用底下指令確認是否有抓到spi1
```
$ ls /dev/*spi*
```

ref: [Raspberry Pi SPI and I2C Tutorial](https://learn.sparkfun.com/tutorials/raspberry-pi-spi-and-i2c-tutorial/all)


## 結語
硬體驗證就是這麼簡單有趣~