### CA：
- 公共信任的CA
- 私有CA
  	
#### 建立私有CA：
>openssl
>OpenCA

##### openssl命令：
配置文件：/etc/pki/tls/openssl.cnf
			
>构建私有CA:
>在确定配置为CA的服务上生成一个自签证书，并为CA提供所需要的目录及文件即可；

**步骤：**
(1) 生成私钥
`(umask 077; openssl genrsa -out /etc/pki/CA/private/cakey.pem 4096)`

```
[root@centos7 ~]# (umask 077; openssl genrsa -out /etc/pki/CA/private/cakey.pem 4096)
Generating RSA private key, 4096 bit long modulus
................................++
....................................................................................................................++
e is 65537 (0x10001)
```

(2) 生成自签证书
`openssl req -new -x509 -key /etc/pki/CA/private/cakey.pem -out /etc/pki/CA/cacert.pem -days 3655`

>-new：生成新证书签署请求；
>-x509：生成自签格式证书，专用于创建私有CA时；
>-key：生成请求时用到的私有文件路径；
>-out：生成的请求文件路径；如果自签操作将直接生成签署过的证书；
>-days：证书的有效时长，单位是day；

```
[root@centos7 ~]# openssl req -new -x509 -key /etc/pki/CA/private/cakey.pem -out /etc/pki/CA/cacert.pem -days 3655
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:CN
State or Province Name (full name) []:Jiangsu
Locality Name (eg, city) [Default City]:Nanjing
Organization Name (eg, company) [Default Company Ltd]:MageEdu
Organizational Unit Name (eg, section) []:Ops
Common Name (eg, your name or your server's hostname) []:ca.magedu.com
Email Address []:caadmin@magedu.com

[root@centos7 ~]# ls /etc/pki/CA
cacert.pem  certs  crl  newcerts  private
```

(3) 为CA提供所需的目录及文件；
`mkdir  -pv  /etc/pki/CA/{certs,crl,newcerts}`
`touch  /etc/pki/CA/{serial,index.txt}`
`echo  01 > /etc/pki/CA/serial`
						
**要用到证书进行安全通信的服务器，需要向CA请求签署证书：**
192.168.0.100是CA				
192.168.0.104是访问主机

**步骤：（以httpd为例）此为另一台主机**
(1) 用到证书的主机生成私钥；
`mkdir  /etc/httpd/ssl `
`cd  /etc/httpd/ssl`
`(umask  077; openssl  genrsa -out  /etc/httpd/ssl/httpd.key  2048)`

```
[root@promote ~]# mkdir /etc/httpd/ssl
[root@promote ~]# cd /etc/httpd/ssl
[root@promote ssl]# (umask  077; openssl  genrsa -out  /etc/httpd/ssl/httpd.key  2048)
Generating RSA private key, 2048 bit long modulus
....................................................................................................+++
.............+++
e is 65537 (0x10001)
```

(2) 生成证书签署请求
`openssl  req  -new  -key  /etc/httpd/ssl/httpd.key  -out /etc/httpd/ssl/httpd.csr  -days  365`

```
[root@promote ssl]# openssl req -new -key /etc/httpd/ssl/httpd.key -out /etc/httpd/ssl/httpd.csr -days 365
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:CN
State or Province Name (full name) []:Jiangsu
Locality Name (eg, city) [Default City]:Nanjing
Organization Name (eg, company) [Default Company Ltd]:MageEdu
Organizational Unit Name (eg, section) []:Ops
Common Name (eg, your name or your server's hostname) []:www.magedu.com
Email Address []:webmaster@magedu.com

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:
```

(3) 将请求通过可靠方式发送给CA主机；

```
[root@promote ssl]# scp httpd.csr root@192.168.0.100:/tmp/
The authenticity of host '192.168.0.100 (192.168.0.100)' can't be established.
RSA key fingerprint is bc:57:27:7c:b3:8f:f6:a4:d8:70:b1:b0:59:b4:2d:ad.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.168.0.100' (RSA) to the list of known hosts.
Address 192.168.0.100 maps to promote.cache-dns.local, but this does not map back to the address - POSSIBLE BREAK-IN ATTEMPT!
root@192.168.0.100's password: 
httpd.csr                                                                         100% 1058     1.0KB/s   00:00    
```

