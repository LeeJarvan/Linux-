

### SSL

SSL（Secure Sockets Layer，安全套接字层）是一种安全的加解密协议，它的实现也是需要程序（算法）来实现。ssl是一种公共功能，但ssl本身只是一种规范和协议，需要程序员开发出一种遵循SSL协议规范的程序来实现。
			
**分层设计：**
1、最底层：基础算法原语的实现，aes, rsa, md5
2、向上一层：各种算法的实现； 
3、再向上一层：组合算法实现的半成品；
4、用各种组件拼装而成的各种成品密码学协议软件；
				
**SSL/TLS**
>SSL: 安全套接字层（ssl 1.0, ssl 2.0, ssl 3.0）
>TLS：传输层安全 （tls 1.0, tls 1.1, tls 1.2, tls 1.3）


#### SSL会话主要三步：
①客户端向服务器端索要并验正证书；
②双方协商生成“会话密钥”；
③双方采用“会话密钥”进行加密通信；
			
#### SSL Handshake Protocol：
>**第一阶段：ClientHello**
>支持的协议版本，比如tls 1.2；
>客户端生成一个随机数，稍后用户生成“会话密钥”
>支持的加密算法，比如AES、3DES、RSA；
>支持的压缩算法；
>					
>**第二阶段：ServerHello**
>确认使用的加密通信协议版本，比如tls 1.2；
>服务器端生成一个随机数，稍后用于生成“会话密钥”
>确认使用的加密方法；
>服务器证书；
>					
>**第三阶段**
>验正服务器证书，在确认无误后取出其公钥；（发证机构、证书完整性、证书持有者、证书有效期、吊销列表）				
>发送以下信息给服务器端：
>编码变更通知，表示随后的信息都将用双方商定的加密方法和密钥发送；
>客户端握手结束通知；
>						
>**第四阶段**
>收到客户端发来的第三个随机数pre-master-key后，计算生成本次会话所有到的“会话密钥”；
>向客户端发送如下信息：
>编码变更通知，表示随后的信息都将用双方商定的加密方法和密钥发送；
>服务端握手结束通知；
>![](https://upload-images.jianshu.io/upload_images/16547068-f9044d8815ae2c00.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### SSL应用（参考示例）
以https通信过程为例描述SSL的实现过程：
>HTTPS（全称：Hyper Text Transfer Protocol over Secure Socket Layer），是以安全为目标的HTTP通道，简单讲是HTTP的安全版。即HTTP下加入SSL层，HTTPS的安全基础是SSL。



![](https://upload-images.jianshu.io/upload_images/16547068-6e3bfe08413ba6c9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

以https为例，进一步说明如何依靠CA来可靠的获得通信对方的公钥，如图：



![](https://upload-images.jianshu.io/upload_images/16547068-000531d7ee06a819.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)





**https的主要实现过程说明：**

>（1）在通信之前，服务器端通过加密算法生成一对密钥，并把其公钥发给CA申请数字证书，CA审核后，结合服务端发来的相关信息生成数字证书，并把该数字证书发回给服务器端。
>（2）客户端和服务器端经tcp三次握手，建立初步连接。
>（3）客户端发送http报文请求并协商使用哪种加密算法。
>（4）服务端响应报文并把自身的数字签名发给服务端。
>（5）客服端下载CA的公钥，验证其数字证书的拥有者是否是服务器端（这个过程可以得到服务器端的公钥）。（一般是客户端验证服务端的身份，服务端不用验证客户端的身份。）
>（6）如果验证通过，客户端生成一个随机对称密钥，用该密钥加密要发送的URL链接申请，再用服务器端的公钥加密该密钥，把加密的密钥和加密的URL链接一起发送到服务器。
>（7）服务器端使用自身的私钥解密，获得一个对称密钥，再用该对称密钥解密经加密的URL链接，获得URL链接申请。
>（8）服务器端根据获得的URL链接取得该链接的网页，并用客户端发来的对称密钥把该网页加密后发给客户端。
>（9）客户端收到加密的网页，用自身的对称密钥解密，就能获得网页的内容了。
>（10）TCP四次挥手，通信结束。


### OpenSSL
**组件：**
>libcrypto, libssl主要由开发者使用
>openssl：多用途命令行工具

#### openssl:
从多子命令，分为三类：
- 标准命令
- 消息摘要命令（dgst子命令）
- 加密命令（enc子命令）

```
[root@promote ~]# openssl version
OpenSSL 1.0.2k-fips  26 Jan 2017
[root@promote ~]# openssl ?
openssl:Error: '?' is an invalid command.

Standard commands
asn1parse         ca                ciphers           cms               
crl               crl2pkcs7         dgst              dh                
dhparam           dsa               dsaparam          ec                
ecparam           enc               engine            errstr            
gendh             gendsa            genpkey           genrsa            
nseq              ocsp              passwd            pkcs12            
pkcs7             pkcs8             pkey              pkeyparam         
pkeyutl           prime             rand              req               
rsa               rsautl            s_client          s_server          
s_time            sess_id           smime             speed             
spkac             ts                verify            version           
x509              

Message Digest commands (see the `dgst' command for more details)
md2               md4               md5               rmd160            
sha               sha1              

Cipher commands (see the `enc' command for more details)
aes-128-cbc       aes-128-ecb       aes-192-cbc       aes-192-ecb       
aes-256-cbc       aes-256-ecb       base64            bf                
bf-cbc            bf-cfb            bf-ecb            bf-ofb            
camellia-128-cbc  camellia-128-ecb  camellia-192-cbc  camellia-192-ecb  
camellia-256-cbc  camellia-256-ecb  cast              cast-cbc          
cast5-cbc         cast5-cfb         cast5-ecb         cast5-ofb         
des               des-cbc           des-cfb           des-ecb           
des-ede           des-ede-cbc       des-ede-cfb       des-ede-ofb       
des-ede3          des-ede3-cbc      des-ede3-cfb      des-ede3-ofb      
des-ofb           des3              desx              idea              
idea-cbc          idea-cfb          idea-ecb          idea-ofb          
rc2               rc2-40-cbc        rc2-64-cbc        rc2-cbc           
rc2-cfb           rc2-ecb           rc2-ofb           rc4               
rc4-40            rc5               rc5-cbc           rc5-cfb           
rc5-ecb           rc5-ofb           seed              seed-cbc          
seed-cfb          seed-ecb          seed-ofb          zlib   
```

##### 对称加密：
工具：`openssl  enc`, ` gpg`
支持的算法：3des, aes, blowfish, towfish
			
**enc命令：**
加密：`openssl  enc  -e  -des3  -a  -salt  -in fstab   -out fstab.ciphertext`

```
[root@promote ~]# openssl enc -e -des3 -a -salt -in fstab -out fstab.ciphertext
enter des-ede3-cbc encryption password:
Verifying - enter des-ede3-cbc encryption password:
[root@promote ~]# cat fstab.ciphertext
U2FsdGVkX19XpMD+Z0r8qS41Ov8IqY7LHDtswe7qy7amEb6sxIzXr/K6HW0w2qWq
rzNUUZGemEjdiYikaLQePfxVgreuqcOAVmrn+VNDt+gYdTw+cCK3v9jSLBxuJOXc
Hb2deMJdk+j0umCRnS+Db/cy5M1Kj4eXckRu4Yjft0oZkoRG8u5CIj81rL4qwMXB
2AAOsDl8ZPakVDh7TyYvqWndpH2gFTdYcw1NyOOFqCgUV5bE11LSOSA0U69CCFqa
ijSHky/uujwP2M08pFQAogUN6KfLjvqq5frFhR6nWHT1k7UHwYnWSNPTTDLw0nQf
Vo/QiyTqVZ+tdBqdd08lKcavDImGOrGkhyqDi9f4UaAzXkL22YW1WUhFZ8mstsjd
PbEp6vkzgKz2k90pbf3p9JUxYbIKHgcjrBfclDQmaMPQTCw4rDy1gm6tfXPhad3g
Nin8p78lNR43VfHYbf0HpMCZClh8ZPCNgIbO/CGnJNJOf7uozK3/fhCntg8oVvVK
q9y6SXySYrM/wC6kbF13u22mOB4NRGCwHITDPLvTOOJ78JEd0HWPgwkfqGgMvMSp
BiRg6MounfixNZLECwGLyRoT+S7To4oiDzpvs+JV9xWaVnEB8yaS0Swmi6YxVmI6
REHhw2WrCV3X7HQLCXOMuGXkNMh24w2TRNTYidC7SaPmIK7V5DVhkBKM4M/IlKq1
uTtJaU/DXUCUF34eYlGVsmw9QpUD5/SxUjcWpX8woljthwIvUy7kHvPctp0lwBZP
miKiCYlpG00=
```
解密：`openssl  enc  -d  -des3  -a  -salt  -out fstab   -in fstab.ciphertext`

```
[root@promote ~]# openssl enc -d -des3 -a -salt -out fstab -in fstab.ciphertext
enter des-ede3-cbc decryption password:
[root@promote ~]# cat fstab

#
# /etc/fstab
# Created by anaconda on Sun Apr 14 09:29:51 2019
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/centos_promote-root /                       xfs     defaults        0 0
UUID=273deb66-d03c-457f-8b29-5df019b3e53a /boot                   xfs     defaults        0 0
/dev/mapper/centos_promote-home /home                   xfs     defaults        0 0
/dev/mapper/centos_promote-swap swap                    swap    defaults        0 0
```
##### 单向加密：
工具：openssl dgst, md5sum, sha1sum, sha224sum, ...
			
**dgst命令：**
`openssl  dgst  -md5  /PATH/TO/SOMEFILE`

```
[root@promote ~]# md5sum fstab
c58fef6dca558db2e6122bdf12f66aba  fstab
[root@promote ~]# openssl dgst -md5 fstab
MD5(fstab)= c58fef6dca558db2e6122bdf12f66aba
```

**生成用户密码：**
工具：passwd, openssl  passwd
`openssl  passwd  -1  -salt  SALT`

```
[root@promote ~]# openssl passwd -1 -salt 12345678
Password: 
$1$12345678$2MUF23c1xwXhNSpW3dupd.
```

**生成随机数：**
工具：openssl  rand
`openssl  rand  -hex  NUM`
`openssl  rand  -base  NUM`

```
[root@promote ~]# openssl rand -base64 12
0iMFITFdgsHDsKLA
[root@promote ~]# openssl rand -hex 12
6e02d827b1944dfe66e18b93
```

##### 公钥加密：
**加密解密：**
算法：RSA，ELGamal
工具：openssl  rsautl, gpg

**数字签名：**                       
算法：RSA， DSA， ELGamal
工具：openssl  rsautl, gpg

##### 密钥交换：
算法：**DH**
				
**生成密钥：**
生成私钥：`(umask 077;  openssl  genrsa  -out  /PATH/TO/PRIVATE_KEY_FILE  NUM_BITS)`

```
[root@promote ~]# (umask 077;openssl genrsa -out /tmp/mykey3.private 2048)
Generating RSA private key, 2048 bit long modulus
...............................................................................................................................................+++
........................................................................................................................................+++
e is 65537 (0x10001)
```
提出公钥：`openssl  rsa  -in  /PATH/FROM/PRIVATE_KEY_FILE  -pubout`

```
[root@promote ~]# openssl rsa -in /tmp/mykey3.private -pubout
writing RSA key
-----BEGIN PUBLIC KEY-----
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAslrkVrMPj7efIJ1Pt337
sVVguLyu1xdU4BpYZJEEXncik0+UtlzQ6yR2tpys0LxDac2urL4dLhk5S/khU71j
zm4w3yZPolxMP0hAORisx4QmVN+QRfayWSCnR2RUI73Xn8hnY7fPGN7kZD5ZlMnd
H0aUiuSMugQQR5ZqJmzmHUqcsfT/flAQDAhSChFlseTJ4y93uHR8zGou4cR3mluL
gGSXAtokj5+9m4Q0pMQuuu+qaZyfJu+3iv1OgXNi5BOFwhIZdrZv64W+nx0C9sUb
HjpFliMss0tCAebxM6fwjaxqv+8Is156THXfSvIVDcqkSU0Uckx3OKUcWFK8SVyD
6wIDAQAB
-----END PUBLIC KEY-----
```

#### Linux系统上的随机数生成器：
>/dev/random：仅从熵池返回随机数；随机数用尽，则阻塞；
>
>/dev/urandom：从熵池返回随机数；随机数用尽，会利用软件生成伪随机数，非阻塞；伪随机数不安全；

##### 熵池中随机数的来源：
>硬盘IO中断时间间隔；
>键盘IO中断时间间隔；
