# 关于resource

---
## 目录
- [resource的类型](#resource的类型)
- [可选的过滤条件](#可选的过滤条件)
- [可选的排序依据](#可选的排序依据)
- [角色名称对应表](#角色名称对应表)
---

## resource的类型
所有类型都具有下表字段。

| 字段 | 接口数据类型 | 说明 |
| :---: | :---: | :--- |
| `UUID` | `string` | resource的唯一标识符。 |
| `introduction`/`intro` | `string` | resource的简介。 |
| `description`/`desc` | `string` | resource的具体说明。 |
| `history` | `array` | resource的变更历史，元素格式见后。只在响应体中出现。 |

`history`元素的格式如下：
```json
{
    "UUID": {
        "self": "自身版本的UUID",
        "parent": "父版本的UUID"
    },
    "intro": "提案简介",
    "date": "提案创建日期（YYYYMMDD）",
    "sponsor_ID": {
        "org": "发起提案的账户所在的组织的ID",
        "profile": "发起提案的账户的ID"
    },
    "checker_ID": {
        "org": "审核的账户所在的组织的ID",
        "profile": "审核的账户的ID"
    },
    "content": {
        "操作类型（insert/replace）": ["字段", "起始位置（第一个字符为1）", "末尾位置（第一个字符为1）", "数据（见具体接口引用定义）"]
    }
}
```

### 音乐创作者
**类型名**：`artist`.

| 字段 | 接口数据类型 | 说明 |
| :---: | :---: | :--- |
| `name` | `string` | 音乐创作者的正式名称。 |
| `birthplace` | `object` | 音乐创作者的籍贯，只记录到国家内顶级地理行政规划区域。格式见后文。 |
| `active_time` | `array` | 音乐创作者活跃时间，只记录发布第一个作品和退出创作的时间点。长度为`2`，元素为UNIX时间戳，第一个为开始，第二个为结束。 |
| `composition` | `array` | 音乐创作者参与的作品。元素为歌曲或音乐产品的UUID。 |

`birthplace`的格式如下：
```json
{
    "country": "所在国家的正式英文名",
    "FLAD": "所在的第一层级行政区"
}
```
除了`country`外的字段都可能不存在。  
注：`FLAD`是`first-level administrative divisions`的缩写。

### 活动
**类型名**：`event`.

| 字段 | 接口数据类型 | 说明 |
| :---: | :---: | :--- |
| `title` | `string` | 活动名称。 |
| `place` | `object` | 活动地点。格式见后文。 |
| `time` | `array` | 活动时间。长度为`2`，元素为UNIX时间戳，第一个为开始，第二个为结束，第二个不一定存在。|
| `produce_release` | `array` | 活动所生产的音乐产品。元素为resource的UUID。 |
| `tag` | `array` | 响应体元素为标签的名称列表，请求体元素为标签的UUID。 |

`place`的格式如下：
```json
{
    "country": "所在国家的正式英文名",
    "FLAD": "所在的第一层级行政区",
    "SLAD": "所在的第二层级行政区",
    "TLAD": "所在的第三层级行政区"
}
```
除了`country`外的字段都可能不存在。  
注：
- `FLAD`是`first-level administrative divisions`的缩写。
- `SLAD`是`second-level administrative divisions`的缩写。
- `TLAD`是`third-level administrative divisions`的缩写。

### 歌曲
**类型名**：`song`.

| 字段 | 接口数据类型 | 说明 |
| :---: | :---: | :--- |
| `title` | `string` | 歌曲名称。 |
| `front_cover` | `binary` | 歌曲封面，同时也作为自身的`combined_trademark`。 |
| `back_cover` | `binary` | 歌曲封底。 |
| `lyrics` | `array` | 正序排序歌曲歌词。 |
| `tag` | `array` | 响应体元素为标签的名称列表，请求体元素为标签的UUID。 |
| `production_team` | `object` | 歌曲的制作团队。键名为角色名称（具体见[角色名称对应表](#角色名称对应表)），键值为音乐创作者的UUID组成的数组。 |

### 音乐产品
本质上是对`song`的扩展.  
**类型名**：`release`.

| 字段 | 接口数据类型 | 说明 |
| :---: | :---: | :--- |
| `title` | `string` | 音乐产品名称。 |
| `front_cover` | `binary` | 音乐产品封面，同时也作为自身的`combined_trademark`。 |
| `back_cover` | `binary` | 音乐产品封底。 |
| `illustration` | `binary` | 音乐产品内页插绘。 |
| `type` | `string` | 音乐产品的类型，具体见后文。 |
| `release_date` | `string` | 音乐产品的发行日期，格式为`YYYYMMDD`。 |
| `song_list` | `array` | 音乐产品的曲目，元素为resource的UUID。 |
| `tag` | `array` | 响应体元素为标签的名称列表，请求体元素为标签的UUID。 |
| `production_team` | `object` | 音乐产品的制作团队。键名为角色名称（具体见[角色名称对应表](#角色名称对应表)），键值为音乐创作者的UUID组成的数组。 |

**音乐产品的类型**有以下：
- `album`.
完整专辑，一般不少于8首.
- `EP`.
这里使用美国标准，即小于8首的专辑.
- `single`.
单曲.
- `compilation`.
合辑，包含一些歌曲，不一定是一个系列的.
- `greatest_hits`.
精选辑，一般内容都来自一个特定的文化作品创作者、社团或公司.
- `demo`.
样本录音.
- `other`.
其他类型.

### 社团
**类型名**：`team`.

| 字段 | 接口数据类型 | 说明 |
| :---: | :---: | :--- |
| `name` | `string` | 社团的正式名称。 |
| `birthplace` | `object` | 社团的创建地，只记录到国家内顶级地理行政规划区域。格式见后文。 |
| `active_time` | `array` | 社团活跃时间，只记录创建和停止活动的时间点。长度为`2`，元素为UNIX时间戳，第一个为开始，第二个为结束。 |
| `member` | `array` | 社团成员。元素为音乐创作者的UUID。 |
| `composition` | `array` | 社团作品。元素为歌曲或音乐产品的UUID。 |

`birthplace`的格式如下：
```json
{
    "country": "所在国家的正式英文名",
    "FLAD": "所在的第一层级行政区"
}
```
除了`country`外的字段都可能不存在。  
注：`FLAD`是`first-level administrative divisions`的缩写。

---

## 可选的过滤条件

| 字段 | 参数数据类型 | 适用的resource类型 | 说明 |
| :---: | :---: | :---: | :--- |
| `tag` | `array` | 通用 | 要求resource包含指定标签。元素为标签的UUID。 |
| `intro` | `string` | 通用 | 要求resource简介包含指定内容。为空表示使用检索关键词。 |
| `name` | `string` | 通用 | 要求resource名称（即`name`或`title`字段）包含指定内容。为空表示使用检索关键词。 |
| `member` | `array` | `team` | 要求社团包含指定成员。元素为音乐创作者的UUID。 |
| `place` | `object` | `team`/`artist`/`event` | 要求地点字段（即`place`或`birthplace`）在指定地点。格式见后文。 |
| `time` | `array` | `team`/`artist`/`event` | 要求时间字段（即`time`或`active_time`）在指定范围。长度为`2`，元素为UNIX时间戳，第一项为开始，第二项为结束（该项可不存在）。 |
| `composition` | `array` | `team`/`artist`/`event` | 要求包含指定作品（即`composition`或`produce_release`字段）。元素为音乐产品或歌曲的UUID。 |
| `release_date` | `array` | `release` | 要求发布时间在指定范围。长度为`2`，元素格式为`YYYYMMDD`，第一项为开始，第二项为结束（该项可不存在）。 |

`place`的格式如下：
```json
{
    "country": "所在国家的正式英文名",
    "FLAD": "所在的第一层级行政区",
    "SLAD": "所在的第二层级行政区",
    "TLAD": "所在的第三层级行政区"
}
```
提供的每个字段均与资源对应字段进行精确匹配。资源类型不存在的字段将被忽略。  
注：
- `FLAD`是`first-level administrative divisions`的缩写。
- `SLAD`是`second-level administrative divisions`的缩写。
- `TLAD`是`third-level administrative divisions`的缩写。

---

## 可选的排序依据

| 排序顺序 | 说明 |
| :---: | :--- |
| `ASC` | 正序。 |
| `DESC` | 倒序。 |

| 排序依据 | 说明 |
| :---: | :--- |
| `name_a-z` | resource的名字（即`name`或`title`字段）按A-Z的顺序排序。 |
| `tag_num` | 按tag数量排序。 |
| `change_time` | 按最近更改的时间排序。 |
| `personal_recommend` | 按个人推荐排序。（暂未实现） |
| `public_recommend` | 按公众推荐排序。（暂未实现） |

---

## 角色名称对应表

| 角色名称 | 职能/名字 |
| :---: | :--- |
| `composer` | 作曲，歌曲旋律创作。 |
| `lyricist` | 作词，歌曲歌词创作。 |
| `arranger` | 编曲，歌曲制造。 |
| `music_producer` | 歌曲制作人，制作过程管理。 |
| `mixing_engineer` | 混音工程师。 |
| `audio_editor` | 编辑师。 |
| `music_director` | 音乐总监。 |