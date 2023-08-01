---
title: Nvidia Jetson Orin NX 安裝Jetson Linux 燒入 Image
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
* Host PC(Build Server)/WSL Ubuntu 18.04 LTS
* target board 開發版和SOM
* 高品質的傳輸線

### 判斷SOC型號

再拿到SOM準備開始燒入Image也要先確認SOM是哪一個型號的。
根據[Wiki](https://en.wikipedia.org/wiki/Tegra)可以知道

|型號|SOC|
|:-:|---|
|Xavier NX|TE860M|
|Orin NX|TE980M|

SOC Variant如下圖所示。

![nvidia_orin_xavier_diff](/assets/images/nvidia_orin_xavier_diff.png)

## 使用SDK Manager 燒入Jetson Linux到 Nvidia Jetson Orin NX

根據[Nvidia手冊](https://docs.nvidia.com/jetson/archives/r35.3.1/DeveloperGuide/index.html#software-for-jetson-modules-and-developer-kits)使用NVIDIA SDK Manager來安裝Jetson Linux與相關compoment，SDK Manger可以參考[此手冊](https://docs.nvidia.com/sdk-manager/)。建議這邊Host PC distro用Ubuntu來使用SDK Manager，因為support的compoment比較多，不同版本的支援度請參考下圖。

![Nvidia_SDK_Manager_compatiblity](/assets/images/Nvidia_SDK_Manager_compatiblity.png)

這邊假設你已經架設好Host PC，接下來下載[SDK Manager](https://developer.nvidia.com/sdk-manager)到Host PC，並下指令。
```console
$ sudo dpkg -i sdkmanager_1.9.2-10899_amd64.deb
```

在下底下指令安裝相依套件
```console
$ sudo apt-get install -f
```
執行SDK Manager
```console
$ sdkmanager
```

執行時出現錯誤訊息如下
```console
$ /opt/nvidia/sdkmanager/sdkmanager-gui: error while loading shared libraries: libatk-bridge-2.0.so.0: cannot open shared object file: No such file or directory
```
請安裝以下套件
```console
$ sudo apt-get install libatk-bridge2.0-0
```
如果用出現libgtk library找不到，請安裝以下套件
```console
$ sudp apt-get install gnome-terminal
```
確認可以執行後請按照該網址來繼續下載與安裝SDK[Install Jetson Software with SDK Manager](https://docs.nvidia.com/sdk-manager/install-with-sdkm-jetson/index.html)

打開SDK Manager後會出現登入畫面，如下圖所示。

![nvidia_logging](/assets/images/nvidia_logging.png)

請登入你是先註冊好的帳號。


接下來要連接USB到target board，這邊是使用WSL Linux來建置環境與燒入firmware，WSL要連到target board我們要使用USB over IP來連接USB device。

請參考[這篇文章]({% link _posts/2023-07-21-Windows_mount_Linux_partition_wsl.md %})

請將你的板子上電並進入recovery模式。進入方式是先按下recovery按鈕，再上電。

我們開啟powershell下入以下指令。
```powershell
PS D:\Users\caspar_su> usbipd wsl list
BUSID  VID:PID    DEVICE                                                        STATE
2-1    0955:7323  APX                                                           Not attached
2-2    2109:0100  USB 2.0 BILLBOARD                                             Not attached
2-3    1532:0062  USB 輸入裝置, Razer Atheris                                   Not attached
2-4    3044:4eec  USB 輸入裝置                                                  Not attached
2-5    27c6:639c  Goodix MOC Fingerprint                                        Not attached
2-6    0bda:554a  Integrated Webcam, Camera DFU Device                          Not attached
2-10   8087:0026  Intel(R) Wireless Bluetooth(R)                                Not attached
```

上面如果看到APX代表有順利進入recovery模式。

如上面APX是2-1所以輸入底下指令
```powershell
PS C:\Windows\system32> usbipd wsl attach --busid 2-1 --distribution Ubuntu-18.04
```

再回到WSL Ubuntu，下達以下指令。
```console
$ lsusb
Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 001 Device 002: ID 0955:7323 NVIDIA Corp. APX
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
```

上面可以看到出現 NVIDIA Corp. APX。

這時候回到SDK Manager的Hardware configuration，點選重新整理，就會抓到你的裝置。

再來就是選好JetPack版本，按下一步continue。如圖所示。

![nvidia_sdkmanager_continue](/assets/images/nvidia_sdkmanager_continue.png)


## 使用initrd燒入firmware到 Nvidia Jetson Orin NX

另外的燒入方式，可以使用initrd~

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
