# 萌养时光第三版 接口文档

## 更新历史
* 2015-7-9: 初始化文档, 已完成模块: User, Feed

## TODO
* 推送
* 聊天
* 频道
* 等等等等

## 接口列表

### [user]用户模块 user

#### [login]登录 login `POST`

| username | string | 用户名 |
| password | string | 密码 |
| type | int | 3: 教师, 4: 家长 |

错误:
| 500 | type错误 |
| 501 | 用户名或密码错误 |
| 502 | 绑定状态错误 |

教师登录返回:

家长登录返回:

