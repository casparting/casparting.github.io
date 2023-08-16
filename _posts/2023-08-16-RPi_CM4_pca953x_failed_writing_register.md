---
title: RPi CM4 PCA953x 發生錯誤訊息 failed writing register error -121
excerpt: >
  說明如何解決PCA953x發生的錯誤找到根因。
categories:
  - Embedded System
tags:
  - Raspberry Pi
  - Linux
  - SBC
  - Embedded System
  - CM4
  - IoT
  - USB
  - I2C
  - PCA953x
---
## 前言
本篇內容主要說明解決PCA953x出現的錯誤訊息。
## 前提準備
一塊已經裝好RPi OS的樹梅派板子。

## dmesg出現錯誤訊息

錯誤訊息如下。
```shell
~$ dmesg |grep pca953x
[    6.890299] pca953x 1-0025: supply vcc not found, using dummy regulator
[    6.901817] pca953x 1-0025: using AI
[    6.902772] pca953x 1-0025: failed writing register
[    6.953380] pca953x: probe of 1-0025 failed with error -121
```

而我的/boot/config.txt設定如下
```console
# Uncomment some or all of these to enable the optional hardware interfaces
dtparam=i2c_arm=off
dtparam=i2c_vc=on
#dtparam=i2s=on
dtparam=spi=on

# Enable PCA9575 GPIO Extender
dtoverlay=pca953x,addr=0x25,pca9575

#Enable I2C0
dtoverlay=i2c0,pins_44_45
```

奇怪的是我把i2c_arm設成off關閉i2c1卻沒有消失。
```console
~$ i2cdetect -l
i2c-20  i2c             fef04500.i2c                            I2C adapter
i2c-1   i2c             bcm2835 (i2c@7e804000)                  I2C adapter
i2c-21  i2c             fef09500.i2c                            I2C adapter
i2c-0   i2c             bcm2835 (i2c@7e205000)                  I2C adapter
```
查看i2c上的設備
```console
~$ i2cdetect -r -y 0
     0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
00:                         -- -- -- -- -- -- -- --
10: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
20: -- -- -- -- -- 25 -- -- -- -- -- -- -- -- -- --
30: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
40: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
50: -- 51 -- -- -- -- -- 57 -- -- -- -- -- -- -- --
60: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
70: -- -- -- -- -- -- -- --
```
```console
~$ i2cdetect -r -y 1
     0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
00:                         -- -- -- -- -- -- -- --
10: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
20: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
30: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
40: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
50: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
60: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
70: -- -- -- -- -- -- -- --
```
看了dtoverlay的說明如下，也沒看出什麼~
```console
~$ dtoverlay -h pca953x
Name:   pca953x

Info:   TI PCA953x family of I2C GPIO expanders. Default is for NXP PCA9534.

Usage:  dtoverlay=pca953x,<param>=<val>

Params: addr                    I2C address of expander. Default 0x20.
        pca6416                 Select the NXP PCA6416 (16 bit)
        pca9505                 Select the NXP PCA9505 (40 bit)
        pca9535                 Select the NXP PCA9535 (16 bit)
        pca9536                 Select the NXP PCA9536 or TI PCA9536 (4 bit)
        pca9537                 Select the NXP PCA9537 (4 bit)
        pca9538                 Select the NXP PCA9538 (8 bit)
        pca9539                 Select the NXP PCA9539 (16 bit)
        pca9554                 Select the NXP PCA9554 (8 bit)
        pca9555                 Select the NXP PCA9555 (16 bit)
        pca9556                 Select the NXP PCA9556 (8 bit)
        pca9557                 Select the NXP PCA9557 (8 bit)
        pca9574                 Select the NXP PCA9574 (8 bit)
        pca9575                 Select the NXP PCA9575 (16 bit)
        pca9698                 Select the NXP PCA9698 (40 bit)
        pcal6416                Select the NXP PCAL6416 (16 bit)
        pcal6524                Select the NXP PCAL6524 (24 bit)
        pcal9555a               Select the NXP PCAL9555A (16 bit)
        max7310                 Select the Maxim MAX7310 (8 bit)
        max7312                 Select the Maxim MAX7312 (16 bit)
        max7313                 Select the Maxim MAX7313 (16 bit)
        max7315                 Select the Maxim MAX7315 (8 bit)
        pca6107                 Select the TI PCA6107 (8 bit)
        tca6408                 Select the TI TCA6408 (8 bit)
        tca6416                 Select the TI TCA6416 (16 bit)
        tca6424                 Select the TI TCA6424 (24 bit)
        tca9539                 Select the TI TCA9539 (16 bit)
        tca9554                 Select the TI TCA9554 (8 bit)
        cat9554                 Select the Onnn CAT9554 (8 bit)
        pca9654                 Select the Onnn PCA9654 (8 bit)
        xra1202                 Select the Exar XRA1202 (8 bit)
```

