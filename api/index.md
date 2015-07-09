# 萌养时光第三版 接口文档

## 更新历史

* 2015-7-9: 初始化文档, 已完成模块: User, Feed

## TODO

* 推送
* 聊天
* 频道
* 等等等等

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

### 翻页

*   接口可翻页标志: `PAGED`
*   如果接口可翻页, 可在入参中加上`limit`字段限制每页数据条数, 默认为`10`
*   向前翻页(即拉取新消息): 在入参中加上`prev`字段, 如无特殊说明, 值为当前接口返回的数据中第一条数据的`_id`字段
*   向后翻页(获取历史消息): 在入参中加上`next`字段, 如无特殊说明, 值为当前接口返回的数据中最后一条数据的`_id`字段
*   `prev`字段的优先级高于`next`

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
    | Url | 链接 | 符合链接正则的字符串, 最长2kb |

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

### 身份验证

*   除了用户登录接口外均需要验证, 如果验证失败, 则返回status为401
*   验证方式: 在请求头部加入AUTHORIZATION字段, 值为用户登录时返回的token
*   token有效期为1个月(这个有效期必需有, 这是PHP中SESSION控制机制), 每次请求会自动续期
*   如果当前用户在另一个地方重新登录拿了新的token, 则当前token失效

## 接口列表

### 用户模块 user

#### 登录 login `POST`

| key | type | desc |
| --- | --- | --- |
| username | string | 用户名 |
| password | string | 密码 |
| type | int | 3: 教师, 4: 家长 |

错误code:

| key | type |
| --- | --- | --- |
| 500 | type错误 |
| 501 | 用户名或密码错误 |
| 502 | 绑定状态错误 |

教师登录返回:

家长登录返回:

