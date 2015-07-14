# 萌养时光推送服务器设计文档

## 基本思路

*   使用MQTT(mosquitto)作为broker
*   证书加密
*   话题以`topic/`开头
*   **以登录用户的用户`id`作为客户端`id`**
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
| id | MongoId | 推送消息的id, 可用这条来去重 |

## IOS

应用活跃时直接推送到broker, 后台时推送到APNS(是否需要也推到broker队列中?)

## broker与数据库

* broker只做推送, 数据全部放在数据库里面
* 客户端收到推送之后publish一个消息到`$log`话题里面, 消息结构如下, 服务器会监听这个话题进行数据处理

| key | type | desc |
| --- | --- | --- |
| c | int | =200: 成功处理, 其它则表示收到了但是数据有问题 |
| d | mixed | 如果c不等于200, 则把收到的消息原封不动的打回来 |
| m | string | 说明 |
| id | MongoId | 确认收到的消息id |

## ISSUE

*   是否考虑在用户登录时从服务器请求一次MQ? 而不是走MQTT