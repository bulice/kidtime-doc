# 萌养时光推送服务器设计文档

## 基本思路

*   使用MQTT(mosquitto)作为broker
*   证书加密
*   话题以`topic/`开头
*   以登录用户的用户`id`作为客户端`id`
*   `clear session`设置为`false`
*   `QoS`为`1`(需要客户端排重)

## 话题设计

*   推送: `topic/push/u_<user_id>|c_<class_id>`

`u_<user_id>`指推送到具体的某个人, `c_<class_id>`指按班级推送, 用户登录后需要订阅这些话题

## 消息结构

采用`json`, 包含两个字段:

| key | type | desc |
| --- | --- | --- |
| c | int | 消息类型, 比如@, 评论等等, 具体等梳理了再列出 |
| d | mixed | 不同的消息类型可能有不同的结构 |

## IOS

应用活跃时直接推送到broker, 后台时推送到APNS(是否需要也推到broker队列中?)

## TODO

*   broker与PHP通信?

怎么拿到broker回执? 需要建立一个PHP的`console`程序, 并且使用守护进程?