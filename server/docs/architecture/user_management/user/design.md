# User系统

外部数据关联user使用UID.
user的基本数据有：
- 账户UID.
自增，不由外部管理.
- 所在group的UID.
- ID.
- 密码.
- 主权限.
默认为`comment,gain_permission,resource_rectify,create_personal_list`.
- 管理权限.
默认为空.
- 销户.
时间戳，默认为0.
- Q.
- A.

user表中，第一行作为admin账户的预留位.
admin账户的数据为：
- `0`
- `admin`
- 无
- 无
- 无
- `admin`
- `0`
- 无
- 无

admin账户的登陆凭证为空，为了防止被意外的登录，它只能被本地所调用.

---
## 系统组成
- [账户操作](#账户操作)
    - [注册](#注册)
    - [登录](#登录)
    - [销户与恢复](#销户与恢复)
    - [冻结与解冻](#冻结与解冻)
    - [修改密码](#修改密码)
- [User权限系统](./manager_permission/design.md)
- [通知系统](./notification/design.md)
- [社交系统](./socialize/design.md)
- [信息系统](./information/design.md)
---

## 账户操作
### 注册
提供所在的group的UID、账户ID、密码和一组Q&A注册一个新账户.
账户ID在同group下必须唯一.

### 登录
提供group的UID、账户ID、密码登录一个账户.

### 销户与恢复
提供所在的group的UID、账户ID、密码和Answer注销或恢复一个账户.

### 冻结与解冻
提供所在的group的UID、账户ID、密码和Answer冻结或解冻一个账户.

### 修改密码
提供所在的group的UID、账户ID、新密码和Answer修改账户密码.

### 补充
销户时间超过90天的账户将UID以外的键值置空.


## 用户权限系统
设计文档：[user_manager_permission](./manager_permission/design.md)
用户的社交、操作的权限管理.用于管理用户能力范围.


## 通知系统
设计文档：[user_notification](./notification/design.md)
包含各类通知，主要由以下组成：
- 系统通知，由系统生成.
- 社交通知，评论被评论.
- 权限通知，权限被授予、卸下、封禁.


## 社交系统
设计文档：[socialize](./socialize/design.md)
目前只有评论和讨论.


## 信息系统
设计文档：[information](./information/design.md)
个人贡献次数、个人收藏、user行为图象等信息.