# 一、什么是rpc？
1、屏蔽网络编程细节，调用远端的方法像调用本地一样简单
# 二、rpc通信流程
![image](https://github.com/user-attachments/assets/3a3ff586-4054-4b8e-9cda-3ee5fcb377b9)
1、rpc是一个远程调用，要保证数据的准确性，所以默认一般用TCP进行传输
2、网络传输是以二进制形式进行传输，所以中间有序列化和反序列化过程
3、涉及到动态代理技术，屏蔽具体细节（go语言中用的是反射，没有自带动态代理包）
# 三、可扩展向后兼容的协议
![image](https://github.com/user-attachments/assets/cde9f46d-076e-4e32-8860-c5389a734180)
1、请求头需要包括，消息ID、协议长度、协议版本、序列化方式、消息类型、扩展字段（防止有一些特殊的需求）
2、协议体里面放请求接口和参数
# 四、rpc为什么还要单独设计一个协议，而不用现有的http1.1协议等
1、rpc更多的是应用间的通信，对性能要求较高
2、HTTP数据包大小相对请求数据要大很多，还要加很多无用的内容，比如回车换行符等
3、HTTP属于无状态协议，建立连接后断开，每次请求都要重新连接
4、HTTP2是2015年出来的，grpc底层直接用的HTTP2
# 五、有哪些常用的序列化？
1、JSON，需要额外开销空间大，且没有类型，比如java强类型语言，就需要反射来解决，服务方和客户端发送数据量要较小，否则严重影响性能
2、Hessian，比JSON紧凑，跨语言，但是对java中的一些对象类型不支持
3、Protobuf，不需要通过反射获取类型，序列化后比JSON和Hessian体积要小，序列化和反序列化很快
# 六、RPC框架如何选择序列化？
1、序列化与反序列化的性能效率
2、序列化与反序列化后的空间开销，二进制体积大小
3、序列化协议的通用性和兼容性
4、稳定性和可用率，要考虑是否有很多人用过、升级迭代后这个序列化协议，还可以稳定工作等因素
# 七、RPC框架更倾向于哪种网络IO模型？
1、四种IO模型：同步阻塞，同步非阻塞，IO多路复用、异步非阻塞
2、更倾向于IO多路复用，因为rpc更多应用在高并发场景下，考虑到系统内核、编程语言等
# 八、什么是IO多路复用？
1、多个网络连接IO可以注册到一个复用器（select）上，用户调用后阻塞，但是用户可以在这一个线程中，同时处理多个IO请求
2、好比我们去餐厅吃饭，这次我们是几个人一起去的，我们专门留了一个人在餐厅排号等位，其他人就去逛街了，等排号的朋友通知我们可以吃饭了，我们就直接去享用了。
# 九、什么是零拷贝？
![image](https://github.com/user-attachments/assets/91530da1-38e6-4bdf-ac61-87cf7066fae6)
1、进程每一次读写操作，用户态到内核态一次，内核态到网卡一次，返回数据再有两次，零拷贝就是解决用户态到内核态的过程，CPU的上下文切换，避免这一过程
2、虚拟内存：将用户控件和内核空间的数据都写到一个地方
3、主要两种实现方式：分别是 mmap+write 方式和 sendfile 方式，
mmap是利用了虚拟内存(划分为用户内存和内核内存)映射到用户空间中,sendfile可以直接从磁盘读取到内核中然后直接从内核发送到网卡,不需要进行用户态和内核态的转换拷贝

