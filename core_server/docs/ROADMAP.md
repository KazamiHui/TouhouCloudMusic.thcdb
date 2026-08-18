# THCDB 开发路线图

本文档说明项目发展路线的阶段及其对应的功能。

---
**功能树**:
- [已开发功能](#已开发功能)
- [里程碑](#里程碑)
  - [核心功能](#核心功能)
  - [第一阶段：中心功能](#中心功能)
  - [第二阶段：边沿功能](#边沿功能)
  - [第三阶段：外延功能](#外延功能)
---

## 已开发功能

TODO.

---
## 里程碑
---
### 核心功能
#### 用户权限系统
设计文档：[manger_permission](./architecture/user_management/user/manager_permission/design.md)
职能简述：
用户一切活动的权限的管理。

#### resource管理系统
设计文档：[resource_management](./architecture/resource_management/design.md)
职能简述：
对resource的增删查改和版本控制，依赖用户权限系统。

---
### 中心功能

#### 用户列表系统
设计文档：[list](./architecture/user_management/user/information/list/design.md)
职能简述：
用户管理自身的歌单、收藏夹等数据。

#### 标签管理系统
设计文档：[tag](./architecture/tag/design.md)
职能简述：
对tag名称的增删改，依赖用户权限系统。

---
### 边沿功能

#### 用户resource关注系统
设计文档：[attention](./architecture/user_management/user/information/attention/design.md)
职能简述：
用户管理自身对resource的关注.

#### 用户社交系统
设计文档：[socialize](./architecture/user_management/user/socialize/design.md)
职能简述：
对resource评论及对其举报的处理。

---
### 外延功能

#### 推荐系统
设计文档：[recommendation](./architecture/recommendation/design.md)
职能简述：
计算推荐系数，用于强化resource搜索能力.