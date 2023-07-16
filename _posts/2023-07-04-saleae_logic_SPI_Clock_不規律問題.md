---
title: Saleae Logic SPI Clock 不規律問題
excerpt: >
  解決Saleae Logic USB 邏輯分析儀量測SPI訊號clock不規律問題。
categories:
  - Embedded System
tags:
  - SPI
  - Saleae Logic
---

## 前言
Saleae Logic USB 邏輯分析儀在量測SPI訊號時發現clock不規律 duty cycle 不一致。
## 分析問題
看到我量測clock的時間，並換算成頻率如下圖所示
![spi_clock_speed](/assets/images/spi_clock_speed.png)

發現最小的duty cycle頻率剛好就是我的邏輯分析儀最快的速度24MHz

也就是說SPI頻率太快以至於我的邏輯分析儀sample rate不及。

## 解決方式
替換一個更好的邏輯分析儀

## Troubleshooting

其他還有關於Saleae Logic相關的問題如下：
* [saleae_logic_usb_connect]({% link _posts/2022-05-21-saleae_logic_usb_connect.md %})
* [saleae_logic_出現read_timeout問題]({% link _posts/2023-07-04-saleae_logic_出現read_timeout問題.md %})

## 結語
快速簡單寫了解決方法，希望能幫助到遇到跟我一樣問題的朋友。