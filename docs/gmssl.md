GmSSL for Bash 
===

这个 [GmSSL: 支持国密SM2/SM3/SM4/SM9/SSL的密码工具箱 ](https://github.com/guanzhi/GmSSL)快速参考备忘单展示了它的常用命令使用清单



## 主要特性

### 主要特性

<!--rehype:wrap-class=col-span-3-->

超轻量：GmSSL 3 大幅度降低了内存需求和二进制代码体积，不依赖动态内存，可以用于无操作系统的低功耗嵌入式环境(MCU、SOC等)，开发者也可以更容易地将国密算法和SSL协议嵌入到现有的项目中。

更合规：GmSSL 3 可以配置为仅包含国密算法和国密协议(TLCP协议)，依赖GmSSL 的密码应用更容易满足密码产品型号检测的要求，避免由于混杂非国密算法、不安全算法等导致的安全问题和合规问题。

更安全：TLS 1.3在安全性和通信延迟上相对之前的TLS协议有巨大的提升，GmSSL 3 支持TLS 1.3协议和RFC 8998的国密套件。GmSSL 3 默认支持密钥的加密保护，提升了密码算法的抗侧信道攻击能力。

跨平台：GmSSL 3 更容易跨平台，构建系统不再依赖Perl，默认的CMake构建系统可以容易地和Visual Studio、Android NDK等默认编译工具配合使用，开发者也可以手工编写Makefile在特殊环境中编译、剪裁。



## 编译与安装

### cmake构建

GmSSL 3 采用了`cmake`构建系统。下载源代码后将其解压缩，进入源码目录，执行：

```bash
mkdir build
cd build
cmake ..
make
make test
sudo make install
```

在`make install`完成后，GmSSL会在默认安装目录中安装`gmssl`命令行工具，在头文件目录中创建`gmssl`目录，并且在库目录中安装`libgmssl.a`、`libgmssl.so`等库文件。

### Visual Studio环境编译

在`Visual Studio`命令提示符下执行：

```bash
mkdir build
cd build
cmake .. -G "NMake Makefiles"
nmake
```

## 入门

### 基础

gmssl版本

```bash
$ gmssl version
```

gmssl所有命令

```bash
$ gmssl help
```

gmssl子命令参数含义

```bash
$ gmssl sms4-ecb -help
```

### 命令行中使用SM3

`SM3`计算摘要：

```bash
$ echo -n hello | gmssl dgst -sm3
```

`SM3`计算HMAC：

```bash
$ echo -n hello | gmssl dgst -sm3 -hmac 11223344556677881122334455667788
```

### 命令行中使用SM4(默认CBC模式)

`SM4`对输入字符加密：

```bash
$ echo hello | gmssl enc -sms4-cbc > ciphertext.bin
```

`SM4`对加密文件解密

```bash
$ cat cipehrtext.bin | gmssl enc -sms4-cbc -d
```

`SM4-CBC`模式对test.txt文件进行加解密：

```bash
$gmssl sms4-cbc -K 11223344556677881122334455667788 -iv 11223344556677881122334455667788 -in test.txt -out testcbc.txt -e

$gmssl sms4-cbc -K 11223344556677881122334455667788 -iv 11223344556677881122334455667788 -in testcbc.txt -out testmm.txt -d 
```

### 命令行中使用SM2

`SM2`生成私钥(对生成的`SM2`私钥使用`SM4`进行加密后输出)

```bash
$ gmssl sm2 -genkey -sms4 -out sm2.pem 
```

导出`SM2`公钥

```bash
$ gmssl sm2 -in sm2.pem -pubout -out sm2Pub.pem
```

`SM2`签名

```bash
$ gmssl sm2utl -sign -in msg.txt -inkey sm2.pem -id zongpengxin -out sig.der
```

`SM2`验签

```bash
$ gmssl sm2utl -verify -in msg.txt -sigfile sig.der -pubin -in sm2Pub.pem -id Alice
```

`SM2`加密

```bash
$ gmssl sm2utl -encrypt -in msg.txt -pubin -inkey sm2Pub.pem -out enced.der
```

`SM2`解密

```bash
$ gmssl sm2utl -decrypt -in enced.der -inkey sm2.pem
```

### 命令行中使用SM9

<!--rehype:wrap-class=col-span-2-->

生成`SM9 `主公钥/主私钥

```bash
$ gmssl sm9setup -alg sm9sign -pass 1234 -out sign_msk.pem -pubout sign_mpk.pem
```

使用主私钥生成用户私钥(使用outpass参数指定的口令加密)

```bash
$ gmssl sm9keygen -alg sm9sign -in sign_msk.pem -inpass 1234 -id alice -out alice.pem -outpass 1234
```

使用用户私钥对数据进行签名，生成签名

```bash
$ echo hello | gmssl sm9sign -key alice.pem -pass 1234  -out hello.sig
```

使用主公钥对签名数据进行验签

```bash
$ echo hello | gmssl sm9verify -pubmaster sign_mpk.pem -id alice -sig hello.sig
```

使用主公钥以及用户id进行加密

```bash
$ echo hello | gmssl sm9encrypt -pubmaster enc_mpk.pem -id bob -out hello.der
```

使用用户私钥解密

```bash
$ gmssl sm9decrypt -key bob.pem -pass 1234 -id bob -in hello.der
```

### 命令行中使用ZUC

注：由于ZUC算法是对称加密算法，所以加密解密的操作是一样的。

设置ZUC算法加密所需的秘钥和IV

```bash
KEY=11223344556677881122334455667788
IV=11223344556677881122334455667788
```

使用秘钥和IV对数据进行加密和解密

```bash
$ echo hello | gmssl zuc -key $KEY -iv $IV -out zuc.bin
$ gmssl zuc -key $KEY -iv $IV -in zuc.bin
```

### PBKDF2算法

使用PBKDF2算法，对password 1234 和salt进行6000次哈希计算

```bash
$ gmssl pbkdf2 -pass 1234 -salt 1122334455667788 -iter 60000 -outlen 16
```

注：PBKDF2(Password-Based Key Derivation Function) 应用一个伪随机函数以导出密钥。导出密钥的长度本质上是没有限制的（但是，导出密钥的最大有效搜索空间受限于基本伪随机函数的结构）。

它的基本原理是通过一个伪随机函数（例如 HMAC 函数），把明文和一个盐值作为输入参数，然后重复进行运算，并最终产生密钥。


### 数字证书

生成自签名证书

```bash
gmssl sm2keygen -pass 1234 -out sm2.pem -pubout sm2pub.pem

gmssl certgen -C CN -ST Beijing -L Haidian -O PKU -OU CS -CN Alice -days 365 -key sm2.pem -pass 1234 \
  -key_usage "digitalSignature" -key_usage "keyCertSign" -key_usage cRLSign \
  -out cert.pem
```

解析数字证书

```bash
gmssl certparse -in cert.pem
```
