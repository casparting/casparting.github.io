---
title: SK-AM62x Build Kernel
excerpt: >
  介紹SK-AM62x 編譯Linux Kernel。
categories:
  - Embedded System
tags:
  - SK-AM62x
  - Linux
  - SBC
  - Embedded System
  - Kernel
---
## 前言
本篇內容介紹 SK-AM62x 編譯source code裡的Kernel。
## 前提準備
* Host PC (Development Host)
* Host PC OS: Ubuntu 18.04 LTS
P.S. 官方不建議使用虛擬機，這邊我是使用WSL

## 下載與安裝SDK
根據TI官方[下載頁面](https://www.ti.com/tool/PROCESSOR-SDK-AM62X)，依照你的需求選擇適合的SDK，這邊我是下載PROCESSOR-SDK-LINUX-AM62X內的
ti-processor-sdk-linux-am62xx-evm-08.06.00.42-Linux-x86-Install.bin

在下載等待的同時我們先安裝編譯source code所需要的一些套件，指令如下
```
$ sudo apt update
$ sudo apt install build-essential bison flex libssl-dev libncurses-dev u-boot-tools
```

安裝好後ti-processor-sdk-linux-am62xx-evm-08.06.00.42-Linux-x86-Install.bin程式也下載好後，接下來要執行它，如下指令。
```
$ chmod +x ti-processor-sdk-linux-am62xx-evm-08.06.00.42-Linux-x86-Install.bin
$ ./ti-processor-sdk-linux-am62xx-evm-08.06.00.42-Linux-x86-Install.bin
```

注意!如果這邊出現圖形化GUI錯誤訊息，請參考這篇文章內介紹的WSL使用GUI介面的介紹~

安裝過程確認路徑後就下一步完成~

Ref: 
* [Download and Install the SDK](https://software-dl.ti.com/processor-sdk-linux/esd/AM62X/08_06_00_42/exports/docs/devices/AM62X/linux/Overview/Download_and_Install_the_SDK.html)
* [Basic Setup Steps](https://dev.ti.com/tirex/explore/node?node=A__APVGH4iNvyKxG.AeLQAVgg__linux_academy_am62x__XaWts8R__LATEST&search=am62x)

## 編譯 Linux Kernel
官方說明編譯Kernel有兩種方式
* Using the SDK top level Makefile
* Using the Kernel top level Makefile

兩者的差異是SDK top level Makefile比較簡單方便使用，但是客製化設定就相對比較缺乏~

底下先介紹SDK top level的方式

### 編譯 Kernel 使用 SDK Top Level Makefile
請輸入以下指令
```
$ cd ti-processor-sdk-linux-am62xx-evm-08.06.00.42/
$ make linux
$ sudo DESTDIR=<sdcard-mounting-point> make linux_install
```
如果編譯上有錯誤請看下面的[Troubleshooting](#troubleshooting)

### 編譯 Kernel 使用 Kernel Top Level Makefile

設定cross-compile到環境變數中~
```
$ export PATH=/home/caspar/ti-processor-sdk-linux-am62xx-evm-08.06.00.42/linux-devkit/sysroots/x86_64-arago-linux/usr/bin:$PATH
```

先使用make clean來檢查編譯是否正常，也順便清除檔案
```
$ cd board-support/linux-5.10.168+gitAUTOINC+2c23e6c538-g2c23e6c538/
$ make ARCH=arm64 CROSS_COMPILE=aarch64-none-linux-gnu- distclean
  CLEAN   arch/arm64/crypto
  CLEAN   arch/arm64/kernel/vdso
  CLEAN   arch/arm64/kernel
  CLEAN   certs
  CLEAN   drivers/firmware/efi/libstub
  CLEAN   drivers/scsi
  CLEAN   drivers/tty/vt
  CLEAN   drivers/video/logo
  CLEAN   kernel
  CLEAN   lib/raid6
  CLEAN   lib
  CLEAN   net/wireless
  CLEAN   usr
  CLEAN   arch/arm64/boot
  CLEAN   vmlinux.symvers modules-only.symvers modules.builtin modules.builtin.modinfo
  CLEAN   scripts/basic
  CLEAN   scripts/dtc
  CLEAN   scripts/genksyms
  CLEAN   scripts/kconfig
  CLEAN   scripts/mod
  CLEAN   scripts
  CLEAN   include/config include/generated arch/arm64/include/generated .config .version Module.symvers
```

接下來我們可以從既有的defconfig檔案，選擇適合我們的開發平台，不同的defconfig有不同的driver設定，可能有支援USB網路驅動程式，有的沒有支援，我們是使用TI SK-AM62x E3板子，所以通常也會有相對應該板子的defconfig，如果板子是客製化的則需要進去make menuconfig來作客製化設定，開關驅動程式。

使用特定的defconfig設定黨指令如下
```
$ make ARCH=arm64 CROSS_COMPILE=aarch64-none-linux-gnu- <defconfig>
```
至於有那些defconfig可以選擇

你可以打下面指令來尋找相關檔案
```
$ find ./ -name *_defconfig
./arch/mips/configs/cavium_octeon_defconfig
./arch/mips/configs/decstation_defconfig
./arch/mips/configs/generic_defconfig
./arch/mips/configs/decstation_r4k_defconfig
./arch/mips/configs/maltaaprp_defconfig
./arch/mips/configs/bcm63xx_defconfig
./arch/mips/configs/tb0226_defconfig
./arch/mips/configs/bcm47xx_defconfig
./arch/mips/configs/nlm_xlp_defconfig
./arch/mips/configs/cobalt_defconfig
./arch/mips/configs/loongson3_defconfig
./arch/mips/configs/lemote2f_defconfig
./arch/mips/configs/cu1000-neo_defconfig
./arch/mips/configs/ath25_defconfig
./arch/mips/configs/xway_defconfig
./arch/mips/configs/omega2p_defconfig
./arch/mips/configs/ci20_defconfig
./arch/mips/configs/mtx1_defconfig
./arch/mips/configs/bigsur_defconfig
./arch/mips/configs/rbtx49xx_defconfig
...
./arch/arm64/configs/tisdk_am62xx-evm_defconfig
...
```

使用TIw提供的defconfig
tisdk_[platformName]_defconfig

platformName: am64xx-evm for AM64x, and am65xx-evm for AM65x 以此類推

所以我們的平台是AM62x應選擇tisdk_am62xx-evm_defconfig，

為了確認以及驗證是否正確，我們用剛剛的指令去find找看看有沒有這個檔案

確認檔案路徑在底下
```
./arch/arm64/configs/tisdk_am62xx-evm_defconfig
```

所以我們輸入以下指令來載入tisdk_am62xx-evm_defconfig設定檔
```
make ARCH=[platform_chip] CROSS_COMPILE=[cross-compile-prefix] tisdk_am62xx-evm_defconfig
```
這邊要注意platform_chip就是要看tisdk_am62xx-evm_defconfig檔案的路徑是位於arch內的什麼路徑

我們發現他在arm64裡面，所以使用的晶片是arm64的

再來cross-compile-prefix我們要到/home/caspar/ti-processor-sdk-linux-am62xx-evm-08.06.00.42/linux-devkit/sysroots/x86_64-arago-linux/usr/bin底下ls確認compiler的名稱
```
$ ls
2to3                                  automake-1.16       ifnames                 qemu-riscv64
2to3-3.8                              autoreconf          ilk6x                   qemu-sh4
aarch64-none-linux-gnu-addr2line      autoscan            ivshmem-client          qemu-system-aarch64
aarch64-none-linux-gnu-ar             autoupdate          ivshmem-server          qemu-system-arm
aarch64-none-linux-gnu-as             bison               lastlog                 qemu-system-i386
aarch64-none-linux-gnu-c++filt        bison.real          libinfo6x               qemu-system-mips
aarch64-none-linux-gnu-cpp            bsdcat              libtoolize              qemu-system-mips64
aarch64-none-linux-gnu-g++
```
確認到compiler前戳名稱就是aarch64-none-linux-gnu-

所以最後下達的指令以我的為例~
```
$ make ARCH=arm64 CROSS_COMPILE=aarch64-none-linux-gnu- tisdk_am62xx-evm_defconfig
#
# configuration written to .config
#
```

客製化設定~請輸入以下指令
```
$ make ARCH=arm64 CROSS_COMPILE=aarch64-none-linux-gnu- menuconfig
```
![kernel_config_setting](/assets/images/kernel_config_setting.png)

進入GUI進行kernel的設定。

開始編譯Linux Kernel，注意後面的-j[num]，建議num輸入你CPU有幾核心
```
$ make ARCH=arm64 CROSS_COMPILE=aarch64-none-linux-gnu- Image -j12
...
  AS      .tmp_vmlinux.kallsyms1.S
  LD      .tmp_vmlinux.kallsyms2
  KSYMS   .tmp_vmlinux.kallsyms2.S
  AS      .tmp_vmlinux.kallsyms2.S
  LD      vmlinux
  SORTTAB vmlinux
  SYSMAP  System.map
  OBJCOPY arch/arm64/boot/Image
```

## 安裝Linux Kernel
```
$ cd /home/caspar/ti-processor-sdk-linux-am62xx-evm-08.06.00.42/board-support/linux-5.10.168+gitAUTOINC+2c23e6c538-g2c23e6c538
$ sudo cp arch/arm64/boot/Image <rootfs path>/boot
```

如果你跟我一樣使用WSL目前還沒有解決方法可以讓windows寫檔案到Linux檔案系統

所以只能把檔案複製起來放到linux系統中複製過去

底下wsl mount sd card但只能讀到fat格式的partition
```
$ cd /mnt
$ sudo mkdir e
$ sudo mount -t drvfs E: /mnt/e
$ sudo DESTDIR=/mnt/e make linux_install
```

使用[Ext2Read tool](https://sourceforge.net/projects/ext2read/)來從windows讀取linux檔案系統。(要用系統管理權限開啟) 這也只能讀取無法寫入。



## Troubleshooting

### 安裝 Kernel 出現的錯誤
安裝過程出現以下錯誤訊息 Input/output error
```
$ sudo DESTDIR=/mnt/e make linux_install
=======================================
Installing the Linux Kernel DTBs
=======================================
install -d /mnt/e/boot
===================================
Installing the Linux Kernel
===================================
install -d /mnt/e/boot
install /home/caspar/ti-processor-sdk-linux-am62xx-evm-08.06.00.42/board-support/linux-5.10.168+gitAUTOINC+2c23e6c538-g2c23e6c538/arch/arm64/boot/Image /mnt/e/boot
install /home/caspar/ti-processor-sdk-linux-am62xx-evm-08.06.00.42/board-support/linux-5.10.168+gitAUTOINC+2c23e6c538-g2c23e6c538/vmlinux /mnt/e/boot
install: error writing '/mnt/e/boot/vmlinux': Input/output error
Makefile:25: recipe for target 'linux_install' failed
make: *** [linux_install] Error 1
```
請檢查SD card內容是否已經裝滿了~

## 結語
編譯source code 是嵌入式開發工程師基本的技能~