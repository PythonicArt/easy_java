 

Proxy provide a surrogate or placeholder for another object to control access to it.

代理提供一个具体对象的替代或是装载器， 用来这控制和管理 这个对象的访问。

Remote Proxy
    管理 client 和远端对象的交互。 client 通过代理直接获得一个远端对象.

    对象分类
        client,  客户端 proxy, 访问对象 RealObject
        远端proxy, 访问对象

    流程
        代理封装数据传输服务， 定义需要的类对象之后， 将请求发至远端， 远端收到请求得到 访问对象， 将访问对象序列化， 传至请求端

        看上去像是执行本地的方法， 其实请求发到远端执行了一个请求操作， get方法。

    需要解决的问题
        1. 数据服务的搭建
            涉及网络数据传输， 协议， 端口， 请求与相应。 对象的序列化与反序列化
                java 的 rmi服务封装这些功能， 
            这是同步还是异步的? 性能要求？
            请求与实际对象实例的映射
                绑定：Naming.rebind("Name", object);
                请求： InterfaceName object = (InterfaceName) Naming.lookup("rmi://ip/Name")

            请求端 如果管理 RealObject 二进制文件的?
                1. client提前存储一份copy， 包括其包含的所有数据成员的类的二进制实现文件。
                2. 动态加载。建立一个服务器专门存储需要用到的类二进制文件。 client在解序列化时遇到无法处理的类， 通过url访问该服务器，拿到对应的二进制文件。
    
    实现方法 
        1. 声明一个接口， 这个接口作为 RealObject的声明
        2. 服务端实现这个接口，这个实例作为proxy,  该实例注册到rmi服务中.
        3. 客户端通过rmi服务向 服务端请求, 得到对象 类型转换为 声明的接口类型，即只取接口声明的部分。

        第二步 proxy自己实例化了接口对象，从而可以替代RealObject注册到rmi服务中。 也可以使用另一个对象， 该对象拥有一个接口对象成员， 类似于包装的做法， 工厂模式。 
        但是注册到rmi服务的一定是实现接口服务对象。 


Virtrual Proxy
    管理 实例化代价很高的对象， 代价一般指时间久， 不能立即实例化， 比如有些数据来自远端。 代理对象实现所需实例化对象的全部方法， 在数据还没有就位的时候方法使用临时数据。 当数据就位，使用实际对象的数据。 

    这个和1的区别在于 是使用原对象还是对原对象进行包装。  

Protection Proxy
    动态管理 实体对象的 访问权限。 由于对象的访问权限在编码的时候就已经确定， 不断地去修改代码实现需求不现实。 
    client不直接访问实体对象， 而是通过代理访问。 代理根据client的身份， 决定访问权限。 

