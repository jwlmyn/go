# 一、一条mysql查询语句是如何执行的？
![alt text](image.png)
## 1、连接器
（1）与客户端TCP握手后，建立连接，读取用户名密码，在权限表里查询权限
（2）连接成功后没有后续动作，那么连接处于空闲状态，如果客户端太长时间没动静，那么连接器会自动断开，默认八小时
## 2、查询缓存
（1）受到一个查询请求后会到查询缓存中查看，是否有这条数据，如果有则直接返回
（2）弊大于利，只要对一张表进行更新，那么它的查询环村就会被清空，如果更新频繁，那么缓存的作用就大打折扣，按需配置
## 3、分析器
（1）词法分析：识别SQL语句里面的字符串都代表什么
（2）语句分析：根据语法规则判断这个SQL语句是否满足MySQL语法
## 4、优化器
（1）决定使用哪个索引
（2）多表关联时，选择访问顺序
## 5、执行器
（1）判断用户权限
（2）返回结果
