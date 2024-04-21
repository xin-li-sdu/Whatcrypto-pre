 NTL
====

NTL是一个高性能、可移植的`C++`库，为任意长度的整数提供数据结构和算法

## 简介

### 涉及领域领域

<!--rehype:wrap-class=col-span-2-->

- 任意长度整数运算和任意精度浮点运算；

- 整数和有限域上的多项式算术，包括基本算术、多项式分解、不可约判定、最小多项式计算、迹线、范数等计算；

- 格基归约，包括 非常健壮和快速的Schnorr-Euchner,实现、块 Korkin-Zolotarev 归约，以及块 Korkin-Zolotarev 的新 Schnorr-Horner 剪枝启发式；

- 整数、有限域和任意精度浮点数的基本线性代数。


### 平台

NTL同时具备Unix、Mac OS或Windows任意平台上使用，NTL可与GMP一起构建以增强性能，NTL可与gf2x库一起构建，以便在GF(2)上快速计算大型多项式。NTL具有优秀的二次开发接口可快速实现新的数论算法。近年来，众多隐私计算、安全多方计算的开源库采用了该数论库，

## NTL及其依赖库安装

### GMP库安装

<!--rehype:wrap-class=col-span-2-->

`GMP`是一个用于长整数运算的库。对于基本运算(例如整数乘法)，它比NTL的长整数快2-3倍。因此NTL库默认使用`GMP`库。所以安装NTL库之前首先安装`GMP`库。

```bash
wget https://gmplib.org/download/gmp/gmp-6.1.2.tar.bz2
tar -jxvf gmp-6.1.2.tar.bz2 
mv gmp-6.1.2 gmp
rm gmp-6.1.2.tar.bz2
cd gmp
./configure --prefix=$PWD 
make
make check
make install
cd ..
```

### GF2X库安装

`gf2x`是一个用于在GF(2)上进行多项式快速乘法的库.与仅实现 Karatsuba 乘法版本的 NTL 不同，`gf2x`还实现了其他算法，这些算法对于非常大的阶多项式来说更快

```bash
wget https://gitlab.inria.fr/gf2x/gf2x/uploads/46a3851a4aa6888e6a6a7ce3de33f0f4/gf2x-1.2.tar.gz
tar -zxvf gf2x-1.2.tar.gz 
mv gf2x-1.2 gf2x
rm gf2x-1.2.tar.gz
cd gf2x
./configure --prefix=$PWD ABI=64 CFLAGS="-m64 -O2"
make
make check
make install
cd ..
```

### NTL库安装

<!--rehype:wrap-class=col-span-3-->

```bash
#下载NTL库
wget http://www.shoup.net/ntl/ntl-10.4.0.tar.gz
#解压
tar -zxvf ntl-10.4.0.tar.gz 
mv ntl-10.4.0 ntl
rm ntl-10.4.0.tar.gz
cp ./ntl_patch/* ./ntl/include/NTL/
linux=$(pwd)
cd ntl/src
#安装
./configure PREFIX=$linux/gmp GMP_PREFIX=$linux/gmp NTL_THREAD_BOOST=on NTL_FFT_LAZYMUL=on NTL_FFT_BIGTAB=on NTL_GF2X_LIB=on  GF2X_PREFIX=$linux/gf2x
make -j20
mv ./ntl.a ./libntl.a
```

##  NTL的使用



### NTL库的基本变量

| 术语     | 说明                                 |
| -------- | ------------------------------------ |
| `ZZ`     | 大整数（超出基本数据类型范围的整数） |
| `ZZ_p`   | 模 P 的大整数                        |
| `zz_p`   | 模单精度 P 的整数                    |
| `GF2`    | 模 2 的整数                          |
| `ZZX`    | 具有 ZZ 系数的单变量多项式           |
| `ZZ_pX`  | 具有 ZZ_p 系数的单变量多项式         |
| `zz_pX`  | 具有 zz_p 系数的单变量多项式         |
| `GF2X`   | 具有 GF2 系数的多项式                |
| `ZZ_pE`  | ZZ_p 上的环/域扩张                   |
| `zz_pE`  | zz_p 上的环/域扩张                   |
| `GF2E`   | GF2 上的环/域扩张                    |
| `ZZ_pEX` | 具有 ZZ_pE 系数的单变量多项式        |
| `zz_pEX` | 具有 zz_pE 系数的单变量多项式        |
| `GF2EX`  | 具有 GF2E 系数的单变量多项式         |

