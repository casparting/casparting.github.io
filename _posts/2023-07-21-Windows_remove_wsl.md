---
title: 完整移除 WSL 發行版 Remove Distribution
excerpt: >
  快速說明完整移除wsl步驟。
categories:
  - Windows
tags:
  - WSL
  - Windows
  - Distribution
---
## 前言
如果從windows setting的應用程式中點選解除安裝，wsl的資料夾並沒有移除~如圖所示。

![wsl_remove](/assets/images/wsl_remove.png)

底下快速說明如何正確移除整個資料。

## 移除某個版本的wsl distro

請開啟powershell，輸入底下指令確認目前有哪些distro。

```powershell
PS C:\Windows\system32> wsl -l
Windows 子系統 Linux 版發佈:
Ubuntu (預設)
Ubuntu-18.04
docker-desktop
Ubuntu-20.04
docker-desktop-data
```

再輸入底下指令刪除特定版本的wsl distro，這邊以Ubuntu 20.04為例。

```powershell
PS C:\Windows\system32> wsl --unregister Ubuntu-20.04
```
再回去看wsl路徑 \\\\wsl$ 內20.04資料夾順利移除，釋出空間了。

## 結語
快速幫助遇到相同問題的人~