於是我去看pca953x的dts發現如下。
```dts
// Definitions for NXP PCA953x family of I2C GPIO controllers on ARM I2C bus.
/dts-v1/;
/plugin/;

/{
	compatible = "brcm,bcm2835";

	fragment@0 {
		target = <&i2c_arm>;
		__overlay__ {
			#address-cells = <1>;
			#size-cells = <0>;
			status = "okay";

			pca: pca@20 {
				compatible = "nxp,pca9534";
				reg = <0x20>;
				gpio-controller;
				#gpio-cells = <2>;

				status = "okay";
			};
		};
	};

```
Ref: https://github.com/raspberrypi/linux/blob/rpi-6.1.y/arch/arm/boot/dts/overlays/pca953x-overlay.dts

他的i2c竟然是使用i2c_arm，而不是i2c_vc，兩者差異可以去看我[這篇文章]({% link _posts/2023-08-16-RPi_CM4_關閉I2C1啟用I2C0.md %})

也就是說他是使用i2c1，竟然她的dts是這樣宣告難怪/dev會長出i2c1的設備。

底下我們試試看把pca953x註解，並重新開機。
```console
# Enable PCA9575 GPIO Extender
#dtoverlay=pca953x,addr=0x25,pca9575
```

果不其然的重新檢查i2c設備，i2c1消失了
```console
~$ i2cdetect -l
i2c-20  i2c             fef04500.i2c                            I2C adapter
i2c-21  i2c             fef09500.i2c                            I2C adapter
i2c-0   i2c             bcm2835 (i2c@7e205000)                  I2C adapter
```

所以到這邊我們可以知道i2c0可以找到pca953x的addr是25，但沒有UU表示存取使用到pca953x，其原因就是pca953x的dtoverlay是使用i2c1，剛剛上面我們有列出i2c1上根本沒有任何裝置。

從我公司的電路圖得知，這些設備位址25、51、57都是接到GPIO44、GPIO45。

且GPIO44、GPIO45可以是i2c0也可以是i2c1，為了方便設置裝置，(不修改dts的情況之下)我決定把GPIO44、GPIO45由i2c0換成i2c1。設定改成如下。
```console
# Uncomment some or all of these to enable the optional hardware interfaces
dtparam=i2c_arm=on
dtparam=i2c_vc=off
#dtparam=i2s=on
dtparam=spi=on

# Enable PCA9575 GPIO Extender
dtoverlay=pca953x,addr=0x25,pca9575

#Enable I2C0
dtoverlay=i2c1,pins_44_45
```
一樣重新啟動板子。

dmesg可以發現錯誤訊息消失了，且i2c pca953x變成UU使用中。
```
~$ i2cdetect -l
i2c-20  i2c             fef04500.i2c                            I2C adapter
i2c-1   i2c             bcm2835 (i2c@7e804000)                  I2C adapter
i2c-21  i2c             fef09500.i2c                            I2C adapter

~$ i2cdetect -r -y 1
     0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
00:                         -- -- -- -- -- -- -- --
10: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
20: -- -- -- -- -- UU -- -- -- -- -- -- -- -- -- --
30: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
40: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
50: -- 51 -- -- -- -- -- 57 -- -- -- -- -- -- -- --
60: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
70: -- -- -- -- -- -- -- --
```

## 結語
硬體驗證就是這麼簡單有趣~