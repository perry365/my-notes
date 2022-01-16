# HTTPS

## HTTP的缺点
- 通信使用明文（不加密），内容可能被窃听。
- 不验证通信方的身份，有可能遭遇伪装。
- 无法证明报文的完整性，有可能已遭篡改。

明文的协议都有可能出现上述问题。针对上述问题的常见处理方式如下：

- 加密处理防止窃听
    - 将通信加密。通过和SSL（Secure Socket Layer，安全套接层）或TLS（Transport Layer Security，安全传输层协议）组合使用，加密HTTP的通信内容。
    - 将内容加密。对报文主体(body)内容加密.不同于将通信线路加密，仍有被篡改的风险。
- 查明对方的证书。SSL可以确定通信方。SSL不仅可以提供加密处理，而且提供一种被称为证书的手段，确定通信方。
- 防止篡改。散列值校验（MD5、SHA-1）等。不是绝对安全，不排除MD5本身被改写的可能。

---

## HTTP+加密+认证+完整性保护=HTTPS

HTTPS并非应用层的新协议。只是HTTP通信接口部分用SSL或TLS协议代替而已。


应用(HTTP) | 应用(HTTP)
---|---
TCP | SSL
IP | TCP
-  | IP

SSL独立于HTTP，SSL还可以用于其他运行在应用层的SMTP或Telnet等协议。

### 公开秘钥加密技术

SSL采用公开秘钥加密（Public-key cryptography）.近代加密算法中，加密算法是公开的，而秘钥却是保密的。

#### 共享秘钥

加密和解密用同一秘钥的方式成为共享秘钥加密（Common key crypto system）,也称对称秘钥加密。

发送秘钥就有被窃听的风险，但不发送，对方就不能解密。
秘钥若能够安全传送，那么数据也应该能安全送达。

#### 公开秘钥
公开秘钥加密使用一对非对称的秘钥，一把叫做私有秘钥（private key），另一把叫做公开秘钥（public key）。

发送密文的一方使用对方的公开秘钥进行加密，对方收到被加密的信息后，使用自己的私有秘钥进行解密。这种方式，不需要发送用来解密的私有秘钥，也不用担心秘钥被攻击者窃听而盗走。

### HTTPS采用混合加密机制

公开秘钥加密方式比共享秘钥更为复杂，若通信时使用公开秘钥加密，效率就很低。

1. 使用公开秘钥加密方式，安全的交换，在稍后的共享秘钥加密中使用的秘钥。
2. 确保交换的秘钥安全的前提下，使用共享秘钥加密方式进行通信。


#### 证明公开秘钥正确性的证书

- 可以由数字证书认证机构（CA，Certificate Authority）和其相关机关颁发的公开秘钥证书。公钥证书又称数字证书或证书。
- 由自认证机构颁发的证书叫做自签名证书。使用OpenSSL可以给自己颁发证书。


### HTTPS的安全通信机制


```
sequenceDiagram
客户端->>服务器: 1.HandShake:开始SSL通信
服务器->>客户端: 2.HandShake: 应答
服务器->>客户端: 3.HandShake: Certificate报文(公钥证书)
服务器->>客户端: 4.HandShake: SSL握手协商结束
客户端->>服务器: 5.HandShake:Client Key Exchange
客户端->>服务器: 6.ChangeCipherSpec
客户端->>服务器: 7.HandShake:Finished
服务器->>客户端: 8.ChangeCipherSpec
服务器->>客户端: 9.HandShake:Finished
客户端->>服务器: 10.Application Data(HTTP)
服务器->>客户端: 11.Application Data(HTTP)
客户端->>服务器: 12.关闭连接 close_notify
```

#### SSL和TLS

主流版本SSL3.0和TLS1.0。
TSL是以SSL为原型开发的协议，有时统称为SSL。

#### HTTPS慢
HTTPS慢主要是因为SSL慢，SSL慢分两种：
- 通信慢
- 由于大量消耗CPU和内存资源，导致处理速度慢。

HTTPS一般比HTTP慢2-100倍。







