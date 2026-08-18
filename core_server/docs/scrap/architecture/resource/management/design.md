# resource management系统

该系统作为基础设施，对resource进行管理.

---
## 职能
对resource的信息、数据进行**增删查改**，对这些操作进行审核。
- [获取数据](#获取数据)
- [检索](#检索)
- [创建](#创建)
- [删除](#删除)
- [修改](#修改)
- [examine and verify](./examine_and_verify/design.md)
- [版本控制](./version_control/design.md)

对同一对象的操作，删除优先级最高.

---

## 获取数据
### 输入
| 参数 | 描述 |
| :---: | :--- |
| UUID | resource的UUID |
| 语种 | 自然语言中的一种，用于确定文本的语言 |
### 输出
返回`UUID`对应的resource的指定`语种`的数据.

## 检索
### 输入
| 参数 | 描述 |
| :---: | :--- |
| 检索参照 | 用于检索的文本 |
| 检索范围 | 限定检索的键 |
| 过滤器 | 用于过滤掉不符合条件的resource |
| 排序依据 | 用于对检索结果进行排序 |
### 输出
返回按照`排序依据`排序过的，所有`检索范围`中存在`检索参照`，且具有`过滤器`指定内容的resource的UUID的集合.

## 创建
### 输入
| 参数 | 描述 |
| :---: | :--- |
| 类型 | 创建的resource的类型 |
| 数据 | 创建的resource的数据内容，指定每个键的值 |
### 输出
在`类型`对应的resource类型的表创建一行每个键的值与`数据`中的对应键相同的值.

## 删除
删除**已存在的resource**.
接收一个UUID，删除对应的resource除`UUID`和`当前的revision_node的UID`.

## 修改
修改**已存在的resource**的数据片段.
接收以下内容：
- UUID.
- 修改内容.

## examine and verify
设计文档：[examine_and_verify](./examine_and_verify/design.md)
对resource的修改请求提案的审核，可以传入一连串请求，作为单个提案.
每个提案至多有一个resource及其直接关联的resource的对象.

## 版本控制
设计文档：[version_control](./version_control/design.md)
对resource的修改进行记录，最小记录单位为操作。