(4) 在CA主机上签署证书（192.168.0.100）
`openssl ca  -in  /tmp/httpd.csr  -out  /etc/pki/CA/certs/httpd.crt  -days  365`

```
[root@promote ~]# ls /tmp
httpd.csr

[root@centos7 ~]# openssl ca  -in  /tmp/httpd.csr  -out  /etc/pki/CA/certs/httpd.crt  -days  365
Using configuration from /etc/pki/tls/openssl.cnf
Check that the request matches the signature
Signature ok
Certificate Details:
        Serial Number: 1 (0x1)
        Validity
            Not Before: May 12 05:31:33 2019 GMT
            Not After : May 11 05:31:33 2020 GMT
        Subject:
            countryName               = CN
            stateOrProvinceName       = Jiangsu
            organizationName          = MageEdu
            organizationalUnitName    = Ops
            commonName                = www.magedu.com
            emailAddress              = webmaster@magedu.com
        X509v3 extensions:
            X509v3 Basic Constraints: 
                CA:FALSE
            Netscape Comment: 
                OpenSSL Generated Certificate
            X509v3 Subject Key Identifier: 
                2B:E7:6F:3B:7E:48:C9:03:02:CE:68:F8:3C:FB:A9:E6:27:06:EC:A2
            X509v3 Authority Key Identifier: 
                keyid:9A:E9:64:4A:23:BB:30:48:17:42:94:56:E5:2E:73:28:ED:F4:DC:6D

Certificate is to be certified until May 11 05:31:33 2020 GMT (365 days)
Sign the certificate? [y/n]:y


1 out of 1 certificate requests certified, commit? [y/n]y
Write out database with 1 new entries
Data Base Updated

[root@centos7 ~]# cd /etc/pki/CA
[root@centos7 CA]# ls
cacert.pem  certs  crl  index.txt  index.txt.attr  index.txt.old  newcerts  private  serial  serial.old
[root@centos7 CA]# cat index.txt
V	200511053133Z		01	unknown	/C=CN/ST=Jiangsu/O=MageEdu/OU=Ops/CN=www.magedu.com/emailAddress=webmaster@magedu.com

[root@centos7 CA]# scp certs/httpd.crt root@192.168.0.104:/etc/httpd/ssl/
The authenticity of host '192.168.0.104 (192.168.0.104)' can't be established.
RSA key fingerprint is SHA256:hfkuHnZoBJ6c4SWJHcfKFwWG9pRr3YqnNmsnaQhlNSA.
RSA key fingerprint is MD5:dc:10:38:36:37:eb:c2:aa:48:7d:72:41:19:f5:23:0e.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.168.0.104' (RSA) to the list of known hosts.
root@192.168.0.104's password: 
httpd.crt                                                                         100% 5881     2.8MB/s   00:00    
```


**查看证书中的信息：**
`openssl  x509  -in /etc/pki/CA/certs/httpd.crt  -noout  -serial  -subject`

```
[root@centos7 ~]# openssl  x509  -in /etc/pki/CA/certs/httpd.crt  -noout  -serial  -subject
serial=01
subject= /C=CN/ST=Jiangsu/O=MageEdu/OU=Ops/CN=www.magedu.com/emailAddress=webmaster@magedu.com
```

##### 吊销证书：
**步骤：**
(1) 客户端获取要吊销的证书的serial（在使用证书的主机执行）：
`openssl  x509  -in /etc/pki/CA/certs/httpd.crt  -noout  -serial  -subject`
(2) CA主机吊销证书
先根据客户提交的serial和subject信息，对比其与本机数据库index.txt中存储的是否一致；
						
**吊销：**
`openssl  ca  -revoke  /etc/pki/CA/newcerts/SERIAL.pem`
其中的SERIAL要换成证书真正的序列号；

(3) 生成吊销证书的吊销编号（第一次吊销证书时执行）
`echo  01  > /etc/pki/CA/crlnumber`

(4) 更新证书吊销列表
`openssl  ca  -gencrl  -out  thisca.crl `
						
查看crl文件：
`openssl  crl  -in  /PATH/FROM/CRL_FILE.crl  -noout  -text`
							

​		