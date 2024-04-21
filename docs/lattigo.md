 LattiGo
====

Go语言的格基多方同态加密库

## 入门

### LattiGo包含子项目

<!--rehype:wrap-class=col-span-2-->

| 模块                              | 功能                                                         |
| --------------------------------- | ------------------------------------------------------------ |
| `lattigo/ring`                    | RNS加速的多项式模块化算术运算，包括RNS基数扩展、RNS重新缩放、数论变换（NTT）、均匀、高斯和三元采样。 |
| `lattigo/bfv`                     | Brakerski的尺度不变同态加密方案的RNS加速Fan-Vercauteren版本。它提供了整数的模运算。 |
| `lattigo/ckks`                    | RNS加速版的近似数字同态加密（HEAAN，又名CKKS）方案。它提供了复数的近似算术。 |
| `lattigo/dbfv` 和 `lattigo/dckks` | BFV和CKKS方案的分布式（或阈值）版本，可使用共享的秘密密钥实现安全的多方计算解决方案。 |
| `lattigo/examples`                | 可执行的Go程序，演示了Lattigo库的用法。请注意，每个子包都包含测试文件，这些文件进一步演示了Lattigo基元的用法。 |
| `lattigo/utils`                   | 支持的结构和功能。                                           |

