---
title: Nvidia Jetson Orin NX 編譯 Linux Kernel
excerpt: >
  介紹Nvidia Jetson Orin NX 編譯 Linux Kernel。
categories:
  - Embedded System
tags:
  - Nvidia
  - Linux
  - SoM
  - Embedded System
  - Jetson
  - Orin NX
  - Kernel
---
## 前言
Nvidia Jetson主要是用於AI邊緣或機器人的System on Module(SoM)，模組產品名稱包含Jetson Orin、Jetson Xavier、Jetson TX2、Jetson Nano，不同系列有不同的特色詳細可以參考這邊[NVIDIA Jetson](https://www.nvidia.com/en-us/autonomous-machines/embedded-systems/)。
## 前提準備
* Host PC(Build Server)
* target board 開發版和SOM

## 客製化編譯Linux kernel
下載Board Support Package(BSP)[此連結](https://developer.nvidia.com/embedded/jetson-linux-archive)選擇你的版本，下載Driver Package (BSP) Sources。

解開BSP壓縮檔
```console
$ tar -xfva public_sources.tbz2
```
解開kernel source壓縮檔
```console
$ cd Linux_for_Tegra/source/public
$ tar –xjf kernel_src.tbz2
```
編譯之前預先安裝的套件
```console
$ sudo apt install build-essential bc
```
下載cross-compiler Bootlin Toolchain gcc一樣在[此連結](https://developer.nvidia.com/embedded/jetson-linux-archive)中下載

我習慣把cross-compiler放在/opt/nvidia/toolchain/底下
也可以依照自喜喜好，但下面我會以此路徑為例。

編譯前的環境變數設定
```console
$ export CROSS_COMPILE_AARCH64_PATH=/opt/nvidia/toolchain/
$ export CROSS_COMPILE_AARCH64=/opt/nvidia/toolchain/bin/aarch64-buildroot-linux-gnu-
```
建立kernel編譯後的輸出路徑
```console
$ mkdir kernel_out
```
開始編譯
```console
$ ./nvbuild.sh -o $PWD/kernel_out
```

出現以下錯誤訊息
```console
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
```console
$ sudo apt install flex
```

```console
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
```console
$ sudo apt install bison
```
出現找不到openssl header file問題
```console
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
```console
$ sudo apt-get install libssl-dev
```

編譯完成之後，在Linux_for_Tegra/tools/底下使用script來燒入firmware，燒入firmware有兩個script可以使用二擇一。

底下我們只講使用initrd的方式來燒入，燒入較flash燒入來的方便。參考連結[在這裡](https://docs.nvidia.com/jetson/archives/r35.3.1/DeveloperGuide/text/SD/FlashingSupport.html#flashing-with-initrd)

預先準備
* 高品質的USB-C/micro-USB線
* Host PC使用NetworkManager
* 關閉自動掛載儲存設備
```console
$ systemctl stop udisks2.service
```
* 安裝正確的相依套件
```console
$ sudo tools/l4t_flash_prerequisites.sh # For Debian-based Linux
```

board name請參考[此連結](https://docs.nvidia.com/jetson/archives/r35.3.1/DeveloperGuide/text/IN/QuickStart.html#jetson-modules-and-configurations)表格內的Configuration欄位。

待續。。。