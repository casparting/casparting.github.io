---
title: ASUS USB-AC51 韌體安裝 Linux Debian
excerpt: >
  解決Linux Debian上無法使用ASUS USB-AC51 Wifi dongle的問題。
categories:
  - Linux
tags:
  - Linux
  - Debian
  - ASUS
  - USB-AC51
---

## 前言
解決Linux Debian上無法使用ASUS USB-AC51 Wifi dongle的問題。

## firmware-misc-nonfree套件
ASUS USB-AC51需要mt7610e.bin的firmware檔如下所示

>MediaTek MT76x0 wifi firmware (mediatek/mt7610e.bin)

Ref: [Debian官方套件網站](https://packages.debian.org/sid/firmware-misc-nonfree)

## 確認/etc/apt/sources.list 是否有加入 non-free
請下達該指令

```
~$ cat /etc/apt/sources.list
```

出現以下訊息

```
deb http://opensource.nchc.org.tw/debian/ bullseye main contrib non-free
deb-src http://opensource.nchc.org.tw/debian/ bullseye main contrib non-free

deb http://security.debian.org/debian-security bullseye-security main contrib non-free
deb-src http://security.debian.org/debian-security bullseye-security main contrib non-free
```

最尾端有**non-free**

## 開始安裝firmware-misc-nonfree套件
```
# apt-get update;apt-get upgrade -y
# apt-get install firmware-misc-nonfree -y
```

## 結語
希望快速簡單的步驟能幫助跟我遇到相同問題的朋友。