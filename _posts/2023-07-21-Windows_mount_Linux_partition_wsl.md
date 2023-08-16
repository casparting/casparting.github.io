---
title: Windows Mount Linux Partition in WSL 掛載 存取 Linux USB SD Card 隨身碟 EXT4
excerpt: >
  解決Windows WSL 無法掛載 Linux USB或 SD card問題。
categories:
  - Windows
tags:
  - WSL
  - Mount
  - Linux
  - USB
  - SD Card
---
## 掛載Linux disk in WSL2

網路上有些文章參考了[這篇](https://learn.microsoft.com/en-us/windows/wsl/wsl2-mount-disk)說可以成功掛載linux disk，我實際測試掛載USB linux的結果會發生底下錯誤訊息。

搜尋有哪些磁碟設備
```powershell
PS C:\Windows\system32> GET-CimInstance -query "SELECT * from Win32_DiskDrive"

DeviceID           Caption                             Partitions Size         Model
--------           -------                             ---------- ----         -----
\\.\PHYSICALDRIVE1 TS-RDF5A Transcend USB Device       2          15833664000  TS-RDF5A Transcend USB Device
\\.\PHYSICALDRIVE0 NVMe KBG50ZNS512G NVMe KIOXIA 512GB 5          512105932800 NVMe KBG50ZNS512G NVMe KIOXIA 512GB
```

底下指令掛載磁碟~確有錯誤0x8007000f
```powershell
PS C:\Windows\system32> wsl --mount \\.\PHYSICALDRIVE1 --partition 2 --type EXT4
系統找不到指定的磁碟機。
Error code: Wsl/Service/AttachDisk/0x8007000f
```

我個人猜測是他只能掛載磁碟機不能掛載USB或SD Card。

## 使用USB/IP來掛載 USB Drive/ SD Card

再深入搜尋解法時找到有些人用usbipd來連接USB裝置，但我在試完之後卻發現，lsusb雖然有抓到USB裝置，但是卻沒有dev裝置，sda~sdd都是我本機端的內建磁碟裝置。或者是dmesg 顯示error=remount-ro的訊息，而沒有說USB裝置是哪一個sdx。

Ref: [Connecting USB devices to WSL](https://devblogs.microsoft.com/commandline/connecting-usb-devices-to-wsl/)

但最後面有解決方式，所以這邊還是先一步一步來安裝usbipd。

在WSL輸入底下指令
```console
sudo apt install linux-tools-5.4.0-77-generic hwdata
sudo update-alternatives --install /usr/local/bin/usbip usbip /usr/lib/linux-tools/5.4.0-77-generic/usbip 20
```

開啟Powershell輸入底下指令
```powershell
PS C:\Windows\system32> usbipd wsl list
BUSID  VID:PID    DEVICE                                                        STATE
2-2    2109:0100  USB 2.0 BILLBOARD                                             Not attached
2-3    1532:0062  USB 輸入裝置, Razer Atheris                                   Not attached
2-4    8564:4000  USB Mass Storage Device                                       Not attached
2-5    27c6:639c  Goodix MOC Fingerprint                                        Not attached
2-6    0bda:554a  Integrated Webcam, Camera DFU Device                          Not attached
2-10   8087:0026  Intel(R) Wireless Bluetooth(R)                                Not attached
```

列出你現在有哪些WSL distribution
```powershell
PS C:\Windows\system32> wsl -l
Windows 子系統 Linux 版發佈:
Ubuntu (預設)
Ubuntu-18.04
docker-desktop
Ubuntu-20.04
docker-desktop-data
PS C:\Windows\system32> usbipd wsl attach --busid 2-4 --distribution Ubuntu-18.04
usbipd: warning: A third-party firewall may be blocking the connection; ensure TCP port 3240 is allowed.
```

回到WSL看到lsusb有抓到我創見的讀卡機，但卻沒有抓到sdx設備。
```console
caspar@Y01467-Caspar:~$ lsusb
Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 001 Device 002: ID 8564:4000 Transcend Information, Inc. RDF8
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub

caspar@Y01467-Caspar:~$ lsblk
NAME MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda    8:0    0 363.3M  1 disk
sdb    8:16   0     2G  0 disk [SWAP]
sdc    8:32   0     1T  0 disk
sdd    8:48   0     1T  0 disk /mnt/wslg/distro
```

## 原來WSL Linux Kernel沒有USB Storage Driver

```
caspar@Y01467-Caspar:~$ sudo cp /proc/config.gz config.gz
[sudo] password for caspar:
caspar@Y01467-Caspar:~$ sudo gunzip config.gz
caspar@Y01467-Caspar:~$ cat config |grep USB_STOR
# NOTE: USB_STORAGE depends on SCSI but BLK_DEV_SD may
# also be needed; see USB_STORAGE Help for more info
# CONFIG_USB_STORAGE is not set
```

所以請再繼續參考我這篇文章[Windows_wsl_build_kernel]({% link _posts/2023-07-21-Windows_wsl_build_kernel.md %})來編譯WSL Linux kernel。

## 編譯完成之後再一次使用USBIPD

結果如下
```console
~$ lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda      8:0    0 363.3M  1 disk
sdb      8:16   0     2G  0 disk [SWAP]
sdc      8:32   0     1T  0 disk
sdd      8:48   0     1T  0 disk /mnt/wslg/distro
sde      8:64   1  14.8G  0 disk
├─sde1   8:65   1 133.6M  0 part
└─sde2   8:66   1  14.6G  0 part
```

```console
~$ dmesg
.
.
.
[   76.676269] EXT4-fs (sdc): mounted filesystem with ordered data mode. Opts: discard,errors=remount-ro,data=ordered. Quota mode: none.
[   77.677039] scsi 1:0:0:0: Direct-Access     TS-RDF5A Transcend        0009 PQ: 0 ANSI: 6
[   77.677594] sd 1:0:0:0: Attached scsi generic sg4 type 0
[   77.978090] sd 1:0:0:0: [sde] 30930944 512-byte logical blocks: (15.8 GB/14.7 GiB)
[   77.985711] sd 1:0:0:0: [sde] Write Protect is off
[   77.985716] sd 1:0:0:0: [sde] Mode Sense: 21 00 00 00
[   77.991443] sd 1:0:0:0: [sde] Write cache: disabled, read cache: enabled, doesn't support DPO or FUA
[   78.022535]  sde: sde1 sde2
[   78.037250] sd 1:0:0:0: [sde] Attached SCSI removable disk
```

順利抓到sde設備也出現我sd card的容量。

Ref: [Linux Tips - Add USB Drive to WSL (Ext4) (2022)](https://www.youtube.com/watch?v=iyBfQXmyH4o)

## 結語
快速幫助遇到相同問題的人~