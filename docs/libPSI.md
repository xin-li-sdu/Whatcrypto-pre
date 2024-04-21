 libPSI
====

A library for performing private set intersection

## Install

### 在Linux或Windows上安装

<!--rehype:wrap-class=col-span-2-->

```bash
git clone https://github.com/osu-crypto/libPSI.git
cd libPSI
python build.py
```

### 测试

```bash
/out/build/<platform>/frontend/frontend.exe -u
//对所有协议进行本地化测试
```




## 库内容

### 支持的psi协议

| 符号 | 说明                                                 |
| --------- | ---------------------------------------------------- |
| `-dcwr`   | DCW13+PSZ14 - 随机混淆布隆过滤器（半诚实*）          |
| `-rr16`   | RR16 - 随机混淆布隆过滤器（恶意安全）                |
| `-rr17a`  | RR17 - 哈希到桶并比较风格（恶意安全，最快）          |
| `-rr17a-sm` | RR17sm - 哈希到桶并比较风格（标准模型恶意安全）      |
| `-rr17b`  | RR17b - 哈希到桶并提交比较风格（恶意安全）           |
| `-grr`    | GRR19 - 哈希到桶并提交比较风格（差分隐私和恶意安全） |
| `-dkt`    | DKT12 - 公钥风格（恶意安全）                         |
| `-ecdh`   | ECHD - Diffie-Hellman 密钥交换（半诚实安全）         |
| `-kkrt`   | KKRT16 - 哈希到桶并比较风格（半诚实安全，最快）      |
| `-drrt`   | DRRN17 - 两服务器 PIR 风格（半诚实安全）             |
<!--rehype:className=shortcuts--> 


### 基于文件的命令行参数

| 参数            | 说明                                            |
|----------------|-------------------------------------------------|
| -in            | 参与方集合的路径。应该是包含 16 字节元素并具有 .bin 扩展名的二进制文件。否则，路径应具有 .csv 扩展名，并且每行一个元素，32 个字符的十六进制行最好。|
| -r             | 值应为 { 0, 1 }，其中 0 表示PSI发送方。                      |
| -out           | 输出文件路径。将以与输入相同的格式写入。（默认值 = in || ".out"）|
| -ip            | PSI接收方的IP地址和端口。（默认值 = localhost:1212）          |
| -server        | 值应为 {0, 1}，指示此参与方是否应为IP服务器。（默认值 = r）    |
| -bin           | 始终将输入文件解释为二进制的可选标志。                         |
| -csv           | 始终将输入文件解释为CSV的可选标志。                           |
| -receiverSize  | 指定接收方集合大小的可选参数。                               |
| -senderSize    | 指定发送方集合大小的可选参数。                               |
<!--rehype:className=shortcuts--> 

### 基准命令行参数
| 参数          | 说明                                                                                           |
|--------------|------------------------------------------------------------------------------------------------|
| -r           | 两个终端模式。值应为 { 0, 1 }，其中 0 表示PSI发送方和网络服务器。                                   |
| -ip          | 服务器的IP地址和端口。（默认值 = localhost:1212）                                               |
| -n           | 每个参与方拥有的项目数，以空格分隔。（默认值 = 256）                                             |
| -nn          | 每个参与方拥有的项目数为2^n，以空格分隔。                                                         |
| -nn2         | PIR PSI中服务器拥有的项目数为2^n，以空格分隔。                                                     |
| -t           | 每个参与方拥有的线程数，以空格分隔。（默认值 = 1）                                               |
| -trials      | 执行的试验次数。（默认值 = 1）                                                                    |
| -v           | 打印额外信息。（默认值 = 0）                                                                      |
| -ping        | 执行ping和带宽测试（默认值 = 0）                                                                 |
| -b           | 协议所依赖的位大小。                                                                               |
| -s           | 使哈希到桶类型协议使用 n/s 个桶（默认值 = 1）                                                     |
| -bigBlock    | 每个PIR为PSI-PSI服务器数据库贡献的条目数量。大=较小的PIR查询但较大的PSI（默认值 = 16）            |
<!--rehype:className=shortcuts--> 

## 运行实例

### 命令行输入

<!--rehype:wrap-class=col-span-2-->



这是私有集合交集计算库 libPSI 的前端程序的帮助信息。该程序支持多种协议和文件参数设置，可以用于执行私有集合交集计算。你可以根据需要选择不同的协议和参数设置来运行程序。

```bash
./frontend.exe -kkrt -in ReceiverDat.csv -r 1 -out Intersection.csv	
./frontend.exe -kkrt -in SenderDat.csv -r 0
```

在这个命令中：

- `-kkrt` 表示选择 KKRT 协议进行私有集合交集计算。
- `-in input.csv` 指定输入文件的路径和格式。你需要准备一个 CSV 文件，每行包含一个元素。
- `-r 0` 表示当前执行的是 PSI 发送方。
- `-out output.csv` 指定输出文件的路径和格式，计算结果将会保存在这个文件中。

### 读取csv文件的逻辑

readSet可以支持两种文件输入格式，二进制文件和csv文件。这里只讨论csv文件，因为csv文件更已读，而且对大数据文件支持更好。 

要求csv的每一个条目都是十六进制数hexdecimal，如果这个hex长度超128bit，则调用Random Oracle将它压缩为128bit，如果长度不足128bit则直接认定为128bit。最终这个128bit hex会转换成block类型的变量

注意Intersection.csv存储的不是交集元素本身，而是交集元素在ReceiverDat.csv的位置索引。

重复的索引对报告异常。重复的索引对表明在处理数据时出现了重复的值。PSI是没有顺序的，必须保证无重。

另见
---

- [lib PSI仓库](https://github.com/osu-crypto/libPSI)