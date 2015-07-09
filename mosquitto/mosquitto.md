# 名称
mosquitto - 一个MQTT 服务器

# 大纲
mosquitto [-c configFile] [-d | --daemon] [-p port] [-v]

# 描述
mosquitto 是一个MQTT协议3.1版本的broker

# 选项
* -c, --config-file
通过文件加载配置, 如果不指定则使用mosquitto.conf中描述的默认值
* -d, --daemon
作为一个守护进程在后台运行mosquitto. 所有其它的表现没有变化
* -p, --port
指定监听端口
* -v, --verbose
使用长日志模式, 相当于将配置文件中的log_type设置为all, 此选项会覆盖配置文件中的配置

# 配置
mosquitto可以通过配置文件进行配置

# Broker状态
客户端可以通过订阅$SYS层的以下话题获取到Broker的状态信息, 如果话题被标注为静态的, 
则此状态只在客户端订阅时发送, 否则会每间隔一段时间更新一次, 间隔时间由配置文件中的
sys_interval设定, 单位为秒, 如果设置为0, 则不更新

* $SYS/broker/bytes/received
自Broker启动以来收到的数据包总大小(Byte)
* $SYS/broker/bytes/sent
自Broker启动以来发送的数据包总大小(Byte)
* $SYS/broker/changeset **STATIC**
当前版本Broker的更新
* $SYS/broker/clients/active
当前正连接上的客户端数量
* $SYS/broker/clients/expired
通过persistent_client_expiration选项移除和使过期的已断开的永久客户端数量
* $SYS/broker/client/inactive
未连接的永久性客户端数量
* $SYS/broker/clients/maximum
客户端最大同时连接数, 仅在$SYS层更新时统计, 因此短期连接的客户端可能未被统计
* $SYS/broker/clients/total
已连接的客户端和未连接的永久性客户端总数
* $SYS/broker/connection/#
当Broker配置上Bridge时, 此话题用于提供连接状态信息, 默认话题为$SYS/broker/connection/,
如果返回值为1则表示连接是活跃的, 0则不活跃
* $SYS/broker/heap/current size
当前使用的堆内存大小
* $SYS/broker/heap/maximum size
最大使用的堆内存大小
* $SYS/broker/load/connections/+
最近一分钟内收到的CONNECT相对于+时间段内的平均值的比值, +可以为1min, 5min, 15min
* $SYS/broker/load/bytes/received/+
最近一分钟内收到的数据包大小相对变化
* $SYS/broker/load/bytes/sent/+
最近一分钟内发送的数据包大小相对变化
* $SYS/broker/load/messages/received/+
最近一分钟内收到的消息数相对变化
* $SYS/broker/load/messages/sent/+
最近一分钟内发送的消息数相对变化
* $SYS/broker/load/publish/dropped/+
最近一分钟内被broker取消发布的消息, 意味着已断开的永久性客户端丢失消息的变化
* $SYS/broker/load/publish/received/+
最近一分钟内broker收到的消息发布相对变化
* $SYS/broker/load/publish/sent/+
最近一分钟内broker发布的消息数量
* $SYS/broker/load/sockets/+
最近一分钟内新增加的socket连接数量相对变化 
* $SYS/broker/messages/inflight
等待被确认的QoS大于0的消息数
* $SYS/broker/messages/received
自broker启动以来收到的消息总数
* $SYS/broker/messages/sent
自broker启动以来发布的消息总数
* $SYS/broker/messages/stored
包括RETAIN和QUEUE中的消息总数
* $SYS/broker/publish/messages/dropped
由于inflight/queuing限制而被broker丢弃的消息发布数量, 参见mosquitto.conf中max_inflight_messages和max_queued_messages
* $SYS/broker/publish/messages/received
自broker启动以来收到的PUBLISH消息数
* $SYS/broker/publish/messages/sent
自broker启动以来发送的PUBLISH消息数
* $SYS/broker/retained messages/count
现有的RETAIN消息数量
* $SYS/broker/subscriptions/count
现有的SUBSCRIBE数量
* $SYS/broker/timestamp **STATIC**
当前版本的broker的构建时间戳
* $SYS/broker/uptime
broker启动时长
* $SYS/broker/version **STATIC**
broker版本

# 订阅话题通配符
* +: 代表单一层级, 比如first/+/third可表示first/second/third, first/two/third等
* \#: 代表任意数量层级, # => a/# => a/b/#

$SYS不能被#替代, 可以通过$SYS/#订阅所有相关消息
**通配符只能单独表示一个层级, 比如a/+/c, 但是a+/c并没有通配符的效果**
**#通配符只能放在话题的最后**

# 桥接
多个broker可以通过桥接功能连接起来, 这对于期望在不同地方仅共享部分信息非常有用.
更多信息参见mosquitto.conf

# SIGNAL
## SIGHUP
    当收到此信号时, mosquitto将试图重新加载配置文件内容, 注意并不是所有的配置项能过这个办法都能重新加载
## SIGUSR1
    当收到此信号时, mosquitto将把数据写入到磁盘, 注意仅在persistence模式下生效
## SIGUSR2
    当收到此信号时, mosquitto会打印出当前的订阅树, 连同RETAIN消息一起. 这只是一个测试功能

# 文件
```
/etc/mosquitto/mosquitto.conf
    配置文件
/var/lib/mosquitto/mosquitto.db
    永久性消息储存位置(persistence模式下)
/etc/hosts.allow, /etc/hosts.deny
    通过TCP-wrappers控制的host访问限制, 参见hosts_access
```

# BUG
参见<http://launchpad.net/mosquitto>

# 参考

# 致谢

# 作者
Roger Light [roger@atchoo.org](mailto:roger@atchoo.org)