# 名称
mosquitto.conf - mosquitto的配置文件

# 大纲
mosquitto.conf

# 描述

mosquitto.conf是mosquitto的配置文件, 此文件可以被放到任意mosquitto可以读取的位置, mosquitto默认不使用配置文件并且会
使用相应的默认值. 配置文件加载方法参见mosquitto

# 文件格式

\#开头的行视为注释行

配置行以变量名开头, 相应的值通过一个空格与之分开

# 身份验证

身份验证选项部分与监听部分可能有有很大的关联性, 本章节旨在解释此种可能.

最简单的方式也是默认的方式是没有身份验证. 提供的未经验证的加密方式可以是基于SST/TLS的证书,可以通过相关配置cafile/capath, 
certfile/keyfile使用

作为协议的一部分, MQTT提供用户名/密码的身份验证方式, 使用password_file选项来定义有效的用户名和密码. 使用此种验证
方式需要在数据传输中对用户名和密码进行加密处理, 否则可能会遭遇暴力攻击.

当使用基于证书加密时有两个选项会影响到身份验证. 第一个是require_certificate, 此选项可以被设为true或者false, 如果为false,
则客户端的SSL/TLS会验证服务器上的证书但是无需客户端提供任何验证数据到服务器, 即仅通过MQTT内置的用户名/密码验证. 如果为true,
客户端必需提供一个有效的证书用来连接. 在这种情况下, 另一个选项use_identity_as_username将会生效, 如果设为true, 则通过客户端
证书提供的Common Name(CN)而不是MQTT用户名进行验证. 但是MQTT密码不会被替换, 因为系统认为只有已经验证过的客户端才有有效的证书.
如果设为false, 客户端必需验证MQTT用户名和密码(如果需要的话).

当使用基于通过psk_hint和psk_file选项获取的pre-shared-key(PSK)的加密方式时, 客户端必需提供有效的验证和key在任何MQTT通信发生
前用来连接到broker. 如果use_identity_as_username为true, PSK验证将会取代MQTT验证, 否则如果使用了password_file选项的话客
户端还需要提供MQTT验证.

证书和PSK加密均在per-listener basis部分配置

身份验证组件可以通过SQL等方式来替换password_file和psk_file选项(包括ACL选项)

可以同时支持多种验证机制

# 一般选项
*   `acl_file <file path>` *null* **RELOAD**

    设置访问控制文件列表(ACL)路径, 如果已设置, 则文件内容将会用于控制客户端对话题的访问.
    
    如果此参数被定义则只有被列出的话题才可以被访问
    
    *   `topic [read|write] <topic>`
    
    访问类型通过使用"read"或"write"来控制, 默认表示二者皆有, `<topic>`可以使用通配符, 仅对匿名用户生效, 如果希望文件对
    指定用户生效, 则使用下面一个参数
    
    *   `user <username>`
    
    此参数指定用户名(而不是客户端id)
        
    *   `pattern [read|write] <topic>`
    
    通过简单正则指定话题, 通配符如下:
    
    *   `%c`: 表示客户端id
    *   `%u`: 表示用户名
        
    e.g.:
    
    *   `pattern write sensor/%u/data`
    *   `pattern write $SYS/broker/connection/%c/state`
    
    注释行以#开头
    
*   `allow_anonymous [true|false]` *true* **RELOAD**

    是否允许客户端不提供用户名和密码进行连接
    
*   `allow_duplicate_messages [true|false]` *true* **RELOAD**

    如果一个客户端订阅了多个话题比如a/#, a/+/c, MQTT期望当broker收到一个消息的话题同时满足这些话题时比如/a/b/c, 客户端应该只收
    到一次消息
    
    mosquitto会一直跟踪客户端收到的消息以满足此需求, 此选项用来禁用这种跟踪, 可以有效减少内存使用量
    
    如果能够确保客户端不会订阅这样的话题可以设置为true, 否则客户端必需能够正确地处理重复的消息, 即使QoS=2

*   `auth_opt_* <value>`

    传递到验证组件的选项, 详见相关组件部分
    
*   `auth_plugin <file path>` *null* **NOT RELOAD**

    指定一个额外的模块用于身份验证和访问控制, 这允许自定义的用户名/密码和访问控制
    
*   `autosave_interval <seconds>` *1800* **RELOAD**

    自动将内存数据写入到磁盘的间隔时间, 如果为0, 则仅在broker退出或者收到SIGHUP信号时写入. **仅在persistence模式下生效**

*   `autosave_on_changes [true|false]` *false* **RELOAD**

    如果为true, 则mosquitto将会统计订阅变化, RETAIN消息, QUEUE消息的数量, 如果总数超过了`auto_interval`的值, 则将内存数据写
    入磁盘, 否则会使用`autosave_interval`
    
