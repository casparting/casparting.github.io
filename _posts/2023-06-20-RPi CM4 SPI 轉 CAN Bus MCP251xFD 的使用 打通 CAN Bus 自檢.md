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

接下來因為我們有用到SPI轉CAN Bus的晶片MCP251xFD，所以我們也來確認RPi的overlap是否有做好dtb。請輸入以下指令。
```
$ dtoverlay -a | grep mcp251
  mcp2515
  mcp2515-can0
  mcp2515-can1
  mcp251xfd
```
在輸入以下指令確認參數
```
$ dtoverlay -h mcp251xfd
Name:   mcp251xfd

Info:   Configures the MCP251XFD CAN controller family
        For devices on spi1 or spi2, the interfaces should be enabled
        with one of the spi1-1/2/3cs and/or spi2-1/2/3cs overlays.

Usage:  dtoverlay=mcp251xfd,<param>=<val>

Params: spi<n>-<m>              Configure device at spi<n>, cs<m>
                                (boolean, required)

        oscillator              Clock frequency for the CAN controller (Hz)

        speed                   Maximum SPI frequence (Hz)

        interrupt               GPIO for interrupt signal

        rx_interrupt            GPIO for RX interrupt signal (nINT1) (optional)

        xceiver_enable          GPIO for CAN transceiver enable (optional)

        xceiver_active_high     specifiy if CAN transceiver enable pin is
                                active high (optional, default: active low)
```

我們看到interrupt為GPIO interrupt的腳位，這時候就要去確認電路圖中CAN BUS interrupt的腳位是接到GPIO的腳位幾~這邊我是接到GPIO 25。
而oscillator的設定可以參考mcp251xfd的datasheet如下圖所示。

![MCP2517FD_oscillator](/assets/images/MCP2517FD_oscillator.png)

oscillator有40, 20, 4 MHz可以選擇。

在來的spi參數是要看SPI轉CAN Bus，是用到哪一個SPI Bus跟哪一個chip select，這邊我們是用SPI1 CS0所以參數就是spi1-0。
接下來到/boot/config.txt檔內加入底下設定。
```
# ENABLE SPI to CAN FD controller
dtoverlay=mcp251xfd,spi1-0,oscillator=20000000,interrupt=25
```
重新開機確認是否有出現can bus interface。下面看到can0即是。
```
$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc mq state DOWN group default qlen 1000
    link/ether e4:5f:01:62:78:fd brd ff:ff:ff:ff:ff:ff
3: can0: <NOARP,ECHO> mtu 16 qdisc noop state DOWN group default qlen 10
    link/can
4: wlan0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether e4:5f:01:62:78:fe brd ff:ff:ff:ff:ff:ff
    inet 192.168.84.28/24 brd 192.168.84.255 scope global dynamic noprefixroute wlan0
       valid_lft 3327sec preferred_lft 2877sec
    inet6 2401:e180:8832:ed70:6501:e74c:bed:64e2/64 scope global dynamic mngtmpaddr noprefixroute
       valid_lft 6927sec preferred_lft 6927sec
    inet6 fe80::2fce:86be:72a2:de91/64 scope link
       valid_lft forever preferred_lft forever
```

ref: 

[whats the difference between /dev/spidev0.0 and /dev/spidev0.1](https://forums.raspberrypi.com/viewtopic.php?t=277416)

[MCP2515 SPI setup](https://forums.raspberrypi.com/viewtopic.php?t=149192)

## CAN BUS 自我檢測方式
測試之前不了解CAN Bus可以先參考[此連結](http://wiki.csie.ncku.edu.tw/embedded/CAN)。

測試的方式可以先看你有幾個CAN Bus，如果只有一組CAN Bus可以用loop back的測試方式，直接Tx接Rx，也就是H、L相接。如果有兩組CAN Bus就H接H，L接L來互傳資料，要注意的是Tx與Rx都一定要接上線路讓他輸出與接收，否則會出現Bus-off的state暫時斷東訊。我們看底下突來簡單解釋。

![CAN_note_state](/assets/images/CAN_note_state.png)

上圖說明REC=Receive Error Counter，TEC=Transmit Error Counter，如果資料傳送不出去counter超過255，則會到Bus-off狀態，反之REC亦然。

那我這邊先說明只有一組CAN Bus的自檢方式。請先把CAN Bus上的H與L短路。接下來輸入以下指令。
啟用can 0並開啟loopback模式
```
$ sudo ip link set can0 down
$ sudo ip link set can0 type can loopback on
$ sudo ip link set can0 up type can bitrate 1000000
```
先運行can dump在背景等待接收傳送資料。
```
$ candump can0&
```
傳送資料出去會收到以下訊息
```
$ cansend can0 001#1122334455667788
can0  001   [8]  11 22 33 44 55 66 77 88
can0  001   [8]  11 22 33 44 55 66 77 88
```
關閉can 0與loopback模式
```
$ sudo ip link set can0 down
$ sudo ip link set can0 type can loopback off
$ sudo ip link set can0 up type can bitrate 1000000
```
ref: [CAN Bus in Linux](https://wiki.rdu.im/_pages/Application-Notes/Software/can-bus-in-linux.html)

## 結語
硬體驗證就是這麼簡單有趣~