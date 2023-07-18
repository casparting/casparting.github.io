---
title: Saleae Logic USB 邏輯分析儀 Linux 連接不上 無法連接
excerpt: >
  解決Saleae Logic USB 邏輯分析儀 Linux Debian無法連接的問題。
categories:
  - Linux
tags:
  - Linux
  - Debian
  - Saleae Logic
---

## 前言
Saleae Logic USB 邏輯分析儀在Linux Debian上無法連接的問題。
## 錯誤訊息
>Install a udev rules file to allow non-root access to Saleae devices

## 解決方式
參考下方連接的檔案內容

Ref: [99-saleae-logic-libusb.rules](https://github.com/keesj/saleae-logic-libusb/blob/master/contrib/udevd/99-saleae-logic-libusb.rules)

將檔案下載下來後放置此路徑底下

```console
# mv 99-saleae-logic-libusb.rules /etc/udev/rules.d/
```

接上Saleae Logic USB出現下面的圖示表示連接成功
![connecting](/assets/images/logic_connect.png)

## Troubleshooting

其他還有關於Saleae Logic相關的問題如下：
* [saleae_logic_SPI_Clock_不規律問題]({% link _posts/2023-07-04-saleae_logic_SPI_Clock_不規律問題.md %})
* [saleae_logic_出現read_timeout問題]({% link _posts/2023-07-04-saleae_logic_出現read_timeout問題.md %})

## 結語
快速簡單寫了解決方法，希望能幫助到遇到跟我一樣問題的朋友。