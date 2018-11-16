---
title: RSA加解密签名用法
date: 2017-08-11 11:26:23
tags:
top: 100
copyright : ture
---

#### 前言
非对称加密技术是保证我们信息安全非常重要的技术。 RSA便是其中翘楚， 应用广泛。本文仅从实用的角度， 总结一下`js`语言中， RSA加解密和签名加解密的实现。

<!--more-->

分享两个库，都是用来加解密的，https://github.com/kjur/jsrsasign https://github.com/travist/jsencrypt
由于`jsrsasign`库加密业务数据总是出现报错，之后就用了`jsencrypt`来实现了业务数据的加密，再通过`jsrsasign`来实现签名以及签名校验

`首先我们要知道加密解密的实现，要明白怎么去加密，又怎么去解密，这个地方就要求我们知道非对称密钥对的概念，什么是密钥对，就是双方之间互相提供公钥来实现加密解密用的密码
，实现加解密要以下几个步骤：
　　（1）乙方生成两把密钥（公钥和私钥）。公钥是公开的，任何人都可以获得，私钥则是保密的。
　　（2）甲方获取乙方的公钥，然后用它对信息加密。
　　（3）乙方得到加密后的信息，用私钥解密。`

```javascript
export const encryptMethod = (bussinessParams: Object) => {
  try {
    //这部分是加密业务数据的方法
    const encrypt = new JSEncrypt()
    encrypt.setPublicKey(publicKey)
    const encrypted = encrypt.encrypt(encodeURIComponent(
      JSON.stringify(bussinessParams)))
    console.log("公钥加密后，原文 : " + encrypted);
    return encrypted
    // 私钥解密
    var decrypt = new JSEncrypt();
    decrypt.setPrivateKey(privKey);
    console.log("公钥加密密文 : " + encrypted);
    var uncrypted = decrypt.decrypt(encrypted);
    console.log("私钥解密后，原文 : " + uncrypted);
  } catch (e) {
    Alert.alert(`RSA ${e}`)
  }
}

```

上面诉说的是业务数据的加密

`我们加密完业务数据信息还想要一种东西来验证我的业务数据是否被串改，这个时候就需要签名的方法来实现验证是否串改信息，签名是需要拿己方私钥对加密过的业务数据进行签名，
签完名之后把加密数据和签名串都传给对方，对方用己方提供的公钥进行签名的校验，直接上代码`

```javascript
export const sign = () => {
  try {
    const sig = new KJUR.crypto.Signature({ alg: 'SHA1withRSA' })
    sig.init(privateKey)
    sig.updateString(encrypted)//此处encrypted是加密的业务数据
    const hSigVal = encodeURIComponent(sig.sign())

    const sig1 = new KJUR.crypto.Signature({ alg: 'SHA1withRSA' })
    sig1.init(publicKey)//此处需要公钥去对签名进行校验
    sig1.updateString(encrypted)
    const verified = sig1.verify(hSigVal)
    
    console.log("签名校验后结果 : " + verified);
  } catch (e) {
    Alert.alert(`RSA ${e}`)
  }
}
```

以下是签名的api文档：https://kjur.github.io/jsrsasign/api/symbols/KJUR.crypto.Signature.html
签名校验的demo：https://kjur.github.io/jsrsasign/sample/sample-rsasign.html
命令生成密钥文档：http://www.cnblogs.com/littleatp/p/5878763.html


`JavaScript RSA 超长字符加解密刚刚接触到RSA当时不了解，RSA加解密是有字符串长度限制的，加密最大字符长度是117位，解密最大长度是128位。
用到的JS库JSEncrypt,刚开始找了好几个,发现就这个好用一些,换回来了(途中遇到很多坑，一步一个坑过来的)。
这个库的缺陷：没法用公钥解密只能加密，私钥没法加密只能
`

分段加密方法：
```javascript
// The right encryption code
JSEncrypt.prototype.encryptLong = function(string) {  
  var k = this.getKey();
  var maxLength = (((k.n.bitLength()+7)>>3)-11);//此处是算法来算出你的密钥可以支持的最大加密字符串长度

  try {
    var lt = "";
    var ct = "";

    if (string.length > maxLength) {
      lt = string.match(/.{1,117}/g);
      lt.forEach(function(entry) {
        var t1 = k.encrypt(entry);
        ct += t1 ;
      });
      return hex2b64(ct);
    }
    var t = k.encrypt(string);
    var y = hex2b64(t);
    return y;
  } catch (ex) {
    return false;
  }
};
```


分段解密方法：
```javascript
// The error decryption code
JSEncrypt.prototype.decryptLong = function(string) {
  var k = this.getKey();
  var maxLength = ((k.n.bitLength()+7)>>3);
  try {
    var string = b64tohex(string);
    var ct = "";
    if (string.length > maxLength) {
      var lt = string.match(/.{1,128}/g);
      lt.forEach(function(entry) {
        var t1 = k.decrypt(entry);
        ct += t1;
      });
    }
    var y = k.decrypt(b64tohex(string));
    return y;
  } catch (ex) {
    return false;
  }
};
```

此篇文章只是个人在使用这两个库中的使用的感受，`jsencrypt`可以加解密但是不存在签名方法， `jsrsasign`可以签名，但是使用过程中加密方法报错。
初次写文章，不到之处还请轻喷！！！


