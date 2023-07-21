---
title: Windows WSL 編譯 Kernel 方法 步驟
excerpt: >
  說明編譯Windows WSL Kernel的方法。
categories:
  - Windows
tags:
  - WSL
  - Kernel
  - Linux
---
## 下載 WSL Kernel

先看一下目前的wsl linux kernel版本是哪一個
```console
~$ uname -a
Linux Y01467-Caspar 5.15.90.1-microsoft-standard-WSL2 #1 SMP Fri Jan 27 02:56:13 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux
```

建議可以下載跟他同版本或比他還新的版本，我們可以到[git hub去看](https://github.com/microsoft/WSL2-Linux-Kernel)，他最新版本就是5.15，所以我就載這個版本。

通常我們會把source code放在/usr/src底下
```console
~$ cd /usr/src/
```
下載source code
```console
$ sudo git clone https://github.com/microsoft/WSL2-Linux-Kernel.git
```

## 安裝編譯Kernel所需要的套件

請輸入底下指令
```console
$ sudo apt install build-essential flex bison dwarves libssl-dev libelf-dev
```

## Kernel選項設定 開啟 USB Storage Driver
我們要基於原本舊版本的kernel設定~所以先複製他的設定檔。
```console
$ cd WSL2-Linux-Kernel-linux-msft-wsl-5.15.y/
$ sudo cp /proc/config.gz config.gz
$ sudo gunzip config.gz
$ sudo mv config .config
```
開啟Kernel 設定 Option
```console
$ sudo make menuconfig
```
會跑出選單畫面，請按鍵盤↓選到Device Drivers後Enter進去，如圖所示。

![kernel_menuconfig_devicedrivers](/assets/images/kernel_menuconfig_devicedrivers.png)

進去後往下選到USB support進去，再往下找到USB Mass Storage support案兩次空白按鍵，直到他變成*號。

然後按下→的按鍵選到Save按下OK。

最後選擇Exit一直到離開選項畫面回到終端機。

## 開始編譯WSL Kernel

下達下面指令
```console
$ sudo time make -j$(nproc)
.
.
.
  LD      arch/x86/boot/setup.elf
  OBJCOPY arch/x86/boot/setup.bin
  BUILD   arch/x86/boot/bzImage
Kernel: arch/x86/boot/bzImage is ready  (#1)
```
這時候可以去泡個咖啡囉~

編譯modules
```console
$ sudo make modules_install -j$(nproc)
  DEPMOD  /lib/modules/5.15.90.3-microsoft-standard-WSL2
```

安裝kernel
```console
$ sudo make install -j$(nproc)
arch/x86/Makefile:142: CONFIG_X86_X32 enabled but no binutils support
sh ./arch/x86/boot/install.sh 5.15.90.3-microsoft-standard-WSL2 \
        arch/x86/boot/bzImage System.map "/boot"
run-parts: executing /etc/kernel/postinst.d/initramfs-tools 5.15.90.3-microsoft-standard-WSL2 /boot/vmlinuz-5.15.90.3-microsoft-standard-WSL2
update-initramfs: Generating /boot/initrd.img-5.15.90.3-microsoft-standard-WSL2
cryptsetup: WARNING: could not determine root device from /etc/fstab
I: The initramfs will attempt to resume from /dev/sdb
I: (UUID=4d84a072-3b0f-4ad9-bd87-994699d24b75)
I: Set the RESUME variable to override this.
run-parts: executing /etc/kernel/postinst.d/unattended-upgrades 5.15.90.3-microsoft-standard-WSL2 /boot/vmlinuz-5.15.90.3-microsoft-standard-WSL2
run-parts: executing /etc/kernel/postinst.d/update-notifier 5.15.90.3-microsoft-standard-WSL2 /boot/vmlinuz-5.15.90.3-microsoft-standard-WSL2
run-parts: executing /etc/kernel/postinst.d/xx-update-initrd-links 5.15.90.3-microsoft-standard-WSL2 /boot/vmlinuz-5.15.90.3-microsoft-standard-WSL2
I: /vmlinuz.old is now a symlink to boot/vmlinuz-5.15.90.3-microsoft-standard-WSL2
I: /initrd.img.old is now a symlink to boot/initrd.img-5.15.90.3-microsoft-standard-WSL2
I: /vmlinuz is now a symlink to boot/vmlinuz-5.15.90.3-microsoft-standard-WSL2
I: /initrd.img is now a symlink to boot/initrd.img-5.15.90.3-microsoft-standard-WSL2
```

ls會看到底下有vmlinux
```console
$ ls
COPYING        MSFT-Merge             SECURITY.md  fs        mm                       scripts   vmlinux.o
CREDITS        Makefile               System.map   include   modules-only.symvers     security  vmlinux.symvers
Documentation  Microsoft              arch         init      modules.builtin          sound
Kbuild         Module.symvers         block        io_uring  modules.builtin.modinfo  tools
Kconfig        README                 certs        ipc       modules.order            usr
LICENSES       README-Microsoft.WSL2  crypto       kernel    net                      virt
MAINTAINERS    README.md              drivers      lib       samples                  vmlinux
```

複製vmlinux
```
$ sudo mkdir -p /mnt/c/Sources
$ sudo cp -rf vmlinux /mnt/c/Sources/
```

開啟powershell輸入底下指令
```powershell
PS C:\Windows\system32> wsl --shutdown
```

開啟你使用者資料夾

我的話是在這邊D:\Users\caspar_su

一般人通常是放在C槽

新增一個檔案命名為.wslconfig 記得確認副檔名不能有.txt

輸入底下內容到檔案內存檔關閉
```
[wsl2]
kernel=C:\\Sources\\vmlinux
```
最後開啟wsl就會抓取新的linux kernel囉~ 底下時間為最新的5.15.90 7/21
```console
$ uname -a
Linux Y01467-Caspar 5.15.90.3-microsoft-standard-WSL2 #1 SMP Fri Jul 21 14:09:26 CST 2023 x86_64 x86_64 x86_64 GNU/Linux
```

Ref: [Advanced settings configuration in WSL](https://learn.microsoft.com/en-us/windows/wsl/wsl-config#configure-global-options-with-wslconfig)

## 結語
快速幫助遇到相同問題的人~