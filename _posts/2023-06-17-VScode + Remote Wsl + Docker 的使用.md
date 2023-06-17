---
title: VScode + Remote Wsl + Docker 的使用
excerpt: >
  使用Windows來開發板子時，會需要build BSP或APP，為了方便trace code，所以我使用vscode搭配remote wsl extension，而專案也用到docker並使用vscode開啟。
categories:
  - IDE
tags:
  - IDE
  - Linux
  - VScode
  - WSL
  - Docker
---
## 前言
使用Windows來開發板子時，會需要build BSP或APP，Windows有推出Subsystem for Linux(WSL)，對於需要再Linux上開發的開發者來說方便許多，可以把SOC商提供的BSP丟入用cross-compiler來build code，而在trace code時，還可以使用vscode搭配remote wsl extension，讓vscode與wsl的連接mount相應的filesystem，而如果專案有用到docker，也可以使用vscode開啟。
## 安裝WSL與VScode
[Windows 10 安裝 WSL1、WSL2](https://hackmd.io/@Kailyn/BkMi80IeF)
## VScode與WSL結合使用
[Developing in WSL](https://code.visualstudio.com/docs/remote/wsl)
## 在WSL內安裝Docker
這邊我WSL distro是安裝Ubuntu，以Ubuntu為例。
請在WSL terminal執行底下指令。

前置設定
```
$ sudo apt-get update
$ sudo apt-get install ca-certificates curl gnupg
$ sudo install -m 0755 -d /etc/apt/keyrings
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
$ sudo chmod a+r /etc/apt/keyrings/docker.gpg
$ echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
安裝docker
```
$ sudo apt-get update
$ sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
ref: [Install Docker Engine on Ubuntu](https://docs.docker.com/engine/install/ubuntu/)
## 執行WSL
執行WSL並且在terminal下以下指令
```
$ code .
```
## 專案有dockerfile
如果專案下載到WSL後，有dockerfile需要build docker image，因為執行code .是使用一般使用者權限，預設docker只能超級使用者權限執行，為了讓開啟vscode後能使用vscode來自動build docker image以及進入container，所以在WSL terminal中我們要給一般使用者也能使用docker，指令如下。
```
$ sudo groupadd docker
$ sudo usermod -aG docker $USER
```
## Troubleshooting
如果在WSL執行
```
code .
```
出現以下錯誤訊息
```
VS Code: Exec format error wsl
```
請到家目錄把.vscode-server和.vscode-remote-containers刪除重新開機再執行code .
## 結語
讓vscode用起來更方便吧~