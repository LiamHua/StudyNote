## 一、原理

 	1. 我们首先要知道Http数据包是没有加密的明文，这意味着任何人只要拦截到了你的数据包就能够查看甚至篡改你与服务端通讯的数据，这也是公共WIFI 极度不安全的原因之一，因为攻击者非常容易拦截到你的通讯数据包。这就是Https诞生的原因，互联网日益发达，安全至关重要。
 	2. Https  =  Http  +  SSL（TLS），TLS 是 SSL 的升级版，两者在概念上是一样的。当前广泛使用的版本为 TLS 1.2.
 	3. TLS 的作用：加密、证书校验、消息完整性校验、
 	4. 论证证书存在的必要性，假如没有证书机制：
 	 + 在http的基础上使用对称加密，显然不可能，因为如何安全传输加密所需的密钥瞬间又成了一个难题
 	 + 使用非对称加密，建立连接后服务端将公钥下发给客户端，注意这里公钥也可能被拦截。以后的数据传输中，客户端发送的数据被拦截后攻击者没有私钥无法解密；服务端发送的数据被拦截后，因前面公钥也可能被拦截故攻击者知道公钥，数据可以解密，这种情况下攻击者可以查看并篡改一方的数据。再进一步，假如用私钥对数据进行签名，那么攻击者将只能查看数据而无法篡改数据，因为攻击者没有私钥是无法篡改签名消息的。似乎安全了许多，但仍不完美。但，还有一种可能，如果攻击者将服务器下发的公钥拦截后将其篡改为自己的公钥的，那么此时就糟糕了。因为这种局面就真正变成了一个“三方通信”，你以为你在跟服务器通信实际上你在与攻击者通信，攻击者可以查看并篡改你与服务器所有的数据。所以非对称加密也不靠谱，只能使用证书机制了。
 	5. 证书机制：证书由CA机构颁发，CA机构是可信任的第三方机构，CA机构也有自己的公钥和私钥，它们与操作系统出品方合作，让操作系统内置它们的根证书，该根证书内包含CA机构的信息和CA机构的公钥。服务端向CA机构申请证书，证书中包含服务端的域名、公钥、有效时间、签名等信息，CA机构使用自己的私钥对这些消息进行加密（一般我们申请证书服务端私钥是由CA机构给我们生成的）。服务端保存该证书，一旦有客户端申请建立Https连接就将该证书发送给客户端，因证书内有签名信息故攻击者无法篡改该证书，唯一有可能的办法是替换为攻击者自己的证书，但这时又需要客户端内置攻击者的根证书才有效，这又是一大难题，所以我们不要相信来源不明的根证书。
 	6. 客户端接收到证书后验证该证书的合法性，验证成功后客户端生成对称加密所需的密钥并用服务端公钥加密，然后发送给服务端，此后客户端与服务端的数据加密由对称加密来完成。



## 二、连接过程

1. TCP 三次握手

![image-20200702172300834](https://pictures.huazai.vip/uPic/image-20200702172300834.png)



2. 客户端向服务端发送 Client Hello 消息，该消息内含有客户端支持的加密套件等信息和随机数1

![image-20200702172803083](https://pictures.huazai.vip/uPic/image-20200702172803083.png)

3. 
   + 服务端向客户端发送Client Hello 的确认ACK
   + 服务端发送 Server Hello，包含服务端选择认同的加密套件和随机数2
   + 服务端发送证书链

![image-20200702184423365](https://pictures.huazai.vip/uPic/image-20200702184423365.png)

![image-20200702184551461](https://pictures.huazai.vip/uPic/image-20200702184551461.png)

4. 客户端生成预备主密钥，并用服务端公钥加密后发送给服务端
5. 双方根据相同的算法用两个随机数和预备主密钥生成主密钥，然后发送Finished消息

大致过程如图：

![image-20200702211944649](https://pictures.huazai.vip/uPic/image-20200702211944649.png)



参考：https://halfrost.com/https_tls1-2_handshake/

参考：https://gameinstitute.qq.com/community/detail/101865



疑问：

1. 两个随机数的具体作用，防止重放攻击？具体啥原理？

 	2. 有的人说预备主密钥就是主密钥，有的人说主密钥是预备主密钥加随机数生成的？直接用预备主密钥不行吗？