### 常用函数

```cpp
SetSeed(const ZZ& s)：//设置PRF种子

RandomBnd(ZZ& x, const ZZ& n)：//随机生成x

RandomBits(ZZ& x, long l)：//随机生成l比特的整数
    
ZZ p(17)//初始化整数为17，这里参数类型是long

p = to_ZZ("123")//读入字符串，可输入大整数

GenPrime(p, 8)//随机生成8比特素数
    
ZZ_p::init(p)//初始化环Zp 

ZZ_p a(2)//初始化为2 mod p，这里参数类型是long

```

### 常用数论函数

<!--rehype:wrap-class=col-span-2-->

```cpp
// 计算两个整数的最大公约数
long GCD(const long& a, const long& b);

// 扩展欧几里得算法，计算a和b的最大公约数，并返回x和y，使得ax + by = gcd(a, b)
void XGCD(long& x, long& y, const long& a, const long& b);

// 模加法
long AddMod(const long& a, const long& b, const long& n);

// 模减法
long SubMod(const long& a, const long& b, const long& n);

// 模取反
long NegateMod(const long& a, const long& n);

// 模乘法
long MulMod(const long& a, const long& b, const long& n);

// 模平方
long SqrMod(const long& a, const long& n);

// 模逆
long InvMod(const long& a, const long& n);

// 模指数运算
long PowerMod(const long& a, const long& e, const long& n);

// 计算整数的二进制表示的长度
long NumBits(const long& a);

// 提取整数的指定比特位
long bit(const long& a, long k);

// 将octet字符串转换为整数
void ZZFromBytes(long& x, const unsigned char *p, long n);

// 将整数转换为octet字符串
void BytesFromZZ(unsigned char *p, const long& x, long n);

// 生成指定范围内的伪随机数
long RandomBnd(const long& n);

// 生成指定位数的伪随机数
long RandomBits(long l);

// 生成指定长度的伪随机数
long RandomLen(long l);

// 生成素数
void GenPrime(long& x, long n, long m = 20);

// 测试整数是否为素数
long ProbPrime(const long& a, long n = 10);

// 非模指数运算
long power(const long& a, const long& e);

// 计算整数的平方根的整数部分
long SqrRoot(const long& a);

// 计算雅可比符号
long Jacobi(const long& a, const long& n);

// 计算模平方根
long SqrRootMod(const long& a, const long& p);

```



## 使用示例

### 求GCD运算

<!--rehype:wrap-class=col-span-3-->

```cpp
#include <NTL/ZZ.h>
void main()
{
　　　ZZ a, b, c;
　　　a = 12;
　　　b = 20;
　　　GCD(c, a, b);
　　　cout << "a=" << a << ", b=" << b << ", c=" << c << "\n";
}
```

### RSA加密解密

<!--rehype:wrap-class=col-span-3-->

```cpp
#include <NTL/ZZ.h>
　　#include <fstream.h>
　　#include <time.h>
　　void main()
　　{
　　　　ZZ a, b, c, d;
　　　　clock_t tBegin, tEnd;
　　　　int i;
　　　　ifstream fin("input.txt");
　　　　fin >> a;
　　　　fin >> b;
　　　　fin >> c;
　　　　tBegin = clock();
　　　　for (i = 0; i < 100; i ++)
　　　　　　d = PowerMod(a, b, c);
　　　　tEnd = clock();
　　　　cout << d << "\n\n";
　　　　cout << "\n\n进行100次1024比特的模指数运算所消耗的时间为:" << tEnd - tBegin << "ms\n";
　　　　cin >> i;    //用于暂停，观测数据
　　}
```



另见
---

- [A Tour of NTL: Examples: Big Integers](https://libntl.org/doc/tour-ex1.html)