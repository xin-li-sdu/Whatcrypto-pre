 Microsoft SEAL
====

Microsoft SEAL是一个同态加密库，它允许在加密的整数或实数上执行加法和乘法。

## 入门

### 算法

SEAL提供了两种机制的同态加密，`BFV`和`CKKS`。SEAL采用的同态加密算法基于RLWE，其中有几个重要的参数：

```cpp
polynomial modulus
    
coefficient modulus
    
plaintext modulus (只在BFV机制中)
```

其中`polynomial modulus`和`max coefficient modulus bit-length`有一个对应关系，示例代码中给出了对应关系，比如1024 对应27，8192对应218。SEAL提供了默认设置的函数，其中`polynomial modulus`是必须设置的。



### 构建过程

<!--rehype:wrap-class=col-span-2-->

①　选择CKKS参数` parms`

②　生成CKKS框架` context`

③　构建CKKS模块 `keygenerator`，`encoder`，`encryptor`，`evaluator`和`decryptor`

④　使用`encoder `将 数据`n `编码为 明文`m`

⑤　使用`encryptor `将 明文`m `加密为 密文`c`

⑥　使用`evaluator `对 密文`c `运算为 密文`c’`

⑦　使用`decryptor `将 密文`c’ `解密为 明文`m’`

⑧　使用`encoder` 将 明文`m’` 解码为 数据`n’`

