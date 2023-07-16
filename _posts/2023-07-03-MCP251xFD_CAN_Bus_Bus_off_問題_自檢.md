---
title: MCP251xFD CAN Bus Bus off 問題 自檢
excerpt: >
  這邊將介紹MCP251xFD為例，怎麼除錯解決CAN Bus Bus off的問題。
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
本篇內容將針對 MCP251xFD CAN Bus在傳送與接收資料時發生Bus off可能的問題。
## 前提準備
我們假設已經設定好CAN Bus dtoverlay，也上好linux kernel driver，請參考這篇文章->[RPi CM4 SPI 轉 CAN Bus MCP251xFD 的使用 打通 CAN Bus 自檢](https://casparting.github.io/embedded%20system/RPi-CM4-SPI-%E8%BD%89-CAN-Bus-MCP251xFD-%E7%9A%84%E4%BD%BF%E7%94%A8-%E6%89%93%E9%80%9A-CAN-Bus-%E8%87%AA%E6%AA%A2/)<-
## cansend資料發生bus off
該設定的都設定了，但是發現CAN Bus在send資料收不到
```
$ cansend can0 001#112233445566778
```
用dmesg去看
```
$ dmesg|grep can
[    6.769105] mcp251xfd spi1.0 can0: MCP2517FD rev0.0 (-RX_INT -PLL +MAB_NO_WARN +CRC_REG +CRC_RX +CRC_TX +ECC -HD o:20.00MHz c:20.00MHz m:20.00MHz rs:8.50MHz es:8.33MHz rf:8.50MHz ef:8.33MHz) successfully initialized.
[  985.791141] IPv6: ADDRCONF(NETDEV_CHANGE): can0: link becomes ready
[ 1038.171626] can: controller area network core
[ 1038.185159] can: raw protocol
[ 1324.714168] mcp251xfd spi1.0 can0: bus-off
```

從上面的連結文章內我們知道當接收或傳送錯誤次數達到一定的數量時state就會變成bus-off的狀態，要回到Normal狀態比需要重新上下電或是重新up and down
```
$ sudo ip link set can0 down
```
在
```
$ sudo ip link set can0 up type can bitrate 1000000
```

這時候我仔細對了兩邊can bus的參數，確認bitrate設定一致。
## 用邏輯分析儀來分析can protocol
有時候軟體的錯誤訊息不會很多，這時候就需要硬體量測，這邊我就接了[邏輯分析儀](https://casparting.github.io/embedded%20system/%E9%82%8F%E8%BC%AF%E5%88%86%E6%9E%90%E5%84%80_uart_format/)來量測

從量測結果發現，資料有不斷地送出來，但是can format的ACK一直都是NACK，表示說資料送出去給對方，對方沒有收到沒有回ACK。

反之對方送data過來對方也會bus-off。

並且我還有測試loopback mode在這種情況之下又可以傳送與接收資料，所以內部晶片應該是沒有問題的。

這時候就可以懷疑我方與對方的bit rate是不是不一致，所以我用了邏輯分析儀的軟體內建的timing量測我方的bit rate，如下圖所示。
![can_bus_bitrate](/assets/images/can_bus_bitrate.png)

發現bit rate果然不是我設定的1MHz，這時候就可以反推，bit rate跟晶片的震盪器有關連性，所以我就去查oscillator的設定。

原來MCP2517FD的oscillator是外部的，所以要依照你硬體電路接的是多少頻率的oscillator，這邊我是接40MHz，所以RPi的設定參數要改/boot/config.txt檔案如下
```
dtoverlay=mcp251xfd,spi1-0,oscillator=40000000,interrupt=25
```
最後補上一張正確bit rate的圖如下
![can_bus_bitrate_correct](/assets/images/can_bus_bitrate_correct.png)


## 結語
硬體軟體除錯需要不少耐心與思考力~