*   `clientid_prefixes <prefix>`    *null* **RELOAD**

    如果被定义, 只有具有与prefix匹配的前缀的clientid的客户端才可以连接, 重新加载时不影响已连接的client
    
*   `connection_messages [true|false]` ** **RELOAD**

    如果为true, 将会在客户端connect和disconnect时登记到log信息里面, 否则不登记
    
*   `include_dir <dir>` *null* **RELOAD**

    加载额外的配置文件件, 最好放在主配置文件的底部, 所有dir下的以.conf结尾的文件都会被加载, 此项仅在主配置文件中生效, 指定的文件夹
    不应该包含主配置文件

*   `log_dest [stdout|stderr|syslog|topic|file <file_name>|none]+` *stderr* **RELOAD**

    将日志信息发送到指定的目标, 可能的目标有stdout, stderr, syslog, topic
    
    stdout和stderr将日志信息输出到控制台
    
    syslog使用用户空间的系统日志设施, 通常在/var/log/messages或者类似的地方
    
    topic将日志发送到$SYS/broker/log/<severity>话题, severity可以是D, E, W, N, I, M, 分别表示debug, error, warning,
    notice, information, message. Message type severity is used by the subscribe and unsubscribe log_type options 
    and publishes log messages at $SYS/broker/log/M/subscribe and $SYS/broker/log/M/unsubscribe.
    
    file 需要额外的一个参数来指定文件, 当收到SIGHUP信号时此文件将会被关闭后重新打开, 文件只能是单独的一个文件
    
    none表示禁用log
    
*   `log_timestamp [true|false]` *true* **RELOAD**

    true则将时间戳打到日志中去
    
*   `log_type [debug|error|warning|notice|information|none|all]+` *error, warning, notice,information* **RELOAD**

    日志记录信息类型, debug信息不会被打到topic中去

*   `max_inflight_messages <count>` *20* **RELOAD**

    最大能够被同时处理传递的QoS为1或2的消息数, 包括握手和重试的消息数. **如果设置为1, 则可以保证消息按顺序发送**
    
*   `max_queued_messages <count>` *100* **RELOAD**

    存放在队列中的有冲突的消息上限, 0表示无上限(不推荐), 参见queue_qos0_messages选项
    
*   `message_size_limit <byte>` *0* **RELOAD**

    允许发布消息的负载的最大值, broker收到的大小超过此值的消息将不会被接收, 0表示由MQTT限制, 值为268435455
    
*   `password_file <file_path>` *null* **RELOAD**

    设置密码文件路径, 如果被定义, 则文件内容将用于控制客户端访问, 可以通过mosquitto_passwd工具构建. 如果使用的mosquitto不支持
    TLS(建议将TLS支持编译进去), 则此文件为text文件, 行的格式为<username>:<password>, 其中的分号和password不是必需的, 如果
    allow_anonymous设置为false, 则只有此文件中的用户才能连接, 否则, 可以配合acl_file使用令匿名用户只有读而经过验证的用户则有
    读写权限.
    
    RELOAD会清空老的数据, 但是不会影响已连接的client
    
*   `persistence [true|false]` *false* **RELOAD**

    如果为true, 连接, 订阅, 消息数据将会被写入硬盘, 文件由persistence_path和persistence_file指定, 当mosquitto重启时将会从从
    文件中加载这些信息. mosquitto在关闭或每间隔auotsave_internal时间或在收到SIGHUP时会将数据写入到磁盘. 如果为false, 则仅保存
    在内存中.
    
*   `persistence_file <file_name>` *mosquitto.db* **RELOAD**

    永久模式下数据在磁盘中存放的文件
    
*   `persistence_path <path>` *./* **RELOAD**

    永久模式下数据存放的路径
    
*   `persistence_client_expiration <duration>` *null* **RELOAD**

    此选项允许移除永久性客户端(`clean session`设置为`false`), 如果其在duration期间内未连接服务器的话. 这是一个非标准选项. MQTT认为
    永久性客户端是永远有效的.
    
    一个设计得不好的客户端会使用一个随机生成的`client id`, 并且将`clean session`设置为`false`. 这会导致其永远不会重新连接, 这个选项允
    许broker将这些客户端移除
    
    `duration`必需是一个整数`x`后面跟上字符`d`, `w`, `m`, 和`y`中的一个, 分别表示天, 周, 月, 年. 例如:
    
    *   `persistent_client 2d`
    *   `persistent_client 2m`
    *   `persistent_client 2y`
    
    作为一个非标准选项, 默认不设定此项表示永久性客户端永不失效.
    
*   `pid_file <file_path>` */var/run/mosquitto.pid* **NOT RELOAD**
    