---
title: Base on RPi CM4 開發板 硬體驗證流程 教學 步驟 硬體驗證工程師 BSP工程師
excerpt: >
  公司base on RPi CM4做了一塊開發板，板子拿到當然要先燒入image再做hardware驗證，在104上我們偶爾在找工作時會看到職缺工作內容要協助硬體驗證，所以底下將寫下整個驗證的流程。
categories:
  - Embedded System
tags:
  - Raspberry Pi
  - Linux
  - SBC
  - Embedded System
  - CM4
  - IoT
---
## 前言
當拿到公司自己Layout的開發板(SBC)的時候，或購買外面現成的SBC，以公司的角度我們一開始應該先做硬體驗證，確保每一個腳位或功能元件都能正常運作，尤其是公司自己Layout的板子，因為是客製化後的，硬體compoment有新增或刪減，Layout線路也不完全一樣，所以更需要做硬體驗證，如果發現功能不正常，我們就要找到root cause看是firmware需要更正，或是硬體線路有誤，本篇內容將base on RPi CM4來帶各位一步一步瞭解硬體驗證的流程與步驟，驗證完了才能開始實現我們產品的功能。
## 硬體驗證須具備哪些技能或知識
* 硬體
    - 會看硬體電路圖
    - 測量儀器的使用(邏輯分析儀)
    - 基本通訊協定常識(UART, I2C, etc.)
* 軟體
    - 燒入firmware
    - BSP的移植
    - 修改Device tree
    - 修改Linux kernel
    - 硬體測試程式的撰寫

## 快速認識CM4開發板
拿到任何板子或需要瞭解晶片時可以先看datasheet的Product Brief介紹，可以讓我們快速瞭解其功能。
### Specification
* CM4 socket: suitable for all variants of Compute Module 4
* Standard Raspberry Pi HAT connectors with PoE support
* Standard PCIe Gen 2 x1 socket
* Real-time clock (RTC) with battery backup
* Dual HDMI connectors
* Dual MIPI camera connectors
* Dual MIPI display connectors
* Gigabit Ethernet socket supporting PoE HAT
* On-board USB 2.0 hub with 2 USB 2.0 connectors
* SD card socket for Compute Module 4 variants without eMMC
* Support for programming eMMC variants of Compute Module 4
* PWM fan controller with tachometer feedback

