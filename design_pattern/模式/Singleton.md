巧克力沸腾锅的问题
    沸腾锅是一个状态机， 
        空状态下， 可以加巧克力，并设置非空。

        非空状态且没有煮过， 开煮， 并设置 煮过

        飞空状态且煮过， 沥干， 并设置 为空。

    这里的沸腾锅 只能存在一个实例， 如果存在多个， 会造成操作的冲突。
    比如一个实例设置完非空之后正在加巧克力， 还没加完就被另一个煮了。 


定义：
    Ensure a class only has one instance and provide a global point of access to it.
    taking a class and letting it manage a single instance of itself. To get an instance, you've got through the class itself.


使用场景、
    在系统中， 只允许一个实例对象存在的实体。如果实例化多个对象， 则会造成不恰当的行为， 资源的过度利用， 不一致的结果。 
    线程池， 缓存， 对话框盒子

    关键点在于 如何确保只实例化一个对象， 当对象存在且受到实例化新对象的请求时， 如果判断并阻止。

实现方式
    1. 限制类外被实例化： 将 构造函数的权限设置为  private
    2. 实现一个全局获取实例的方法， 这个接口中判断实例是否已经存在， 如果未存在则新建， 如果已存在则返回。 
        这个方法是全局的， 设置为 public static 返回类型就是当前的类。
    3. 返回的实例对象作为类的一个变量，当对象新建时赋值给该变量。 设置为  private static


一些相关的基础知识
    静态数据
    如果一个成员被static修饰， 则它能在任何对象呗实例化前访问。并且只能通过类名访问， 而不能通过任何实例化的对象以及  this, super访问。

    变量
        该变量是全局的， 由于不能被任何一个实例引用，它是属于类的。 Class.Name获得一个唯一地址，取得数据

    方法
        方法内只能 引用 同类的静态成员。 非静态成员只有在 实例对象中 才有意义， 静态方法和实例没有关系， 无法访问。

    静态数据在编译或是首次加载的时候就生成了， 而不是在实例化的时候才初始化。



处理多线程同步问题。
    这个模式中 的单例是一个全局的资源， 当多线程访问这个资源时， 有竞争问题。
    如果没有相应的管理措施， 线程同时开启访问， 可能会同时判断 对象为空， 从而各自新建了一个对象。 虽然第二个会把第一个覆盖掉， 但是两个线程此时获得的不是同一个对象。

    1. 使用 synchronized 去修饰 getInstance方法， 让这个方法加锁， 同一时间只有一个线程进入该方法执行， 其他线程必须等待其执行完毕之后才可进入执行。
        这是一个全局锁， 开销是较大的。而且， 我们只需要在创建实例前需要加锁的功能， 之后的所有访问都不需要了。

        如果系统可以忽略这个开销， 那么这是最直观的解决方案。 

    2.  使用static的开局就被创建的特性， 把实例的创建写到 变量赋值中。 这样类一加载实例就被创建。 每个线程访问时实例都已经存在， 而且访问的是同一个实例。

    3. 优化方案一， 只对没有实例时的部分加锁。 用 volatile 修饰 实例变量， 支持多线程的访问。
        ```java
            private volatile static Singleton uniqueInstance;
            public static Singletom getInstance(){
                if(uniqueInstance == null){
                    synchronized (Singleton.class){
                        // 只对这个匿名函数加锁， 也就是说前面判断实例不存在时， 执行这段加锁的代码。
                        if(uniqueInstance == null)
                            uniqueInstance = new Singleton();
                    }
                }
                return uniqueInstance;
            }
        ```
        多线程编程的参考：
        https://www.cnblogs.com/dolphin0520/p/3920373.html
