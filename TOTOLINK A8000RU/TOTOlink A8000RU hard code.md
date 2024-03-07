# TOTOlink A8000RU V7.1cu.643_B20200521 Hard Code Password for root

## Product Information

Device: TOTOlink A8000R
Firmware Version: V7.1cu.643_B20200521
Manufacturer's website information：https://www.totolink.net/
Firmware download address ：https://www.totolink.net/home/menu/detail/menu_listtpl/download/id/176/ids/36.html

![image-20240117214330958](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A8000RU/img/image-20240117214330958.png)

## Vulnerability Description

There is a hard code password for root in /etc/shadow

## Analyze

![image-20240117214528579](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A8000RU/img/image-20240117214528579.png)

after decrypt the passwd we got cs2012

![image-20240117214521167](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A8000RU/img/image-20240117214521167.png)
