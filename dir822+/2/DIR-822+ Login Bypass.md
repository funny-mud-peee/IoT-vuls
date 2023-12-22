# DIR-822+ Login Bypass

## Overview

D-Link DIR-822+ V1.0.2 was found to contain a command injection in `SetStaticRouteSettings` function.  allows remote attackers to execute arbitrary commands via shell

![image-20231222123220855](D:\typora 图片\image-20231222123220855.png)

![image-20231222123226442](D:\typora 图片\image-20231222123226442.png)

## Vulnerability details

This function is the beginning of the entire HNAP authentication

![image-20231222150820971](D:\typora 图片\image-20231222150820971.png)

This authentication bypass vulnerability is caused by the incorrect use of strncmp() to compare the server-calculated LoginPassword with the LoginPassword presented by the client.

Strncmp (x, y, strlen (y)), where strlen encounters 00 truncation, where y represents the input passwd. If \ x00 is entered, the top 0 bits are compared, and not a single bit is compared. This returns strncmp to 0, so there is an unexpected login success here.

![image-20231222150829229](D:\typora 图片\image-20231222150829229.png)

## POC

![image-20231222135839434](D:\typora 图片\image-20231222135839434.png)