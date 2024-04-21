# mbedTLS

 ARM mbedtls使开发人员可以非常轻松地在嵌入式产品中加入加密和 SSL/TLS 功能。它提供了具有直观的 API 和可读源代码的 SSL 库。该工具即开即用，可以在大部分系统上直接构建它，也可以手动选择和配置各项功能。

## 应用层 API

### mbedtls 初始化

mbedtls 客户端初始化函数，用于初始化底层网络接口、设置证书、设置 SSL 会话等。

```cpp
int mbedtls_client_init(MbedTLSSession *session, void *entropy, size_t entropyLen);
```

| 参数       | 描述                            |
| ---------- | ------------------------------- |
| `session`  | mbedtls 会话对象 MbedTLSSession |
| entropy    | mbedtls 熵字符串                |
| entropyLen | mbedtls 熵字符串长度            |
| `返回`     | 描述                            |
| = 0        | 成功                            |
| !0         | 失败                            |

### 配置 mbedtls 上下文

SSL 层配置，应用程序使用 mbedtls_client_context 函数配置客户端上下文信息，包括证书解析、设置主机名、设置默认 SSL 配置、设置认证模式（默认 MBEDTLS_SSL_VERIFY_OPTIONAL）等。

```cpp
int mbedtls_client_context(MbedTLSSession *session);
```

| 参数     | 描述 |
| -------- | ---- |
| `返回`   | 描述 |
| = 0      | 成功 |
| !0       | 失败 |

### 建立 SSL/TLS 连接

使用 `mbedtls_client_connect` 函数为 SSL/TLS 连接建立通道。这里包含整个的握手连接过程，以及证书校验结果。

```cpp
int mbedtls_client_connect(MbedTLSSession *session);
```
| 参数     | 描述 |
| -------- | ---- |
| `返回`   | 描述 |
| = 0      | 成功 |
| !0       | 失败 |
### 向加密连接写入数据

```cpp
int mbedtls_client_write(MbedTLSSession *session, const unsigned char *buf , size_t len);
```

### 从加密连接读取数据

```cpp
int mbedtls_client_read(MbedTLSSession *session, unsigned char *buf , size_t len);
```

### 关闭 mbedtls 客户端

```cpp
int mbedtls_client_close(MbedTLSSession *session);
```

客户端主动关闭连接或者因为异常错误关闭连接，都需要使用 `mbedtls_client_close` 关闭连接并释放资源。

## 代码层

###  常用配置

<!--rehype:wrap-class=col-span-3-->

系统中相关配置已有默认值，如需修改配置，统一在`platform/include/config_aos_base.h`中修改，具体如下：

> 使能加解密: 默认使能, 如需关闭，可修改配置如：

```cpp
def_config:
  MBEDTLS_CONFIG_CRYPTO: 0
```

> 使能MD5: 默认使能，如需关闭，可修改配置如：

```cpp
def_config:
  MBEDTLS_CONFIG_CRYPTO_MD5: 0
```

> 使能SHA1: 默认使能，如需关闭，可修改配置如：

```cpp
def_config:
  MBEDTLS_CONFIG_CRYPTO_SHA1: 0
```

> 使能SHA256: 默认使能，如需关闭，可修改配置如：

```cpp
def_config:
  MBEDTLS_CONFIG_CRYPTO_SHA256: 0
```

> 使能SHA512: 默认不使能，如需使能，可修改配置如：

```cpp
def_config:
  MBEDTLS_CONFIG_CRYPTO_SHA512: 1
```

> 使能RIPEMD160: 默认不使能，如需使能，可修改配置如：

```cpp
def_config:
  MBEDTLS_CONFIG_CRYPTO_RIPEMD160: 1
```

> 使能ARIA: 默认不使能，如需使能，可修改配置如：

```cpp
def_config:
  MBEDTLS_CONFIG_CRYPTO_ARIA: 1
```

> 使能DES: 默认不使能，如需使能，可修改配置如：

```cpp
def_config:
  MBEDTLS_CONFIG_CRYPTO_DES: 1
```

> 使能AES: 默认使能，如需关闭，可修改配置如：

```cpp
def_config:
  MBEDTLS_CONFIG_CRYPTO_AES: 0
```

> 使用AES ROM表: 默认不使能，如需使能，可修改配置如：

```cpp
def_config:
  MBEDTLS_CONFIG_CRYPTO_AES_ROM_TABLES: 1
```

> 使用较少的AES ROM/RAM表: 默认不使能，如需使能，可修改配置如：

```cpp
def_config:
  MBEDTLS_CONFIG_CRYPTO_AES_FEWER_TABLES: 1
```

> 使能CBC加密模式: 默认使能，如需关闭，可修改配置如：

```cpp
def_config:
  MBEDTLS_CONFIG_CRYPTO_MODE_CBC: 0
```

> 使能CFB加密模式: 默认不使能，如需使能，可修改配置如：

```cpp
def_config:
  MBEDTLS_CONFIG_CRYPTO_MODE_CFB: 1
```

> 使能CTR加密模式: 默认不使能，如需使能，可修改配置如：

```cpp
def_config:
  MBEDTLS_CONFIG_CRYPTO_MODE_CTR: 1
```

> 使能OFB加密模式: 默认不使能，如需使能，可修改配置如：

```cpp
def_config:
  MBEDTLS_CONFIG_CRYPTO_MODE_OFB: 1
```

> 使能XTS加密模式: 默认不使能，如需使能，可修改配置如：

```cpp
def_config:
  MBEDTLS_CONFIG_CRYPTO_MODE_XTS: 1
```

> 使能GCM加密模式: 默认不使能，如需使能，可修改配置如：

```cpp
def_config:
  MBEDTLS_CONFIG_CRYPTO_MODE_GCM: 1
```

