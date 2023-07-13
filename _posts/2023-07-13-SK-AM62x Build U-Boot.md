---
title: SK-AM62x Build U-Boot
excerpt: >
  介紹SK-AM62x 編譯bootloader。
categories:
  - Embedded System
tags:
  - SK-AM62x
  - Linux
  - SBC
  - Embedded System
  - U-Boot
---
## 前言
本篇內容介紹 SK-AM62x 編譯source code裡的U-Boot。
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

## 編譯 u-boot
官方說明編譯u-boot有兩種方式
* Using the SDK top level Makefile
* Using the U-Boot top level Makefile

兩者的差異是SDK top level Makefile比較簡單方便使用，但是客製化設定就相對比較缺乏~

底下先介紹SDK top level的方式

### 編譯 U-Boot 使用 SDK Top Level Makefile
請輸入以下指令
```
$ cd ti-processor-sdk-linux-am62xx-evm-08.06.00.42/
$ make u-boot sysfw-image
```

如果編譯上有錯誤請看下面的[Troubleshooting](#troubleshooting)

編譯完之後主要會產生三個重要檔案tiboot3.bin、tispl.bin以及u-boot.img

```
~/ti-processor-sdk-linux-am62xx-evm-08.06.00.42/board-support/k3-image-gen-2022.01 $ ls -l tiboot3*
-rw-r--r-- 1 caspar caspar 335035 Jul 13 13:51 tiboot3-am62x-hs-fs-evm.bin
lrwxrwxrwx 1 caspar caspar     27 Jul 13 13:51 tiboot3.bin -> tiboot3-am62x-hs-fs-evm.bin
```
```
~/ti-processor-sdk-linux-am62xx-evm-08.06.00.42/board-support/u-boot_build/a53$ ls -l tispl.bin u-boot.img
-rw-r--r-- 1 caspar caspar 961988 Jul 13 13:51 tispl.bin
-rw-r--r-- 1 caspar caspar 894604 Jul 13 13:51 u-boot.img
```

### 編譯 U-Boot 使用 U-Boot Top Level Makefile

SDK載下來之後，U-Boot的source code放在這底下
```
~/ti-processor-sdk-linux-am62xx-evm-08.06.00.42/board-support$ cd u-boot-2021.01+gitAUTOINC+2ee8efd654-g2ee8efd654/
```
底下你會看到UBOOT_DIR/即代表上面這個U-Boot source code的路徑

根據這篇AR62x boot flow的文章介紹，我們得知AM62x的U-Boot Image跑在AM62x R5和ARM64核心，所以分別需要32bit和64bit的ARM cross-compile (toolchans) 來編譯U-Boot，toolchans 一樣已經包在SDK裡面了。

設定toolchain的路徑到環境變數中，稍後在編譯的時候才找的到cross-compile。

```
$ export PATH=$PATH:<SDK_INSTALL_DIR>/linux-devkit/sysroots/x86_64-arago-linux/usr/bin
$ export TI_SECURE_DEV_PKG=<SDK_INSTALL_DIR>/board-support/core-secdev-k3
```

也就是

```
$ export PATH=$PATH:/home/caspar/ti-processor-sdk-linux-am62xx-evm-08.06.00.42/linux-devkit/sysroots/x86_64-arago-linux/usr/bin
$ export TI_SECURE_DEV_PKG=/home/caspar/ti-processor-sdk-linux-am62xx-evm-08.06.00.42/board-support/core-secdev-k3
```

#### 編譯 R5 Image
```
$ cd <UBOOT_DIR>/
$ export ARCH=arm
$ export CROSS_COMPILE=arm-none-linux-gnueabihf-
$ make am62x_evm_r5_defconfig O=build/r5
$ make O=build/r5
$ cd ../k3-image-gen-<version>/
$ make SOC=<SOC> SOC_TYPE=<SOC_TYPE> SBL=<UBOOT_DIR>/build/r5/spl/u-boot-spl.bin SYSFW_DIR=../prebuilt-images
```
SOC是am62x SOC_TPYE是gp

結果如下
```
$ make SOC=am62x SOC_TYPE=gp SBL=/home/caspar/ti-processor-sdk-linux-am62xx-evm-08.06.00.42/board-support/u-boot-2021.01+gitAUTOINC+2ee8efd654-g2ee8efd654/build/r5/spl/u-boot-spl.bin SYSFW_DIR=../prebuilt-images
./scripts/gen_x509_combined_cert.sh -b /home/caspar/ti-processor-sdk-linux-am62xx-evm-08.06.00.42/board-support/u-boot-2021.01+gitAUTOINC+2ee8efd654-g2ee8efd654/build/r5/spl/u-boot-spl.bin -l 0x43c00000 -s ../prebuilt-images/ti-fs-firmware-am62x-gp.bin -m 0x40000 -c "" -d out/soc/am62x/evm/combined-tifs-cfg.bin -n 0x67000 -t out/soc/am62x/evm/combined-dm-cfg.bin -y 0x43c3a800 -k ti-degenerate-key.pem -r 1 -o tiboot3-am62x-gp-evm.bin
Certificate being generated :
SUCCESS: Image tiboot3-am62x-gp-evm.bin generated.
```
#### 編譯 AM64 Image
```
$ cd <UBOOT_DIR>/
$ export ARCH=arm
$ export CROSS_COMPILE=aarch64-none-linux-gnu-
$ make am62x_evm_a53_defconfig O=build/arm64
$ make ATF=../prebuilt-images/bl31.bin TEE=../prebuilt-images/bl32.bin DM=../prebuilt-images/ipc_echo_testb_mcu1_0_release_strip.xer5f O=build/arm64
```

結果如下
```
...
FIT description: Configuration to load ATF and SPL
Created:         Thu Jul 13 16:05:19 2023
 Image 0 (atf)
  Description:  ARM Trusted Firmware
  Created:      Thu Jul 13 16:05:19 2023
  Type:         Firmware
  Compression:  uncompressed
  Data Size:    0 Bytes = 0.00 KiB = 0.00 MiB
  Architecture: AArch64
  OS:           ARM Trusted Firmware
  Load Address: 0x9e780000
 Image 1 (tee)
  Description:  OPTEE
  Created:      Thu Jul 13 16:05:19 2023
  Type:         Trusted Execution Environment Image
  Compression:  uncompressed
  Data Size:    0 Bytes = 0.00 KiB = 0.00 MiB
 Image 2 (dm)
  Description:  DM binary
  Created:      Thu Jul 13 16:05:19 2023
  Type:         Firmware
  Compression:  uncompressed
  Data Size:    0 Bytes = 0.00 KiB = 0.00 MiB
  Architecture: Unknown Architecture
  OS:           Unknown OS
  Load Address: 0x89000000
 Image 3 (spl)
  Description:  SPL (64-bit)
  Created:      Thu Jul 13 16:05:19 2023
  Type:         Standalone Program
  Compression:  uncompressed
  Data Size:    318297 Bytes = 310.84 KiB = 0.30 MiB
  Architecture: AArch64
  Load Address: 0x80080000
  Entry Point:  0x80080000
 Image 4 (k3-am625-sk.dtb)
  Description:  k3-am625-sk
  Created:      Thu Jul 13 16:05:19 2023
  Type:         Flat Device Tree
  Compression:  uncompressed
  Data Size:    20754 Bytes = 20.27 KiB = 0.02 MiB
  Architecture: ARM
 Default Configuration: 'k3-am625-sk.dtb'
 Configuration 0 (k3-am625-sk.dtb)
  Description:  k3-am625-sk
  Kernel:       unavailable
  Firmware:     atf
  FDT:          k3-am625-sk.dtb
  Loadables:    tee
                dm
                spl
  MKIMAGE tispl.bin
FIT description: Configuration to load ATF and SPL
Created:         Thu Jul 13 16:05:19 2023
 Image 0 (atf)
  Description:  ARM Trusted Firmware
  Created:      Thu Jul 13 16:05:19 2023
  Type:         Firmware
  Compression:  uncompressed
  Data Size:    0 Bytes = 0.00 KiB = 0.00 MiB
  Architecture: AArch64
  OS:           ARM Trusted Firmware
  Load Address: 0x9e780000
 Image 1 (tee)
  Description:  OPTEE
  Created:      Thu Jul 13 16:05:19 2023
  Type:         Trusted Execution Environment Image
  Compression:  uncompressed
  Data Size:    0 Bytes = 0.00 KiB = 0.00 MiB
 Image 2 (dm)
  Description:  DM binary
  Created:      Thu Jul 13 16:05:19 2023
  Type:         Firmware
  Compression:  uncompressed
  Data Size:    0 Bytes = 0.00 KiB = 0.00 MiB
  Architecture: Unknown Architecture
  OS:           Unknown OS
  Load Address: 0x89000000
 Image 3 (spl)
  Description:  SPL (64-bit)
  Created:      Thu Jul 13 16:05:19 2023
  Type:         Standalone Program
  Compression:  uncompressed
  Data Size:    318297 Bytes = 310.84 KiB = 0.30 MiB
  Architecture: AArch64
  Load Address: 0x80080000
  Entry Point:  0x80080000
 Image 4 (k3-am625-sk.dtb)
  Description:  k3-am625-sk
  Created:      Thu Jul 13 16:05:19 2023
  Type:         Flat Device Tree
  Compression:  uncompressed
  Data Size:    20754 Bytes = 20.27 KiB = 0.02 MiB
  Architecture: ARM
 Default Configuration: 'k3-am625-sk.dtb'
 Configuration 0 (k3-am625-sk.dtb)
  Description:  k3-am625-sk
  Kernel:       unavailable
  Firmware:     atf
  FDT:          k3-am625-sk.dtb
  Loadables:    tee
                dm
                spl
  MKIMAGE spl/u-boot-spl.multidtb.fit
  CAT     spl/u-boot-spl-dtb.bin
  COPY    spl/u-boot-spl.bin
  SYM     spl/u-boot-spl.sym
  COPY    u-boot.dtb
  MKIMAGE u-boot-dtb.img
===================== WARNING ======================
This board uses CONFIG_SPL_FIT_GENERATOR. Please migrate
to binman instead, to avoid the proliferation of
arch-specific scripts with no tests.
====================================================
  CFGCHK  u-boot.cfg
make[1]: Leaving directory '/home/caspar/ti-processor-sdk-linux-am62xx-evm-08.06.00.42/board-support/u-boot-2021.01+gitAUTOINC+2ee8efd654-g2ee8efd654/build/arm64'
```

一樣編譯完後的檔案~
tiboot3.bin
```
~/ti-processor-sdk-linux-am62xx-evm-08.06.00.42/board-support/k3-image-gen-2022.01 $ ls -l tiboot3*
-rw-r--r-- 1 caspar caspar 332446 Jul 13 15:57 tiboot3-am62x-gp-evm.bin
-rw-r--r-- 1 caspar caspar 335035 Jul 13 13:51 tiboot3-am62x-hs-fs-evm.bin
lrwxrwxrwx 1 caspar caspar     24 Jul 13 15:57 tiboot3.bin -> tiboot3-am62x-gp-evm.bin
```
tispl.bin
```
~/ti-processor-sdk-linux-am62xx-evm-08.06.00.42/board-support/u-boot-2021.01+gitAUTOINC+2ee8efd654-g2ee8efd654/build/arm64$ ls -l tispl.bin
-rw-r--r-- 1 caspar caspar 340408 Jul 13 16:05 tispl.bin
```
u-boot.img
```
~/ti-processor-sdk-linux-am62xx-evm-08.06.00.42/board-support/u-boot-2021.01+gitAUTOINC+2ee8efd654-g2ee8efd654/build/arm64$ ls -l u-boot.img
-rw-r--r-- 1 caspar caspar 894604 Jul 13 16:04 u-boot.img
```

## 更新 u-boot
接下來把上面編譯好的三個檔案依序放到sd card的/boot裡面即可

因為我是用WSL所以還需要多一個小步驟~
```
$ cd /mnt
$ sudo mkdir e
# sudo mount -t drvfs E: /mnt/e
```

然後依序把檔案copy到/mnt/e裡面~

最後記得要
```
$ sudo umount /mnt/e
```

Ref: [How can I access my USB drive from my Windows Subsystem for Linux -- Ubuntu distribution?](https://askubuntu.com/questions/1116200/how-can-i-access-my-usb-drive-from-my-windows-subsystem-for-linux-ubuntu-dist)

補充!這邊可能會有小疑惑，怎麼copy symbolic link過去不是link file，而是link file所指向的實體檔案複製過去，並且重新命名為 symbolic 的檔名。

這可以根據[GNU Doc](https://www.gnu.org/software/coreutils/manual/html_node/cp-invocation.html#cp-invocation)他提到~

> When copying to a symbolic link, cp follows the link only when it refers to an existing regular file.

cp一般使用不加特殊參數時，就會這樣複製~

### Troubleshooting

#### 編譯 U-Boot 出現的錯誤
編譯過程出現以下錯誤訊息
```
=============================
Building SYSFW Image
=============================
make[1]: Entering directory '/home/caspar/ti-processor-sdk-linux-am62xx-evm-08.06.00.42/board-support/k3-image-gen-2022.01'
Makefile:67: *** TI_SECURE_DEV_PKG must be set for HS, defaults will not work.  Stop.
make[1]: Leaving directory '/home/caspar/ti-processor-sdk-linux-am62xx-evm-08.06.00.42/board-support/k3-image-gen-2022.01'
Makefile:374: recipe for target 'sysfw-image' failed
make: *** [sysfw-image] Error 2
```

請輸入以下指令
```
$ export TI_SECURE_DEV_PKG=/home/caspar/ti-processor-sdk-linux-am62xx-evm-08.06.00.42/board-support/core-secdev-k3
```

再make一次
```
$ make u-boot sysfw-image
```

最後成功出現底下訊息
```
Certificate being generated :
SUCCESS: Image tiboot3-am62x-hs-fs-evm.bin generated.
rm out/soc/am62x/evm/sec-cfg.o out/soc/am62x/evm/pm-cfg.o out/soc/am62x/evm/rm-cfg.o out/soc/am62x/evm/board-cfg.o
make[1]: Leaving directory '/home/caspar/ti-processor-sdk-linux-am62xx-evm-08.06.00.42/board-support/k3-image-gen-2022.01'
```

#### 執行 make clean 出現的錯誤
執行make clean
```
$ make clean
```

出現底下錯誤

```
=================================
Cleaning Matrix GUI Browser
=================================
make[1]: Entering directory '/home/caspar/ti-processor-sdk-linux-am62xx-evm-08.06.00.42/example-applications/matrix-gui-browser-2.0'
/bin/sh: 18: export: Files/dotnet/:/mnt/c/Program: bad variable name
Makefile.build:10: recipe for target 'qmake' failed
make[1]: *** [qmake] Error 2
make[1]: Leaving directory '/home/caspar/ti-processor-sdk-linux-am62xx-evm-08.06.00.42/example-applications/matrix-gui-browser-2.0'
Makefile:136: recipe for target 'matrix-gui-browser_clean' failed
make: *** [matrix-gui-browser_clean] Error 2
```

其中這行
```
/bin/sh: 18: export: Files/dotnet/:/mnt/c/Program: bad variable name
```
追蹤程式找到了
```
~/ti-processor-sdk-linux-am62xx-evm-08.06.00.42/linux-devkit/environment-setup
```
第18列
```
export PATH=$SDK_PATH_NATIVE/usr/bin:$SDK_PATH_NATIVE/usr/bin/aarch64-linux:$PATH
```

我們看一下PATH裡面的內容
```
$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/usr/lib/wsl/lib:/mnt/c/Windows/system32:/mnt/c/Windows:/mnt/c/Windows/System32/Wbem:/mnt/c/Windows/System32/WindowsPowerShell/v1.0/:/mnt/c/Windows/System32/OpenSSH/:/mnt/c/Program Files/dotnet/:/mnt/c/Program Files/Docker/Docker/resources/bin:/mnt/c/Program Files/PuTTY/:/mnt/c/Program Files/usbipd-win/:/mnt/d/Users/caspar_su/AppData/Local/Microsoft/WindowsApps:/mnt/d/Users/caspar_su/AppData/Local/Programs/Microsoft VS Code/bin:/snap/bin
```
猜測因為路徑中有空白所以判斷出問題~

網路上搜尋找到[相關資料](https://gist.github.com/ilbunilcho/4280bd55a10cefef75e74986b6bff936)移除windows path from wsl

編輯wsl.conf
```
$ sudo vi /etc/wsl.conf
```
輸入以下內容
```
[interop]
appendWindowsPath = false
```
存檔關閉。

重啟wsl。請開啟PowerShell輸入以下指令。
```
> wsl.exe --shutdown
```
再打開wsl確認一次PATH環境變數
```
$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/usr/lib/wsl/lib:/snap/bin
```

最後成功make clean~


## 結語
編譯source code 是嵌入式開發工程師基本的技能~