---
title: Saleae Logic 出現 Read Timeout 問題
excerpt: >
  解決Saleae Logic USB 邏輯分析儀出現 Read Timeout 問題。
categories:
  - Embedded System
tags:
  - Saleae Logic
---

## 前言
Saleae Logic USB 邏輯分析儀在錄製訊號時發生 ReadTimeout問題。
## 分析問題
錯誤訊息如下
> An error occurred during capture: ReadTimeout

> Try lowering the sample rate.

如下圖所示

![saleae_read_timeout](/assets/images/saleae_read_timeout.png)


## 解決方式
檢查電腦USB是否有其他也正在傳送資料，如serial console port，請暫時移除再試一次。

如果還是不行請確認電腦USB是否過慢。

最後不行就是降低sameple rate。

## 結語
快速簡單寫了解決方法，希望能幫助到遇到跟我一樣問題的朋友。