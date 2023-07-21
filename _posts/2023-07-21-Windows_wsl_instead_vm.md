---
title: Windows WSL2 容器 取代 虛擬機速度慢 雙作業系統 開發環境 快速安裝使用 Linux
excerpt: >
  如果開發者需要安裝Linux不想花錢買一台電腦裝Linux，也不想安裝速度慢的虛擬機，又想同時享受Windows和Linux的使用。
categories:
  - Windows
tags:
  - WSL
  - Linux
  - Container
  - Develop
  - BSP
---
## 前言
一般來說就我所知，會接觸Linux的使用者，不外乎是想要用免費軟體，不然就是軟韌體開發者~本篇文章主要是針對後者，軟韌體開發者來說明為什麼比起使用虛擬機或雙作業系統，WSL2有許多方便性，甚至速度快於虛擬機很多~

## 什麼是Windows Subsystem for Linux
Windows Subsystem for Linux簡稱WSL，是一個子Linux作業系統跑在Windows上，不須使用傳統笨慢的虛擬機或是不易切換的雙作業系統。WSL有分先前的版本WSL1和新版本WSL2，我們來看一下他們的架構圖。

WSL1的架構圖。
![wsl1_architecture](/assets/images/wsl1_architecture.png)

WSL2的架構圖。
![wsl2_architecture](/assets/images/wsl2_architecture.png)

圖中可以看到WSL1在Windows Kernel上面，WSL2則有自己的Linux kernel而不再使用Windows Kernel，並且使用輕量化的虛擬機直接對到Hypervisor，速度更接近原生的Linux，也比WSL1快。

Ref: [Exploring the differences between WSL 1 and 2](https://subscription.packtpub.com/book/cloud-and-networking/9781800562448/2/ch02lvl1sec04/exploring-the-differences-between-wsl-1-and-2)

## WSL2和傳統的虛擬機差異
WSL2的架構比起傳統虛擬機，WSL2占用電腦記憶體相對來的低，且更高速的I/O效能，就是存取硬碟速度快，不用花過多的時間安裝虛擬機，也不用資源分配設定。


## 使用WSL2的優點好處
下面列出使用WSL2的好處。
* 占用電腦資源少
* 存取硬碟速度快
* 方便安裝不同版本不同distribuion的Linux
* 易於存取windows資料夾或檔案
* 可存取網路
* 可同時開啟多個WSL
* 易於開發者開發或使用Linux
* 方便移植給其他人使用

## 那些人會需要使用WSL2
以我自己來說，我是韌體開發工程師，我主要的工作是驗證不同板子的硬體元件，並且移植Linux到板子上，對我來說這個職位使用WSL2超級便利，第一因為我經常驗證不同平台的板子，他們會有特定的Board Support Package(BSP)或Software Develop Kit(SDK)，不同的BSP或SDK他們官方建議要使用的distro版本也不同~所以為了確保沒有套件相依性問題或延伸出其他問題，我都會盡量使用官方建議的distro本本，所以我今天開發了Nvidia就使用Ubuntu 20.04 LTS，今天開發TI就使用Ubuntu 18.04 LTS，更方便的是他們兩個獨立不會互相干擾，如果我用Host Build Server，我需要管理cross-compiler，避免使用錯誤的compiler，系統上使用BSP所需要的套件也會變得多元複雜，可能還會彼此套件互斥那就更麻煩了，再者我如果同事今天也要開發跟我一樣板子的時候，我基於乾淨的環境，先建置好了，就可以直接打包丟給同事~

我在編譯Kernel等等速度也相當得快，幾乎快跟原生Linux一樣快，我為什麼還不選擇使用WSL2來當做我的開發環境呢!?我還看到有人寫一篇文章捨棄WSL2來使用Host Build PC，再說另一個方便性，就是他可以很方便的跟Windows檔案資料夾做存取，如果Host Build PC還要拿隨身碟複製，再者做簡報也是很方便，直接複製WSL2裡面的指令或截圖都很快速。

所以通常是開發者或是想體驗或學習的人會使用WSL2囉~

## 其他WSL相關文章
如果看完後對WSL有興趣~可以看看我[其他WSL文章](https://casparting.github.io/tags/#wsl)哦~

## 結語
趕快來使用看看感同體會吧~