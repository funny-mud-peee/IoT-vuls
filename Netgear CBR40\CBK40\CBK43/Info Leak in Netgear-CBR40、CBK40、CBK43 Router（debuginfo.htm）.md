# Info Leak in Netgear-CBR40、CBK40、CBK43 Router（debuginfo.htm）

![image-20240306103709845](D:\typora 图片\image-20240306103709845.png)

## Overview

```
* Type: Information leak
* Supplier: Netgear (https://www.netgear.com/)
* Victim URL: http://192.168.1.1/debuginfo.htm
* Product: CBR40、CBK40、CBK43 —  Orbi AC2200 Tri-band WiFi Cable Modem Router
* Affect version: (lastest) 2.5.0.28
* Firmware download: https://www.downloads.netgear.com/files/GDC/CBK40/CBR40-V2.5.0.28.zip
```

![image-20240306104512980](D:\typora 图片\image-20240306104512980.png)

## Description

An infomation leaking vulnerability is at the web management interface of the affected routers. Without any permition, attacker can get sensitive information from the victim URL.

The victime url is a hidden interface and isn't been protected by authentication.

## Business Impact

The leaked information is sensitive and could result in serious damage. Thus the vulnerability is very dangerous which could also result in reputational damage for the business through the impact on customers' trust.

## Steps to Reproduce

Visit the victime URL from the web, sensitive information is explosed as below: ''' WAN connection type: DHCP Product model name: CBR40'''