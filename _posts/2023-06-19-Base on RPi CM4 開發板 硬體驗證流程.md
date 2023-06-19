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
  - UART
  - CAN Bus
  - SPI
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
例如HDMI、USB Port、Ethernet等等這種都是直接插入後透過terminal或桌面程式就可以知道硬體是否正常運作。
* HDMI: 有無畫面輸出
* USB: 是否接上滑鼠可以移動
* Ethernet: 接上有通的網路線是否可以連上網

但在這個驗證步驟時就遇到了USB與Ethernet不通的問題，這時候我們看一下kernel message跟使用usb tool來看usb device狀態。
```
# dmesg |grep -i USB
# lsusb
```
Ref:

[Linux USB Not Detected or Not Working?](https://www.makeuseof.com/tag/fix-usb-device-port-linux/)

[List devices on USB Hub](https://askubuntu.com/questions/1125603/list-devices-on-usb-hub)


發現沒有USB3.0的Hub，USB插上滑鼠也沒有新增USB device的message。

前面有提到公司是base on CM4 IO板子下去改的，線路跟硬體元件不完全一樣，所以接下來我們進到下一節Troubleshooting來說明如何除錯找到root cause。

板子還剩下LED燈與RS232和CAN BUS這種較不易測試的我們也一併在下一節Troubleshooting來說明如何除錯找到root cause。

## Troubleshooting
### 找出USB與Ethernet不通的問題
如同剛剛所說的公司板子硬體不完全一樣，同樣的firmware之下CM4 IO Board可以使用USB，到公司的板子卻無法使用，所以我就開始查公司提供的硬體電路圖，我從電路圖下去搜尋找到USB接腳位置，發現USB的接腳接去一個PCIE轉USB的晶片，我在搜尋PCIE接腳，又發現PCIE接腳接去CM4，礙於公司保密協定，所以我不能把公司硬體電路圖放上來，我們拿CM4 IO Board電路圖來說如下所示
![CM4_IOBOARD_IO_PIN](/assets/images/CM4_IOBOARD_IO_PIN.png)

上圖這是Module1B Compute Module 4(CM4)的PCIE接腳的部分

我們來看看PCIE從CM4接去了哪邊，請在電路圖文檔搜尋任一PCIE接腳名稱，如PCIE_RX_P。

搜尋後發現另一個出現PCIE_RX_P是PCIE的Connector如下圖所示
![CM4_IOBOARD_PCIE](/assets/images/CM4_IOBOARD_PCIE.png)

上圖是CM4 IO Board上的PCIE Connector

所以我從電路圖比對得知，CM4 IO Board有一個PCIE插槽，在我公司的線路圖上刪除了PCIE插槽，而直接把PCIE轉USB的晶片放上去做連接轉換。

那此時我們可以用lspci指令來看PCI是否有偵測到
```
# lspci |grep -i usb
```

ref: [usb 3.0 pci card not working](https://bbs.archlinux.org/viewtopic.php?id=195950)

經過確認後是有發現USB3.0的PCIE，但是lsusb沒有抓到。

有了大概的方向之後依照過去的經驗，有新增晶片元件，我們就要確認是否有上晶片的驅動程式，所以我開始看Linux kernel的config檔(驅動程式組態設定檔)，通常config可以在/boot/config-$kernel_version可以找到，而CM4我在這邊沒有看到config，所以我們用另一個方式如下。
```
$ sudo modprobe configs
$ zcat /proc/config.gz > .config
$ cat .config
```
ref:

[不會交叉編譯也可以自己編譯核心](https://ithelp.ithome.com.tw/articles/10238927)

[What is /proc/config.gz?](https://blog.fpmurphy.com/2015/10/what-is-procconfig-gz.html)

然後再使用grep搜尋文檔內的關鍵字
```
cat .config | grep PCI
cat .config | grep XHCI
```
經過確認後kernel option是有選到的，我們再來看一下kernel message如下指令
```
# dmesg |grep pci
pci 0000:01:00.0: xHCI HW not ready after 5 sec (HC bug?) status = 0x801
```
發現了錯誤訊息，經網路搜尋得知[USB3 stopped working with 5.13.6-arch1-1 again](https://bbs.archlinux.org/viewtopic.php?id=268407)，這個晶片需要安裝firmware。

燒入的方式請參考[此連結](https://github.com/markusj/upd72020x-load)，firrmware下載請參考[此連結](https://github.com/denisandroid/uPD72020x-Firmware)

這邊安裝完後就順利抓到USB3.0，並且連同Ethernet也起來了，用同樣的方式去查找電路圖，發現Ethernet是USB3.0下去轉的，所以USB3.0通了網路也通了。



### RS232/RS485打通的方法
為了確認RS232設備是否有抓到，一開始我們可以先下這個指令看
```
$ ls -l /dev/ttyA*
crw-rw---- 1 root dialout 204, 64 Jun 16 11:30 /dev/ttyAMA0
```
看看有幾個ttyACMX，開通了幾個UART，這邊我只有看到一個UART，而從電路圖上看我公司板子還有額外使用UART2, UART3, UART4，我們知道晶片的GPIO會有Alternative function pin，也就是說一個pin腳，他可以設定成不同的作用或功能，我們看下圖CM4 Datasheet
![CM4_Alternative_function_assignments](/assets/images/CM4_Alternative_function_assignments.png)

從上圖中我們可以得知GPIO0可以設定成SDA0, SA5, PCLK, SPI3_CE0_N, TXD2, SDA6，不同的通訊協定，看當初產品設計有哪些周邊功能，來規劃每一個GPIO最為哪一個通訊協定，那我們只要看電路圖就可以得知那些腳為用到了哪些功能。

既然我們知道pin腳位功能可以設定，那接下來要講怎麼設定，一般來說要去改device tree的硬體描述設定，但RPi有另位做了overlap的機制，讓我們開發者只要修改config.txt檔，加上簡短幾個描述，就會把他原本又已經編譯好的device tree blob(dtb)，overlap進去原本的dtb。

那我們看一下raspberry overlap的[README](https://github.com/raspberrypi/firmware/blob/master/boot/overlays/README)從這裡我們可以知道要怎麼在/boot/config.txt裡面做設定，而[這裡](https://github.com/raspberrypi/firmware/tree/master/boot/overlays)可以快速知道有哪些已經做好的dtb可以直接使用像是底下這圖。

![CM4_UART_DTB](/assets/images/CM4_UART_DTB.png)

也可以使用底下指令來快速查有哪些UART的設定
```
$ dtoverlay -a | grep uart
  midi-uart0
  midi-uart1
  midi-uart2
  midi-uart3
  midi-uart4
  midi-uart5
  miniuart-bt
  qca7000-uart0
  uart0
  uart1
  uart2
  uart3
  uart4
  uart5
```
ref: [树莓派 4 UART 多串口配置通信](https://zhuanlan.zhihu.com/p/429819646)

UART他已經做好dtb，所以我們可以使用底下指令來確認UART的參數設定或是看上面的README
```
$ sudo dtoverlay -h uart2
Name:   uart2

Info:   Enable uart 2 on GPIOs 0-3. BCM2711 only.

Usage:  dtoverlay=uart2,<param>

Params: ctsrts                  Enable CTS/RTS on GPIOs 2-3 (default off)
```
從剛剛上面的Alternative function pin中的TXD2, RXD2, CTS2即是代表UART2的腳位，而參數Params: ctsrts就是用來設定你的UART是只有TX/RX還是RS232有CTS腳位。

知道語法後我們依照公司的電路圖在config.txt加入底下語法來開啟相對應的UART Port。
```
# Enable UART Port
dtoverlay=uart2,ctsrts
dtoverlay=uart3,ctsrts
dtoverlay=uart4
```

設定完成後重新開機

我們在下達底下指令觀看結果
```
$ ls -l /dev/ttyA*
crw-rw---- 1 root dialout 204, 64 Jun 16 11:30 /dev/ttyAMA0
crw-rw---- 1 root dialout 204, 65 Jun 16 11:17 /dev/ttyAMA1
crw-rw---- 1 root dialout 204, 66 Jun 16 11:17 /dev/ttyAMA2
crw-rw---- 1 root dialout 204, 67 Jun 16 11:17 /dev/ttyAMA3
```
!!補充
雖然UART打開了，但我卻發現dmesg出現了錯誤訊息，我們底下再來看看
```
[   19.566331] pinctrl-bcm2835 fe200000.gpio: pin gpio9 already requested by fe201800.serial; cannot claim for fe204000.spi
[   19.566369] bcm2835_vc_sm_cma_probe: Videocore shared memory driver
[   19.566370] pinctrl-bcm2835 fe200000.gpio: pin-9 (fe204000.spi) status -22
[   19.566388] pinctrl-bcm2835 fe200000.gpio: could not request pin 9 (gpio9) from group gpio9  on device pinctrl-bcm2711
[   19.566397] [vc_sm_connected_init]: start
[   19.566407] spi-bcm2835 fe204000.spi: Error applying setting, reverse things back
```
從錯誤訊息來看猜測是SPI與UART發生衝突(GPIO9)，這時候我就去查config.txt檔，沒有用到SPI的參數，所以Google搜尋相關問題查到[
Setting alternate function for GPIO pins](https://forums.raspberrypi.com/viewtopic.php?t=32160)裡面說換另一個GPIO功能需要把driver modulr給關閉，如下指令
```
# cat blacklist spi-bcm2708 > /etc/modprobe.d/raspi-blacklist.conf
```
再重新開機問題就解決了，目前初步猜測是driver有用到SPI，詳細root cause需要花時間再下去看。
### CAN BUS打通的方法
按照上面同樣的方法，先看硬體電路圖來確認CAN BUS的接線與使用到的compoment有哪些，確認後知道有使用了一顆SPI轉CAN BUS的晶片，然後這邊我也確認過Linux Kernel Option有開啟了，再來就是RPi這邊要開啟SPI interface，一樣進入到/boot/config.txt檔內，新增底下語法
```
dtparam=spi=on
```

## 結語
硬體驗證就是這麼簡單~