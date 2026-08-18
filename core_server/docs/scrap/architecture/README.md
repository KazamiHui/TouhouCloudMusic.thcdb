# THCDB 架构文档

本目录包含 THCDB 服务器的完整架构设计文档。

## 目录结构

```
architecture/
├── README.md                         # 本文件
├── recommendation/                   # 推荐系统
├── resource/                         # resource类型
│    ├── artist/                        # 艺人
│    ├── event/                         # 活动
│    ├── language/                      # 辅助resource展示，不为resource的一种
│    ├── release/                       # 发行版本
│    ├── song/                          # 歌曲
│    ├── team/                          # 团队
├── resource_management/              # resource管理
│    ├── examine_and_verify/            # resource核实系统
│    ├── search/                        # 搜索系统
│    └── version_control/               # 版本控制系统
├── tag/                              # 标签系统
│    ├── history/                       # 历史更改
│    └── management/                    # 标签管理
│         └── compliance_audit/         # tag审核系统
└── user_management/                  # 用户管理
     ├── organization/                  # 组织管理
     └── user/                          # 用户模块
          ├── information/                # 用户信息
          │    ├── attention/               # 关注
          │    └── list/                    # 列表
          ├── manager_permission/         # 权限管理
          ├── notification/               # 通知系统
          └── socialize/                  # 社交系统
               ├── comment/                 # 评论
               └── lawfulness_review/       # 复核系统
```

## 架构原则 

### 领域驱动设计

项目设计遵循 DDD 原则，根据需求切分几个系统及其需要的功能。

### 代码组织架构

#### 垂直切片架构

基础支撑功能代码应使用垂直切片架构，放在 `src/feature/` 目录下：

```
feature/{功能名}/
├── mod.rs      # 模块定义和路由
├── repo.rs     # 数据访问
└── model.rs    # 数据模型（如需要）
```

#### 整洁架构

API代码应使用整洁架构，在 `src/API/`目录下：
- `interface/`：接口参数定义和解析.
- `service/`：API的业务逻辑.
- `model/`：数据模型.


### Examine and Verify系统

所有核心实体的变更都通过Examine and Verify系统进行：

1. 用户提交请求
2. 审核员核实通过
3. 批准后应用到数据库
4. 历史记录保存在历史表中

## Log
日志级别有且仅有：Error, Warning, Info, Debug.  
日志默认使用json编码，以json格式储存，每份日志文件寿命为14天.  
日志格式为
> {"level":"日志等级","time":"yyyymmddhhmmss.SSS","invoker_type":"发起请求的对象的类型","invoker":"发起请求的对象","receiver_module":"动作所在模块","receiver":"动作","status":"动作执行情况","mistake_point":"错误位置"}

`\`作为转义符，换行转义为`\n`.

## 错误码
不打断运行的，返回值简单明了的阐述事实即可.  
打断运行的，返回值以`ERR_所在Feature：`开头，然后阐述问题.

## 快速导航

- **开始开发**: 查看[ROADMAP.md](../ROADMAP.md)了解项目情况
- **共享类型**: 请见[shared-types.md](../feature_implementation/shared-types.md)
- **核心实体**: 请见[resource](./resource/design.md)内容
- **用户系统**: 请见[user](./user_management/user/design.md)
- **数据质量**: 请见[Examine_and_Verify](./resource_management/examine_and_verify/design.md), [Tag](./tag/design.md)