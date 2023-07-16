---
title: MCP251xFD CAN Bus Loopback Mode SPI to CAN Bus 路徑
excerpt: >
  看到了一篇loopback mode的參考資料，對她的圖有點興趣，想量測看看loopback mode下CAN Bus的路徑資料怎麼傳送。
categories:
  - Embedded System
tags:
  - RPi
  - Linux
  - SBC
  - Embedded System
  - CAN Bus
  - SPI
  - MCP251xFD
---
## 前言
本篇內容將以 MCP251xFD 晶片為例，將其CAN Bus設定成loopback，並用邏輯分析儀來量測內部訊號，得知loopback mode下CAN Bus的資料怎麼傳與接收。
## 前提準備
我們假設已經設定好CAN Bus dtoverlay，也上好linux kernel driver，請參考這篇文章->[RPi CM4 SPI 轉 CAN Bus MCP251xFD 的使用 打通 CAN Bus 自檢](https://casparting.github.io/embedded%20system/RPi-CM4-SPI-%E8%BD%89-CAN-Bus-MCP251xFD-%E7%9A%84%E4%BD%BF%E7%94%A8-%E6%89%93%E9%80%9A-CAN-Bus-%E8%87%AA%E6%AA%A2/)<-
## 我看到的參考資料
資料連結如右[連結在這裡](https://e2e.ti.com/support/interface-group/interface/f/interface-forum/963383/tcan4550-q1-use-a-stand-alone-transceiver)
我們可以看到他的SPI to CAN Bus loopback圖data傳遞路徑如下
![TCAN4550_Loopback_Modes](/assets/images/TCAN4550_Loopback_Modes.png)

再來我這邊是用MCP2517FD這顆SPI to CAN Bus的晶片，我們看一下他的datasheet如下圖
![mcp2517fd_interfacing_with_mcu](/assets/images/mcp2517fd_interfacing_with_mcu.png)
![mcp2517fd_block_diagram](/assets/images/mcp2517fd_block_diagram.png)


我打算用邏輯分析儀來量測RXCAN與TXCAN量不量的到訊號，因為根據參考的圖，不論是internal或external loopback mode看起來再CANH與CANL之前的TX與RX會有資料傳遞，底下就來驗證看看。

## CAN Bus loopback mode設定
請參考底下指令設定
```
$ sudo ip link set can0 type can loopback on
$ sudo ip link set can0 up type can bitrate 1000000
```
dump資料看看有沒有接收與傳送資料
```
$ sudo candump can0&
```
傳送資料測試
```
$ cansend can0 001#1122334455667788
  can0  001   [8]  11 22 33 44 55 66 77 88
  can0  001   [8]  11 22 33 44 55 66 77 88
```
兩筆資料代表傳送與接收

## 邏輯分析儀量測
這邊我已經接好線路至SPI interface就是MCP2517FD上的SCK、SI、SO、nCS。
測出來在loopback mode之下是有SPI訊號的如下圖所示
![spi_to_can_bus_mcp2517fd](/assets/images/spi_to_can_bus_mcp2517fd.png)


表示loopback mode會在MCP2517FD內部自己loopback如下圖所示
![mcp2517fd_interfacing_with_mcu_modify](/assets/images/mcp2517fd_interfacing_with_mcu_modify.png)

## 結語
研究原理探究跟因~