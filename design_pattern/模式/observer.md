实时天气及预报app的问题--通知的封装

app需要监听实时的天气数据， 当数据发生变化， 执行更新等一系列的操作

方案一：
在数据对象的changed方法中， 把变动的数据传给数据接收对象， 调用其更新方法。

缺点：
1. 有多少显示对象需要通知，在编码阶段就已经确定， 不支持动态调整。
2. 重复语句太多，需要对每一个通知的对象调用其方法。
3. 这个方法是变化的， 没有进行封装。 


观察者模式
Publisher  数据提供对象， 当数据发生变化，通知所有 subscribers
Subscribers 数据使用对象， 需要实时获得正确的数据。

关系的建立与解除， 数据的通知。

subscribers对象的数量， 什么时候被创建都是运行时决定的， 需要支持动态的建立与解除关系。

publisher 维护一个容器， 这里存放当前订阅数据的所有 subscribers。 当通知时刻来临， 遍历该容器。 通知 subscribers.
很容易想到， subscriber 统一实现一个接口， 该接口就是接受数据更新的方法， 参数就是最新的数据。

订阅就是把自己加到容器中， 取消订阅就是把自己从中移出。

关系
subscriber 引用一个 publisher的实例对象, 用于注册和解除， 以及数据查询。1-1
publisher 维护一个 subscribers组成的容器， 用于通知。 1-n


将publisher抽象
数据更新， 订阅与移除这些都是公共的操作， 唯一变化的就是具体的数据。 所以， 上述操作可以抽象成一个接口或是类。 有子类实现或继承。

继承是最省事的， 因为这些操作封装在父类中。 但是如果需要自定义容器和订阅方式， 则需要自己重写这些方法， 这时候接口的优势更明显。(Program to interfaces, not implementations)

java 提供了一套观察者模式的实现，采用继承的方式。 Observalbe 类 和 Observer 接口
不足之处
1. 类的实现， 局限性较多。 
2. setChanged() 方法是 protected的， 意味着这个方法只能在子类中使用， 而无法通过组合的方式在其他类中被调用。

一些常见的应用
    事件管理器
    gui程序
    警报响应系统
    活动事件侦听

erlang的gen_event也是一个典型的观察者模式的应用， 只是他们的实体都是erlang进程， 而不是对象。 通知是通过消息的传递， 而不是对象方法的调用。


一些细节

某些需求中， 不同的 subscriber 可能对数据的不同部分感兴趣， 而不需要数据的其他部分。如果把数据全部发给 subscribers是多余的。
可以将update值开成一个消息的通知， 而不传递具体的数据。由于 subscriber 有自己 publisher的实例的引用， 只需要调用其对应的数据接口获得自己需要的数据即可。

一个 subscriber 可能有多个不同的 publisher，可以选择每一个publisher对于不同的update接口， 也可以使用同一个update方法。
如果使用同一个， 则需要知道这是来自哪一个 publisher 的方法， update方法可以传递一个 publisher实例， 使用 instanceof 来判断区分


关于通知顺序
这个和具体的实现容器以及遍历方式有关。

一些总结
1. 关系可以动态的建立和解除， 而不是hard code的方式。
2. 耦合关系是松散的。 这句话的理解是， 两个对象尽可能地少知道彼此的细节， 当知道得越多， 调用的方法或是数据成员越多， 会使得修改和维护变得复杂。 观察者模式中， publisher 只知道 subscriber拥有update方法。 相反， subscribers知道 publisher 注册解除以及数据获取方法等必要方法， 这算得上是 loosely coupled.
3. java内置提供的 observable 类 有一些蹩脚之处， 这样也看到了 组合方式的优势。