![CM4](/assets/images/CM4.png)
上面這圖是CM4周邊介紹圖
Ref: [Raspberry Pi Compute Module 4 IO Board product brief](https://datasheets.raspberrypi.com/cm4io/cm4io-product-brief.pdf?_gl=1*w8gf2x*_ga*MTE0MTU3OTMwNi4xNjg2NjI2Mzc5*_ga_22FD70LWDS*MTY4Njk5NTI5Mi4xMC4xLjE2ODY5OTU3NzMuMC4wLjA.)

如果上圖有不知道的專業名稱就去Google搜尋，積極不斷學習才能進步!

下面再來看看不同型號的硬體差異，如果eMMC 0GB的話，表示它是用SD card。

![CM4_Catalog](/assets/images/CM4_Catalog.png)

上面這圖是CM4的型錄圖
Ref: [Compute Module 4 Product Brief](https://datasheets.raspberrypi.com/cm4/cm4-product-brief.pdf?_gl=1*4e82u6*_ga*MTE0MTU3OTMwNi4xNjg2NjI2Mzc5*_ga_22FD70LWDS*MTY4NjYyNjM4MS4xLjEuMTY4NjYyNjM4Mi4wLjAuMA..)

## RPi CM4燒入OS Image
!!注意只要硬體電路需要更改或插入接線時，請先不用讓板子上電!!避免接錯線路或碰觸到其他電路造成短路燒壞板子。

底下的燒入流程需先準備這些配備與軟體
* 硬體
    - Host PC: Windows(電腦主機的作業系統)
    - CM4 IO板以及CM4板
* 軟體
    - 使用Windows Installer(rpiboot)來安裝CM4的Driver
    - 使用Raspberry Pi Imager來安裝CM4的OS

### 步驟與流程
#### 1. CM4接入CM4 IO Board
請先把Compute Module 4(CM4)插入Compute Module 4 IO Board(CM4的IO板)下圖是CM4 IO板，圖中可以看到中間有Compute Module 4的字樣，這個區域就是讓你插入CM4。

![CM4_IOBOARD](/assets/images/CM4_IOBOARD.png)

上面這圖是CM4 IO板

![Compute_Module_4](/assets/images/Compute_Module_4.png)

上面這圖是CM4

![CM4_IOBOARD_J2_Focus](/assets/images/CM4_IOBOARD_J2_Focus.png)

上面這圖是CM4 IO Board上面的J2 Jumper腳，用來開啟或關閉一些功能。

![CM4_IOBOARD_J2](/assets/images/CM4_IOBOARD_J2.png)

上面這圖是J2的電路圖腳位

#### 2. 將J2的腳位2 nRPIBOOT與腳位1 短路
根據官網[Setting up the CMIO board](https://www.raspberrypi.com/documentation/computers/compute-module.html#setting-up-the-cmio-board)
的描述以及[Compute Module 4 IO Board Datasheet](https://datasheets.raspberrypi.com/cm4io/cm4io-datasheet.pdf)第七頁提到，需要把nRPIBOOT的腳位接地，讓原本default用eMMC開機的功能改成使用USB開機，並且讓eMMC做為USB Mass Storage Device給Host PC讀取。接地的方法我們可以使用杜邦線或是直接使用Jumper![Jumper](/assets/images/Jumper.png)把腳位1與腳位2短路就是接地。

#### 3. 連接USB線至Host PC與CM4 IO Board
接著繼續根據[Setting up the CMIO board](https://www.raspberrypi.com/documentation/computers/compute-module.html#setting-up-the-cmio-board)接續的步驟(Use a micro USB cable to connect the micro USB slave port J11 on IO board to the host device.)告訴我們使用USB micro線連接Host PC至 CM4 IO Board的J11，這時候我們需要知道J11的位置在板子的哪邊，只要是需要知道compoment的位置時，我們就需要看板子的PCB Layout圖，可以在回到[Compute Module 4 IO Board Datasheet](https://datasheets.raspberrypi.com/cm4io/cm4io-datasheet.pdf)第9頁即是PCB Layout圖，仔細看可以看到J11的位置如下圖
![J11_Position](/assets/images/J11_Position.png)
#### 4. 下載RPiBoot並安裝相關驅動程式
USB線接上後還先不用接上電源，請先下載[Windows Installer](https://github.com/raspberrypi/usbboot/raw/master/win32/rpiboot_setup.exe)(RPiBoot)並安裝驅動程式。
#### 5. CM4 IO Board接上電源
#### 6. 續第四點安裝後的RPiBoot執行它
執行它便會看到它開啟terminal並開始安裝驅動
，安裝完成之後可以發現Host PC抓到了CM4的USB裝置也就是eMMC。
#### 7. 下載Raspberry Pi Imager並安裝與使用
下載[Raspberry Pi Imager](https://www.raspberrypi.com/software/)並安裝，該軟體的作用是安裝CM4的OS。安裝好之後打開選擇你的裝置(為了避免安裝到錯誤的USB設備，此時建議不要插入其他USB Storage設備)與選擇你要安裝的OS，因為CM4 CPU是64 bit，所以OS我就選64 bit速度較快，如同上面所述，此時安裝的OS就是裝入到CM4的eMMC內。

!!補充：因CM4本身沒有拉USB Debug Port，只能透過GPIO拉Debug Port出來，所以我等等要透過SSH連線進入CM4來操控下達指令。
請點選Raspberry Pi Imager tool右下角的設定圖案。點選開啟SSH服務，並輸入使用者密碼用密碼來登入SSH，另外也可以先設定好WFI連線。
Ref: [How to SSH Into a Raspberry Pi for Remote Access](https://www.makeuseof.com/how-to-ssh-into-raspberry-pi-remote/)

等一段時間燒入完成後就可以斷電把J2的Jumper卸下來。然後再上電開機我們要用eMMC booting。

!!補充：有興趣的人也可以研究看看[Installing OS over the Network](https://www.raspberrypi.com/documentation/computers/getting-started.html#installing-over-the-network)

## 其他相關參考資料
[Compute Module 4 Extensions](https://github.com/raspberrypi/usbboot/blob/master/Readme.md#compute-module-4-extensions)包括recovery、rpi-imager-embedded、mass-storage-gadget、secure-boot-recovery等。

[Compute Module 4 IO Board](https://www.raspberrypi.com/products/compute-module-4-io-board/)

[CM4 Datasheets and Schematics](https://www.raspberrypi.com/documentation/computers/compute-module.html#datasheets-and-schematics)

## 硬體周邊元件驗證
我以公司板子為例，我們可以先從最簡單可以快速驗證的元件開始。
例如HDMI、USB Port、Ethernet這種都是直接插入後透過terminal或桌面程式就可以知道硬體是否正常運作。

## Troubleshooting

## 結語
硬體驗證就是這麼簡單~