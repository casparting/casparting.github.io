---
title: Nvidia Jetson Orin NX 開發環境建置
excerpt: >
  介紹Nvidia Jetson Orin NX開發版的使用與環境的建置。
categories:
  - Embedded System
tags:
  - Nvidia
  - Linux
  - SoM
  - Embedded System
  - Jetson
  - Orin NX
---
## 前言
Nvidia Jetson主要是用於AI邊緣或機器人的System on Module(SoM)，模組產品名稱包含Jetson Orin、Jetson Xavier、Jetson TX2、Jetson Nano，不同系列有不同的特色詳細可以參考這邊[NVIDIA Jetson](https://www.nvidia.com/en-us/autonomous-machines/embedded-systems/)。
## 前提準備

## 安裝Jetson Linux到 Nvidia Jetson Orin NX

根據[Nvidia手冊](https://docs.nvidia.com/jetson/archives/r35.3.1/DeveloperGuide/index.html#software-for-jetson-modules-and-developer-kits)使用NVIDIA SDK Manager來安裝Jetson Linux與相關compoment，SDK Manger可以參考[此手冊](https://docs.nvidia.com/sdk-manager/)。建議這邊Host PC distro用Ubuntu來使用SDK Manager，因為support的compoment比較多，不同版本的支援度請參考下圖。

![Nvidia_SDK_Manager_compatiblity](/assets/images/Nvidia_SDK_Manager_compatiblity.png)

這邊假設你已經架設好Host PC，接下來下載[SDK Manager](https://developer.nvidia.com/sdk-manager)到Host PC，並下指令。
```
$ sudo dpkg -i sdkmanager_1.9.2-10899_amd64.deb
```

在下底下指令安裝相依套件
```
$ sudo apt-get install -f
```
執行SDK Manager
```
$ sdkmanager
```

執行時出現錯誤訊息如下
```
$ /opt/nvidia/sdkmanager/sdkmanager-gui: error while loading shared libraries: libatk-bridge-2.0.so.0: cannot open shared object file: No such file or directory
```
請安裝以下套件
```
$ sudo apt-get install libatk-bridge2.0-0
```
如果用出現libgtk library找不到，請安裝以下套件
```
$ sudp apt-get install gnome-terminal
```
確認可以執行後請按照該網址來繼續下載與安裝SDK[Install Jetson Software with SDK Manager](https://docs.nvidia.com/sdk-manager/install-with-sdkm-jetson/index.html)

接下來要連接USB到target board，這邊是使用WSL Linux來建置環境與燒入firmware，WSL要連到target board我們要使用USB over IP來連接USB device。

請到[這邊](https://github.com/dorssel/usbipd-win/releases)先下載windows usbip程式。

## 客製化編譯Linux kernel
下載Board Support Package(BSP)[此連結](https://developer.nvidia.com/embedded/jetson-linux-archive)選擇你的版本，下載Driver Package (BSP) Sources。

解開BSP壓縮檔
```
$ tar -xfva public_sources.tbz2
```
解開kernel source壓縮檔
```
$ cd Linux_for_Tegra/source/public
$ tar –xjf kernel_src.tbz2
```
編譯之前預先安裝的套件
```
$ sudo apt install build-essential bc
```
下載cross-compiler Bootlin Toolchain gcc一樣在[此連結](https://developer.nvidia.com/embedded/jetson-linux-archive)中下載

我習慣把cross-compiler放在/opt/nvidia/toolchain/底下
也可以依照自喜喜好，但下面我會以此路徑為例。

編譯前的環境變數設定
```
$ export CROSS_COMPILE_AARCH64_PATH=/opt/nvidia/toolchain/
$ export CROSS_COMPILE_AARCH64=/opt/nvidia/toolchain/bin/aarch64-buildroot-linux-gnu-
```
建立kernel編譯後的輸出路徑
```
$ mkdir kernel_out
```
開始編譯
```
$ ./nvbuild.sh -o $PWD/kernel_out
```

出現以下錯誤訊息
```
$ ./nvbuild.sh -o $PWD/kernel_out
Building kernel-5.10 sources
make: Entering directory '/home/caspar/nvidia/nsync/Original_source_code/kernel/kernel-5.10'
make[1]: Entering directory '/home/caspar/nvidia/nsync/Original_source_code/kernel_out'
  GEN     Makefile
  HOSTCC  scripts/basic/fixdep
  HOSTCC  scripts/kconfig/conf.o
  HOSTCC  scripts/kconfig/confdata.o
  HOSTCC  scripts/kconfig/expr.o
  LEX     scripts/kconfig/lexer.lex.c
/bin/sh: 1: flex: not found
make[2]: *** [scripts/Makefile.host:9: scripts/kconfig/lexer.lex.c] Error 127
make[1]: *** [/home/caspar/nvidia/nsync/Original_source_code/kernel/kernel-5.10/Makefile:633: tegra_defconfig] Error 2
make[1]: Leaving directory '/home/caspar/nvidia/nsync/Original_source_code/kernel_out'
make: *** [Makefile:213: __sub-make] Error 2
make: Leaving directory '/home/caspar/nvidia/nsync/Original_source_code/kernel/kernel-5.10'
```
看到/bin/sh: 1: flex: not found，我們安裝套件如下指令。
```
$ sudo apt install flex
```

```
$ ./nvbuild.sh -o $PWD/kernel_out
Building kernel-5.10 sources
make: Entering directory '/home/caspar/nvidia/nsync/Original_source_code/kernel/kernel-5.10'
make[1]: Entering directory '/home/caspar/nvidia/nsync/Original_source_code/kernel_out'
  GEN     Makefile
  LEX     scripts/kconfig/lexer.lex.c
  YACC    scripts/kconfig/parser.tab.[ch]
/bin/sh: 1: bison: not found
make[2]: *** [scripts/Makefile.host:17: scripts/kconfig/parser.tab.h] Error 127
make[1]: *** [/home/caspar/nvidia/nsync/Original_source_code/kernel/kernel-5.10/Makefile:633: tegra_defconfig] Error 2
make[1]: Leaving directory '/home/caspar/nvidia/nsync/Original_source_code/kernel_out'
make: *** [Makefile:213: __sub-make] Error 2
make: Leaving directory '/home/caspar/nvidia/nsync/Original_source_code/kernel/kernel-5.10'
```
看到/bin/sh: 1: bison: not found，我們安裝套件如下指令。
```
$ sudo apt install bison
```
出現找不到openssl header file問題
```
  HOSTCC  scripts/kallsyms
  HOSTCC  scripts/sign-file
/home/caspar/nvidia/nsync/Original_source_code/kernel/kernel-5.10/scripts/sign-file.c:25:10: fatal error: openssl/opensslv.h: No such file or directory
   25 | #include <openssl/opensslv.h>
      |          ^~~~~~~~~~~~~~~~~~~~
compilation terminated.
make[2]: *** [scripts/Makefile.host:95: scripts/sign-file] Error 1
make[2]: *** Waiting for unfinished jobs....
  HOSTCC  scripts/sorttable
```
請安裝openssl development package。
```
$ sudo apt-get install libssl-dev
```

編譯完成之後，在Linux_for_Tegra/tools/底下使用script來燒入firmware，燒入firmware有兩個script可以使用二擇一。

底下我們只講使用initrd的方式來燒入，燒入較flash燒入來的方便。參考連結[在這裡](https://docs.nvidia.com/jetson/archives/r35.3.1/DeveloperGuide/text/SD/FlashingSupport.html#flashing-with-initrd)

預先準備
* 高品質的USB-C/micro-USB線
* Host PC使用NetworkManager
* 關閉自動掛載儲存設備
```
$ systemctl stop udisks2.service
```
* 安裝正確的相依套件
```
$ sudo tools/l4t_flash_prerequisites.sh # For Debian-based Linux
```

board name請參考[此連結](https://docs.nvidia.com/jetson/archives/r35.3.1/DeveloperGuide/text/IN/QuickStart.html#jetson-modules-and-configurations)表格內的Configuration欄位。
