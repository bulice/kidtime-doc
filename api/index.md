# 萌养时光第三版 接口文档

## 目录

*   [更新历史](#更新历史)
*   [todo](#todo)
*   [说明](#说明)
    *   [基础部分](#基础部分)
    *   [翻页](#翻页)
    *   [身份验证](#身份验证)
    *   [文件访问](#文件访问)
    *   [文件上传](#文件上传)
    *   [数据类型](#数据类型)
*   [用户模块](#用户模块-user)
    *   [登录](#登录-login-post)
    *   [修改密码](#修改密码-changepassword-post)
    *   [联系人列表](#联系人列表-contact-get)
    *   [用户信息](#用户信息-detail-get)
    *   [修改个人信息](#修改个人信息-update-post)
*   [消息模块](#消息模块-feed)
    *   [消息列表](#消息列表-index-get-paged)
    *   [创建消息](#创建消息-create-post)
    *   [删除消息](#删除消息-delete-get)
    *   [评论](#评论-comment-post)
    *   [删除评论](#删除评论-deletecomment-get)
    *   [点赞](#点赞-like-get)
    *   [取消赞](#取消赞-deletelike-get)
    *   [置顶](#置顶-stick-get)
    *   [举报信息](#举报信息-report-post)
*   [任务模块](#任务模块-task)

## 更新历史

* 2015-7-9: 初始化文档, 已完成模块: User, Feed

[返回目录](#目录)

## TODO

*   推送
*   聊天
*   频道
*   等等等等

[返回目录](#目录)

## 说明

### 基础部分

*   api访问路径: <http://v3.kindertime.cn/api/{模块名}/{方法}>
*   api测试器: <http://v3.kindertime.cn/test/index.html> **是否需要单独建一个测试环境?**
*   请求方法: `GET`和`POST`不限, 接口列表中的方法为建议方法
*   编码: 所有数据均采用UTF-8编码
*   入参格式: `application/x-www-form-urlencoded`或`application/json`(限`POST`), 或`multipart/form-data`
*   返回格式: `application/json`
*   失败返回(指服务器正常的情况下的返回, 如果服务器宕机或出了其它未知情况可能会返回非期望的值, 可以先检测一下Content-Type头信息):

    | key | type | desc |
    | --- | --- | --- |
    | c | int | 状态码, 500 - 599表示入参错误, 600 - 699表示服务器内部错误, 其它保留 |
    | d | mixed | 如果为object, 表示入参错误的各个字段的错误原因 |
    | m | string | 错误说明 |
    
*   成功返回

    | key | type | desc |
    | --- | --- | --- |
    | c | int | =200 |
    | d | mixed | 返回数据 |
    
*   字段表中加#的字段表示可空

*   为了防止XSS，`<`和`>`会被HTML转义

[返回目录](#目录)

### 翻页

*   接口可翻页标志: `PAGED`
*   如果接口可翻页, 可在入参中加上`limit`字段限制每页数据条数, 默认为`10`
*   向前翻页(即拉取新消息): 在入参中加上`prev`字段, 如无特殊说明, 值为当前接口返回的数据中第一条数据的`_id`字段
*   向后翻页(获取历史消息): 在入参中加上`next`字段, 如无特殊说明, 值为当前接口返回的数据中最后一条数据的`_id`字段
*   `prev`字段的优先级高于`next`

[返回目录](#目录)

### 身份验证

*   除了用户登录接口外均需要验证, 如果验证失败, 则返回`status`为`401`
*   验证方式: 在请求头部加入`AUTHORIZATION`字段, 值为用户登录时返回的`token`
*   `token`有效期为**1个月**(这个有效期必需有, 这是PHP中SESSION控制机制), 每次请求会自动续期
*   如果当前用户在另一个地方重新登录拿了新的`token`, 则当前`token`失效

[返回目录](#目录)

### 文件访问

*   文件访问地址: <http://v3.kindertime.cn/site/file/id/{FileId}[/图片处理代码]>
*   FileId为返回的文件id，图片处理代码由OSS提供，参考<http://docs.aliyun.com/?spm=5176.383663.9.3.HRj7uY#/pub/oss/oss-img-api/intruduction&rule>
    
### 文件上传

*   上传地址：<http://v3.kindertime.cn/site/upload>
*   请求方式：`POST`，格式为`multipart/form-data`，最大`16M`
*   请求字段为`file`，数组格式，多文件上传
*   成功返回：

    | key | type | desc |
    | --- | --- | --- |
    | c | 200 | 状态码 |
    | d | File[] | 文件数组 |
    
    File：
    
    | key | type | desc |
    | --- | --- | --- |
    | \_id | FileId | 文件ID |
    | mine | string | 文件MINE类型 |

### 数据类型

*   基础数据类型列表

    | type | code | desc |
    | --- | --- | --- |
    | 整数 | int | 不得大于2^31 - 1 |
    | 受限整数 | int(a, b) | 限制大于或等于a, 且小于或等于b的整数 |
    | 字符串 | string | |
    | 受限字符串 | string(a, b) | 限制长度大于等于a, 小于等于b的字符串 |
    | 数组 | dataType[] | dataType 表示数据类型 |
    | 受限数组 | dataType[a, b] | 表示限制元素个数大于等于a, 小于等于b的dataType类型的数组 |
    | 空 | null | |

*   预定义标量数据类型
    
    | type | name | desc |
    | --- | --- | --- |
    | MongoId | MongoId | 长度为24的字符串, 每一条数据均有的数据id |
    | MongoDate | 时间戳 | 符合ISO8901格式的时间字符串, +8时区 |
    | FileId | 文件ID | 实际为MongoId, 但是是文件的, 可以通过文件接口访问 |
    | Url | 链接 | 正则`/^((http(s)?\:)?\/\/)?([a-z0-9][a-z0-9_-]*)(\.[a-z0-9][a-z0-9_-]*)+(\:\d{1,6})?(\/[^\s<>]*)?$/i`, 最长2kb |
    | Phone | 手机号 | **字符串** 正则`/^1[34578]\d{9}$/` |
    | Tel | 电话号码 | 正则`/^(\(\+\d{2,3}\))?(\d{3,4}-)?[1-9]\d{6,7}(-\d{1,4})?$/` |

*   预定义的Object
    * Photo 照片
    
    | key | type | desc |
    | --- | --- | --- |
    | \_id | FileId | 文件id |
    | title | string(1, 200)\|null | 标题 |
    
    * Media 多媒体
    
    | key | type | desc |
    | --- | --- | --- |
    | type | int | =1: 照片, =2: 视频, =3: 音频, 4: 链接, =5(默认): 空 |
    | photo | Photo[0, 10] | 照片组, 最多10张, 如果type=1, 则此项有效 |
    | video | FileId | 视频文件id, type=2有效 |
    | video_title | string(1, 200)\|null | 视频标题 |
    | video_cover | FileId\|null | 视频封面id |
    | audio | FileId | 音频文件id, type=3有效 |
    | audio_title | string(1, 200)\|null | 音频标题 |
    | auto_cover | FileId\|null | 音频封面文件id |
    | link | Url | 链接地址, type=4有效 |
    | link_cover | FileId\|null | 链接封面 |
    | link_title | string(1, 200)\|null | 链接标题 |
    
    * User 用户
    
    | key | type | desc |
    | --- | --- | --- |
    | \_id | MongoId | 用户id |
    | name | string(2, 20) | 姓名 |
    | avatar | FileId\null | 头像 |
    | phone | Phone | 手机号 |
    | nickname | string(2, 20) | 昵称 |
    | birthday | MongoDate\|null | 生日 |
    | intro | string(1, 200)\|null | 简介 |
    | gender | int | 性别 |
    
    * School 园区
    
    | key | type | desc |
    | --- | --- | --- |
    | \_id | MongoId | 园区id |
    | name | string(2, 40) | 名称 |
    | avatar | FileId\|null | logo文件id |
    | intro | string(1, 200)\|null | 简介 |
    | address | string(1, 200)\|null | 地址 |
    | contact | Phone|Tel | 联系电话/手机 |
    
    * Unit 班级
    
    | key | type | desc |
    | --- | --- | --- |
    | \_id | MongoId | 班级id |
    | name | string(1, 10) | 名称 |
    | avatar |FileId\|null | logo |
    | intro | string(1, 200)\|null | 简介 |
    
    * Child 小孩
    
    | key | type | desc |
    | --- | --- | --- |
    | \_id | MongoId | 小孩id |
    | name | string(2, 10) | 姓名 |
    | gender | int | 性别，同User中gender |
    | avatar | FileId\|null | 头像 |
    | intro | string(1, 200)\|null | 简介 |
    | birthday | MongoDate\|null | 生日 |
    | address | string(1, 200)\|null | 地址 |
    | school | School | 学校 |
    | unit | Unit | 班级 |
    | father | User\|null | 父亲 |
    | mother | User\|null | 母亲 |
    
    * Teacher 班级教师
    
    | key | type | desc |
    | --- | --- | --- |
    | \_id | MongoId | 教师id |
    | role | string(2, 20) | 角色 |
    | task | string(2, 40) | 任务 |
    | user | User | 用户信息 |
    
    * Comment 评论
    
    | key | type | desc |
    | --- | --- | --- |
    | \_id | MongoId | 评论id |
    | senderInfo | User | 评论人信息 |
    | content | string(1, 200) | 评论内容 |
    | replyInfo | User\|null | 回复人信息，为空表示非回复 |
    | created_at | MongoDate |  |
    
    * Like 赞
    
    | key | type | desc |
    | --- | --- | --- |
    | senderInfo | User | 赞的人信息 |
    
[返回目录](#目录)

## 用户模块 `user`

### 登录 `login` `POST`

| key | type | desc |
| --- | --- | --- |
| username | string(6, 20) | 用户名 |
| password | string(6, 20) | 密码 |
| #type | int | 3: 教师, 4(默认): 家长 |

错误code:

| code | desc |
| --- | --- | --- |
| 500 | type错误 |
| 501 | 用户名或密码错误 |
| 502 | 绑定状态错误 |

教师登录返回:

| key | type | desc |
| --- | --- | --- |
| token | string | TOKEN |
| user | User | 用户信息 |
| school | School | 园区信息 |
| units | Unit[] | 绑定班级列表 |

家长登录返回:

| key | type | desc |
| --- | --- | --- |
| token | string | TOKEN |
| user | User | 用户信息 |
| children | Child[] | 所拥有的小孩列表 |

[返回目录](#目录)

### 修改密码 `changePassword` `POST`

| key | type | desc |
| --- | --- | --- |
| old | string(6, 20) | 旧密码 |
| new | string(6, 20) | 新密码 |

错误码：

| code | desc |
| --- | -- |
| 500 | 旧密码错误 |
| 501 | 新密码不符合要求 |

成功返回：**默认**

[返回目录](#目录)

### 联系人列表 `contact` `GET`

成功返回：

| key | type | desc |
| --- | --- | --- |
| owner | User | 班级主班教师 |
| teachers | Teacher[] | 教师列表 |
| users | User[] | 家长列表（当前班级有小孩的家长） |

[返回目录](#目录)

### 用户信息 `detail` `GET`

| key | type | desc |
| --- | --- | --- |
| #id | MongoId | 要获取资料的用户id，默认为当前登录用户 |

错误码：

| code | desc |
| --- | --- |
| 500 | 用户不存在 |

成功返回：

| key | type | desc |
| --- | --- | --- |
| d | User | 用户信息 |

[返回目录](#目录)

### 修改个人信息 `update` `POST`

| key | type | desc |
| --- | --- | --- |
| #name | string(2, 10) | 姓名 |
| #nickname | string(2, 20) | 昵称 |
| #gender | int | 性别，1男2女3未知 |
| #birthday | MongoDate\|null | 生日 |
| #intro | string(1, 140)\|null | 简介 |
| #address | string(1, 140)\|null | 地址 |
| #avatar | FileId\|null | 头像 |

错误码：

| code | desc |
| --- | --- |
| 500 | 数据错误 |

成功返回：

| key | type | desc |
| --- | --- | --- |
| d | User | 修改后的个人信息 |

[返回目录](#目录)

## 消息模块 `feed`

**预定结构**

* Feed 消息

| key | type | desc |
| --- | --- | --- |
| \_id | MongoId | 消息id |
| senderInfo | User | 发送人信息 |
| content | string(1, 500)\|null | 消息内容，`media.type`不为5时可以为空 |
| media | Media | 媒体信息 |
| comments | Comment[] | 评论列表 |
| like | Like[] | 赞列表 |
| created_at | MongoDate |  |

### 消息列表 `index` `GET` `PAGED`

| key | type | desc |
| --- | --- | --- |
| #type | int | 消息类型，`1`园方，`2`@，`3`发布，默认全部 |
| #stick | bool | 置顶消息，`true`置顶，默认`false`非置顶，如果为`true`则`type`无效 |

成功返回：

| key | type | desc |
| --- | --- | --- |
| d | Feed[] | 消息列表 |

[返回目录](#目录)

## 创建消息 `create` `POST`

| key | type | desc |
| --- | --- | --- |
| #content | string(1, 500)\|null | 内容，`media.type`不为`5`时可为空 |
| #media | Media|null | 媒体内容，默认`type`为`5` |
| #receivers | MongoId[] | @的人列表 |

错误码：

| code | desc |
| --- | --- |
| 500 | 数据错误 |

成功返回：

| key | type | desc |
| --- | --- | --- |
| d | Feed | 发布的消息结构 |

[返回目录](#目录)

### 删除消息 `delete` `GET`

| key | type | desc |
| --- | --- | --- |
| id | MongoId | 消息id |

错误码：

| code | desc |
| --- | --- |
| 500 | 指定id不存在 |
| 501 | 没有权限 |

成功返回：**默认**

[返回目录](#目录)

### 评论 `comment` `POST`

| key | type | desc |
| --- | --- | --- |
| fid | MongoId | 评论的消息的id |
| content | string(1, 200) | 评论内容 |
| #receivers | MongoId[] | @的人列表 |
| #reply | MongoId\|null | 回复的评论的id |

错误码:

| code | desc |
| --- | --- |
| 500 | fid 不存在 |
| 501 | 数据错误 |

成功返回：

| key | type | desc |
| --- | --- | --- |
| d | Comment | 评论数据 |

[返回目录](#目录)

### 删除评论 `deleteComment` `GET`

| key | type | desc |
| --- | --- | --- |
| id | MongoId | 评论id |

错误码:

| code | desc |
| --- | --- |
| 500 | id 不存在 |

成功返回：**默认**

[返回目录](#目录)

### 点赞 `like` `GET`

| key | type | desc |
| --- | --- | --- |
| fid | MongoId | 消息id |

错误码:

| code | desc |
| --- | --- |
| 500 | fid 不存在 |

成功返回：**默认**

[返回目录](#目录)

### 取消赞 `deleteLike` `GET`

| key | type | desc |
| --- | --- | --- |
| fid | MongoId | 消息id |

错误码:

| code | desc |
| --- | --- |
| 500 | fid 不存在 |

成功返回：**默认**

[返回目录](#目录)

### 置顶 `stick` `GET`

| key | type | desc |
| --- | --- | --- |
| fid | MongoId | 消息id |

错误码:

| code | desc |
| --- | --- |
| 500 | fid 不存在 |
| 501 | 权限不足 |

成功返回：**默认**

[返回目录](#目录)

### 举报信息 `report` `POST`

| key | type | desc |
| --- | --- | --- |
| fid | MongoId | 消息id |
| reason | string(5, 200) | 原因 |

错误码:

| code | desc |
| --- | --- |
| 500 | fid 不存在 |
| 501 | 数据错误 |

成功返回：**默认**

[返回目录](#目录)

## 任务模块 `task`