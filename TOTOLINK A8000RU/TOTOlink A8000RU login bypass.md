# TOTOlink A8000RU V7.1cu.643_B20200521 Login Bypass

## Product Information

Device: TOTOlink A8000R
Firmware Version: V7.1cu.643_B20200521
Manufacturer's website information：https://www.totolink.net/
Firmware download address ：https://www.totolink.net/home/menu/detail/menu_listtpl/download/id/176/ids/36.html

![image-20240117214330958](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A8000RU/img/image-20240117214330958.png)

## POC

```url
# access
formLoginAuth.htm?authCode=1&action=login
```

![image-20240228213103122](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A8000RU/img/image-20240228213103122.png)

![image-20240228212755452](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A8000RU/img/image-20240228212755452.png)

## Analyze

Analyzing cstecgi, based on the previous information, it can be inferred that the program flow follows the else branch

![image-20240228212625677](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A8000RU/img/image-20240228212625677.png)

After `cstecgi.cgi`, you will receive` http://192.168.2.168:8080/formLoginAuth.htm?authCode=0&userName=&goURL=login.html&action=login `Due to the fact that the target file formLoginAuth.htm is an html file, it needs to be handed over to `lighttpd` for processing. Reverse search for `"formLoginAuth. htm"` on `lighttpd` and come to the `userloginAuth` function:

![image-20240228212631321](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A8000RU/img/image-20240228212631321.png)

According to the function name "User Login Verification", it is known that the correct location has been obtained. Form-Login has the following two blocks of code:

![image-20240228212636909](https://github.com/funny-mud-peee/IoT-vuls/blob/main/TOTOLINK%20A8000RU/img/image-20240228212636909.png)

It is very clear that when authCode==1, `lighttpd` can generate a session.

- The authCode of cstecgi.cgi is a local variable on the stack, while the authCode of lighttpd is passed in from the URL.
- According to this network request, the authCode value generated in cstecgi.cgi was passed into lighttpd through a URL.
- Lighttpd determines whether to generate a session based on the authCode.
