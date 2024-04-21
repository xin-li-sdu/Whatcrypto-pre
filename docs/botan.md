 Botan
====

Botan是一个用于`C++`的加密算法库，提供了广泛的加密算法和工具，包括对称加密、非对称加密、散列函数、数字签名等。

## 入门

### AES示例

引入头文件\#include <botan/botan.h>，botan库内所有的类、接口大多是声明在一个叫做Botan的名字空间里，你可以用普通引用的方法，如果要懒可以直接using namespace Botan;下面的代码展示如何使用Botan库中的算法进行AES128/CBC对称加密，AES128所需的128位密钥通过MD5获得：

- `cryptoAES(string input, string passphrase, Cipher_Dir opt)`：用于进行 AES 加密或解密的函数。
- `input`：要加密或解密的输入字符串。
- `passphrase`：用作加密密钥的密码。
- `opt`：操作类型，表示是加密还是解密。
- `hash`：使用 MD5 哈希算法从密码生成密钥。
- `key`：生成的密钥。
- `raw_iv`：使用密码生成的初始化向量（IV）的原始数据。
- `iv`：初始化向量对象，用于 AES 加密或解密。
- `pipe`：管道对象，用于将数据流连接到 AES 加密或解密器。
- `output`：加密或解密后的结果。

```cpp
string cryptoAES(string input,string passphrase,Cipher_Dir opt) 
{    
    HashFunction* hash = get_hash("MD5");
    SymmetricKey key = hash->process(passphrase);
    SecureVector<byte> raw_iv = hash->process('0'+ passphrase);
    InitializationVector iv(raw_iv, 16);
    Pipe pipe(get_cipher("AES-128/CBC", key, iv, opt));
    pipe.process_msg(input);
    string output=pipe.read_all_as_string();
    return output;
}
```

### RSA示例

<!--rehype:wrap-class=col-span-2-->

- `testRSAPublicKeyEncode()`：测试 RSA 公钥编码的函数。
- `pemData`：包含 PEM 格式公钥数据的字节数组。
- `publicKey`：使用 Botan 加载的公钥对象。
- `en`：PKCS#1 v1.5 填充方案的 RSA 加密器。
- `ans`：加密后的结果。

需要注意的是，此代码假定 PEM 格式的公钥已正确加载，并且使用了 PKCS#1 v1.5 填充方案进行加密：

```cpp
void MainWindow::testRSAPublicKeyEncode() 
{
    using Botan::byte;
    using Botan::Public_Key;
    using Botan::PK_Encryptor_EME;
    constexpr byte pemData[]=u8R"///(-----BEGIN PUBLIC KEY-----
MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDoUaLxcCvcgWzS1ukXC56F9YWx
PlmuwHa9zPFR4+tinzhtG8PRSgPB7UOAzaFu4hKimPOcOrQaRUof/PhSNGxZAhad
d+/kot0I8y7pwzeza9EAEwpQIki7RextKlWDRLRvaQGLjnW7SUrF2k62zJCdtow0
KcfR+Hx1+7Z4wYYFHwIDAQAB
-----END PUBLIC KEY-----
)///";
    Botan::MemoryVector<byte> pem(pemData,sizeof(pemData)-1);
    std::unique_ptr<Public_Key> publicKey{ Botan::X509::load_key(pem) };
    Botan::AutoSeeded_RNG rng;
    PK_Encryptor_EME en(*publicKey,"EME-PKCS1-v1_5");
    auto ans=en.encrypt((const byte*)"12345",5,rng);
    std::cout<<"encode rsa is : ";
    for (const auto varI:ans) {
        std::cout<<std::hex<<int(varI);
    }
    std::cout<<std::endl;
}
```



另见
---

- [randombit/botan: Cryptography Toolkit (github.com)](https://github.com/randombit/botan)