> 使能CCM加密模式: 默认不使能，如需使能，可修改配置如：

```cpp
def_config:
  MBEDTLS_CONFIG_CRYPTO_MODE_CCM: 1
```

> 使能ARC4加密: 默认不使能，如需使能，可修改配置如：

```cpp
def_config:
  MBEDTLS_CONFIG_CRYPTO_ARC4: 1
```

> 使能BLOWFISH加密: 默认不使能，如需使能，可修改配置如：

```cpp
def_config:
  MBEDTLS_CONFIG_CRYPTO_BLOWFISH: 1
```

> 使能CAMELLIA加密: 默认不使能，如需使能，可修改配置如：

```cpp
def_config:
  MBEDTLS_CONFIG_CRYPTO_CAMELLIA: 1
```

> 使能XTEA加密: 默认不使能，如需使能，可修改配置如：

```cpp
def_config:
  MBEDTLS_CONFIG_CRYPTO_XTEA: 1
```

> 使能CHACHA20加密: 默认不使能，如需使能，可修改配置如：

```cpp
def_config:
  MBEDTLS_CONFIG_CRYPTO_CHACHA20: 1
```

> 使能POLY1305 MAC算法: 默认不使能，如需使能，可修改配置如：

```cpp
def_config:
  MBEDTLS_CONFIG_CRYPTO_POLY1305: 1
```

> 使能CHACHAPOLY AEAD算法: 默认不使能，如需使能，可修改配置如：

```cpp
def_config:
  MBEDTLS_CONFIG_CRYPTO_CHACHAPOLY: 1
```

> 使能X.509证书支持: 默认使能，如需关闭，可修改配置如：

```cpp
def_config:
  MBEDTLS_CONFIG_X509: 0
```

> 使能TLS协议支持: 默认使能，如需关闭，可修改配置如：

```cpp
def_config:
  MBEDTLS_CONFIG_TLS: 0
```

> 使能TLS1.2协议支持: 默认使能，如需关闭，可修改配置如：

```cpp
def_config:
  MBEDTLS_CONFIG_TLS1_2: 0
```

> 使能DTLS协议支持: 默认使能，如需关闭，可修改配置如：

```cpp
def_config:
  MBEDTLS_CONFIG_DTLS: 0
```

> 使能TLS协议调试支持: 默认不使能，如需使能，可修改配置如：

```cpp
def_config:
  MBEDTLS_CONFIG_TLS_DEBUG: 1
```

> TLS/DTLS最大收发报文长度: 单位：字节，默认为4096。可修改配置如2K：

```cpp
def_config:
  MBEDTLS_CONFIG_TLS_MAX_CONTENT_LEN: 2048
```

> 是否包含自测程序: 默认不包含，如需包含，可修改配置如：

```cpp
def_config:
  MBEDTLS_CONFIG_SELFTEST: 1
```

> 使能错误字符串支持: 默认不使能，如需使能，可修改配置如：

```cpp
def_config:
  MBEDTLS_CONFIG_ERROR: 1
```

### AES加密解密

```cpp
mbedtls_aes_context aes_ctx;
//密钥数值  
unsigned char key[16] = { 0x01,0x02,0x03,0x04,0x05,0x06,0x07,0x08,0x09,0x0A,0x0B,0x0C,0x0D,0x0E,0x0F,0x00 };
//明文空间  
unsigned char plain[16] = "helloworld";
//解密后明文的空间  
unsigned char dec_plain[16] = { 0 };
//密文空间  
unsigned char cipher[16] = { 0 };
mbedtls_aes_init(&aes_ctx);
//设置加密密钥  
mbedtls_aes_setkey_enc(&aes_ctx, key, 128);
printf("\n*********** 加密前：%s\n", plain);
mbedtls_aes_crypt_ecb(&aes_ctx, MBEDTLS_AES_ENCRYPT, plain, cipher);
printf("\n*********** 加密后：%s\n", cipher);
//设置解密密钥  
mbedtls_aes_setkey_dec(&aes_ctx, key, 128);
mbedtls_aes_crypt_ecb(&aes_ctx, MBEDTLS_AES_DECRYPT, cipher, dec_plain);
printf("\n*********** 解密后：%s\n", dec_plain);
mbedtls_aes_free(&aes_ctx);
system("pause");
```

###  **MD5**

```cpp
unsigned char encrypt[] = "admin";    //21232f297a57a5a743894a0e4a801fc3
unsigned char decrypt[16];
mbedtls_md5_context md5_ctx;
mbedtls_md5_init(&md5_ctx);
mbedtls_md5_starts(&md5_ctx);
mbedtls_md5_update(&md5_ctx, encrypt, strlen((char *)encrypt));
mbedtls_md5_finish(&md5_ctx, decrypt);
printf("加密前:%s\n加密后:", encrypt);
for (int i = 0; i<16; i++)
{
    printf("%02x", decrypt[i]);
}
mbedtls_md5_free(&md5_ctx);
```



### **SHA1**

```cpp
unsigned char encrypt[] = "admin";
unsigned char decrypt[20];
mbedtls_sha1_context sha1_ctx;
mbedtls_sha1_init(&sha1_ctx);
mbedtls_sha1_starts(&sha1_ctx);
mbedtls_sha1_update(&sha1_ctx, encrypt, strlen((char *)encrypt));
mbedtls_sha1_finish(&sha1_ctx, decrypt);
printf("加密前:%s\n加密后:", encrypt);
mbedtls_sha1_free(&sha1_ctx);
for (int i = 0; i<20; i++)
{
    printf("%02x", decrypt[i]);
}
```



## 可参考

- [《密码技术与物联网安全—mbedtls开发实战》示例代码](https://github.com/iotwuxi/iot_security)





