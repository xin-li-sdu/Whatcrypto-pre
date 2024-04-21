 SecretFlow
====

[SecretFlow](https://github.com/secretflow/secretflow) 是一个隐私保护数据分析和机器学习的统一框架

## 安装

### 安装说明

Secretflow目前提供Lite和Full两种版本，Lite版本不包含深度学习相关依赖所以体积更小，Full版本则包含了所有依赖。您可以根据自己的需求自行选择合适的版本。

最简单的尝试SecretFlow的方式是使用带有SecretFlow的 **官方 docker image** ，你也可以 **通过Python Package Index安装SecretFlow** ，对于Coder，可以从源码安装**SecretFlow** 。对于Windows，可以**通过WSL2安装SecretFlow** 。对于有可用GPU设备的用户，可以 **尝试使用GPU支持**。以下是环境要求：

Python：3.8

pip: >= 19.3

OS: CentOS 7, Ubuntu 18.04

CPU/Memory: 推荐最低配置是 8C16G.

### 方式2: 通过docker

你也可以通过SecretFlow的docker image来尝试SecretFlow。

- 完全版本

```
docker run -it secretflow/secretflow-anolis8:latest
```

- Lite版本

```
docker run -it secretflow/secretflow-lite-anolis8:latest
```

更多版本请查看 [secretflow tags](https://hub.docker.com/r/secretflow/secretflow-anolis8/tags) 。

### 方式 1: 从pypi

请安装 [当前release](https://pypi.org/project/secretflow/) 。

注意python版本需要3.8，用conda构建一个虚拟环境。

```bash
conda create -n sf python=3.8
conda activate sf
```

之后，请使用pip安装SecretFlow。

- 完全版本

```bash
pip install -U secretflow
```

- Lite版本

```bash
pip install -U secretflow-lite
```

### 方式3: 通过源码

下载源码并建立Python虚拟环境

```bash
git clone https://github.com/secretflow/secretflow.git
cd secretflow

conda create -n secretflow python=3.8
conda activate secretflow
```

安装SecretFlow

- 完全版本

```bash
python setup.py bdist_wheel

pip install dist/*.whl
```

- Lite版本

```bash
python setup.py bdist_wheel --lite

pip install dist/*.whl
```

### 方式4: 通过WSL

隐语不直接支持Windows, 需要使用WSL(Windows Subsystem for Linux)，请在WSL的GNU/Linux中安装anaconda。

- 创建conda环境

```bash
conda create -n sf python=3.8
```

- 激活conda环境

```
conda activate sf
```

- 之后，请使用pip安装SecretFlow。

  - 完全版本

  ```bash
  pip install -U secretflow
  ```

  - Lite版本

  ```bash
  pip install -U secretflow-lite
  ```

## 隐私求交

### PSI版本说明

SecretFlow SPU 实现了下面的PSI(Private Set Intersection)协议:

- Semi-honest ECDH-based two-party PSI protocol [[HFH99\]](https://secretflow.readthedocs.io/zh-cn/stable/user_guide/psi.html#hfh99)
- Semi-honest ECDH-based three-party PSI protocol
- Semi-honest OT-based two-party PSI protocol [[KKRT16\]](https://secretflow.readthedocs.io/zh-cn/stable/user_guide/psi.html#kkrt16)
- Semi-honest PCG/VOLE-based two-party PSI protocol (with improved communication efficiency) [[BC22\]](https://secretflow.readthedocs.io/zh-cn/stable/user_guide/psi.html#bc22)
- Semi-honest EC-OPRF based two-party Unbalanced PSI protocol
- Differentially Private (DP) PSI Protocol [[DP-PSI\]](https://secretflow.readthedocs.io/zh-cn/stable/user_guide/psi.html#dp-psi)

一般情况下, OT-based PSI比Diffie-Hellman-based PSI协议运行时间少，但通信量更大。一些场景中可能会更关注通信量。

### 初始化环境

在开始之前，我们需要初始化环境。以下三个节点 `alice`， `bob` 和 `carol` 是在一台机器上创建的，以模拟多个参与者。

```python
import secretflow as sf

# Check the version of your SecretFlow
print('The version of SecretFlow: {}'.format(sf.__version__))

# In case you have a running secretflow runtime already.
sf.shutdown()

sf.init(['alice', 'bob', 'carol'], address='local')
```

### 准备数据集

在实际场景中，示例数据由每个参与者提供，求交的字段需提前约定：

- 交集字段可以为单个字段，也可以为多个字段，需存在于对应方数据集中。
- 交集字段必须是唯一的。如果存在重复数据，需要在求交前进行去重处理。

我们对 iris 数据进行了 3 次随机采样，以模拟 `alice`、`bob` 和 `carol` 提供的数据，三个数据处于未对齐状态。

```python
import os

os.makedirs('.data', exist_ok=True)
da, db, dc = data.sample(frac=0.9), data.sample(frac=0.8), data.sample(frac=0.7)

da.to_csv('.data/alice.csv', index=False)
db.to_csv('.data/bob.csv', index=False)
dc.to_csv('.data/carol.csv', index=False)
```



### 两方隐私求交

我们在物理设备上虚拟化三个逻辑设备：

- alice, bob: PYU设备，负责参与者的本地明文计算。
- spu：SPU设备，由alice和bob组成，负责双方的密文计算。

```python

alice, bob = sf.PYU('alice'), sf.PYU('bob')

spu = sf.SPU(sf.utils.testing.cluster_def(['alice', 'bob']))

```

### 单键隐私求交

接下来，我们使用 `uid` 作为求交列来处理这两个数据，SPU提供 `psi_csv` 函数， `psi_csv` 将csv文件作为输入，并在求交后生成csv文件。默认协议为`KKRT`。

```python

input_path = {alice: '.data/alice.csv', bob: '.data/bob.csv'}

output_path = {alice: '.data/alice_psi.csv', bob: '.data/bob_psi.csv'}

spu.psi_csv('uid', input_path, output_path, 'alice')
```

### 校验结果的正确性

为了校验结果的正确性，我们使用 [pandas.DataFrame.join](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.join.html) 对da和db进行内部求交。可以看到，这两个数据已经根据``uid``对齐，并根据它们的字典顺序排序。

```python
df = da.join(db.set_index('uid'), on='uid', how='inner', rsuffix='_bob', sort=True)
expected = df[da.columns].astype({'uid': 'int64'}).reset_index(drop=True)
da_psi = pd.read_csv('.data/alice_psi.csv')
db_psi = pd.read_csv('.data/bob_psi.csv')
pd.testing.assert_frame_equal(da_psi, expected)
pd.testing.assert_frame_equal(db_psi, expected)

```

### 多键隐私求交

我们还可以使用多个字段来求交，下面演示了如何使用 `uid` 和 `month` 来求交两个数据。在实现方面，多个字段被连接成一个字符串，因此请确保没有重复的多列复合键。

```python

spu.psi_csv(['uid', 'month'], input_path, output_path, 'alice')
```

类似地，我们使用 `panda.dataframe.join` 来验证结果的正确性，可以看到这两个数据已经根据 `uid` 和 `month` 对齐，并根据它们的字典序排序。

```python

df = da.join(
    db.set_index(['uid', 'month']),
    on=['uid', 'month'],
    how='inner',
    rsuffix='_bob',
    sort=True,
)

expected = df[da.columns].astype({'uid': 'int64'}).reset_index(drop=True)

da_psi = pd.read_csv('.data/alice_psi.csv')
db_psi = pd.read_csv('.data/bob_psi.csv')

pd.testing.assert_frame_equal(da_psi, expected)
pd.testing.assert_frame_equal(db_psi, expected)
```

###  三方隐私求交

接下来，我们添加第三方 `carol` ，并为其创建一个PYU设备，以及一个由三方构建的SPU设备。

```
carol = sf.PYU('carol')
spu_3pc = sf.SPU(sf.utils.testing.cluster_def(['alice', 'bob', 'carol']))
```

然后使用 `uid` 和 `month` 作为求交键来执行三方求交。目前三方隐私求交暂时只支持ECDH协议。

```
input_path = {alice: '.data/alice.csv', bob: '.data/bob.csv', carol: '.data/carol.csv'}
output_path = {
    alice: '.data/alice_psi.csv',
    bob: '.data/bob_psi.csv',
    carol: '.data/carol_psi.csv',
}
spu_3pc.psi_csv(
    ['uid', 'month'], input_path, output_path, 'alice', protocol='ECDH_PSI_3PC'
)
```

最后我们同样使用 `panda.dataframe.join` 来验证结果的正确性。

```python
keys = ['uid', 'month']
df = da.join(db.set_index(keys), on=keys, how='inner', rsuffix='_bob', sort=True).join(
    dc.set_index(keys), on=keys, how='inner', rsuffix='_carol', sort=True
)
expected = df[da.columns].astype({'uid': 'int64'}).reset_index(drop=True)
da_psi = pd.read_csv('.data/alice_psi.csv')
db_psi = pd.read_csv('.data/bob_psi.csv')
dc_psi = pd.read_csv('.data/carol_psi.csv')
pd.testing.assert_frame_equal(da_psi, expected)
pd.testing.assert_frame_equal(db_psi, expected)
pd.testing.assert_frame_equal(dc_psi, expected)
```

## 同态加密

很不幸，根本没做BFV和CKKS，只做了Paillier，没啥好看的





