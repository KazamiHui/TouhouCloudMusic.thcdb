# User系统

外部数据关联user使用账户UID.

user的基本数据有：
- 账户UID.
自增，不由外部管理.
- 所在group的UID.
- ID.
- 密码.
- 主权限.
默认与所在的group的`账户主权限`相同.
- 管理权限.
默认为空.
- 停用.
    - 数据类型：数组.
    第一项为时间戳，第二项为停用类型.
- Q&A.
不可为空.
    - 数据类型：数组.
    元素为键值对形式，Q为键名，A为键值.

user表中，第一行作为admin账户的预留位.  
admin账户的数据为：
- 账户UID.
`0`
- 所在group的UID
`0`
- 管理权限.
`admin`
- 停用.
`0`

未展示的即为空值.  
admin账户的登陆凭证为空，为了防止被意外的登录，它只能被本地所调用.

---
## 系统组成
- [账户](#账户)
- [User权限系统](./manager_permission/design.md)
- [通知系统](./notification/design.md)
- [社交系统](./socialize/design.md)
- [信息系统](./information/design.md)
---

## 账户
### 新建账户
提供所在的group的UID、账户ID、密码和至少一组Q&A注册一个新账户.  
账户ID在同group下必须唯一.

### 生成凭证
提供账户UID生成该账户的临时凭证.

### 停用账户
提供账户UID和停用类型，将账户`停用`字段填入调用时的unix时间戳和停用类型.  
停用类型有且只有`freeze`和`close`.

### 恢复账户
提供账户UID，将账户`停用`字段清空.

### 清空账户
提供账户UID，将账户的`账户UID`和`所在group的UID`外的字段清空.

### 获取信息
提供以下内容：
- 账户UID.
- 需要获取信息的键.
可以为多项，必须是user表存在的字段.

返回一串键值对.

### 修改信息
提供以下内容：
- 账户UID.
- 需要修改的键.
为`账户ID`、`密码`或`Q&A`.
- 修改后的内容.
需要与修改的键的数据类型对应.

修改账户的`需要修改的键`的值为`修改后的内容`.


## 用户权限系统
设计文档：[user_manager_permission](./manager_permission/design.md)
用户的社交、操作的权限管理.用于管理用户能力范围.


## 通知系统
设计文档：[user_notification](./notification/design.md)
包含各类通知，主要由以下组成：
- 系统通知，由系统生成.
- 社交通知，如账户的评论被评论.
- 权限通知，权限被授予、卸下、封禁.


## 社交系统
设计文档：[socialize](./socialize/design.md)
处理用户**公开的社会交流**.


## 信息系统
设计文档：[information](./information/design.md)
个人贡献次数、个人收藏、user行为图象等信息.