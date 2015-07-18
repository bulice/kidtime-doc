# ISSUE列表

1. 家长登录无需带上`father`和`mother`字段
2. 家长在发送HTTP请求的时候，header上面要加上当前孩子所在的班级ID吧，这个班级ID在http header上面的key值写什么？

3. 目前客户端获取的数据均按园区和班级为单位，导致推送数据无法按用户推送。

    推送按`user_id`和`unit_id`来推送
    
4. Feed模块单独添加获取置顶消息接口

5. 文档说明教师和家长指定班级的方式
6. 删除评论 deleteComment GET 点赞 like GET 取消赞 deleteLike GET, 这个方法不应该用GET方法吧，删除的操作一般都是DELETE方法啊，然后点赞应该是POST方法吧
