定义：
The facade pattern provides a unified interface to a set of interfaces in a subsytem.

感觉这个不算是一个模式，也许是它简单和过于常规了。 

对于client， 如果一个方法中 调用了多个对象的方法，那么client和这些对象就是高度耦合的。 

此时， 这个函数对于client来说是很臃肿的。 client是使用方，不需要知道太多的实现细节， 应该尽可能地减少其负担。 在这个场景中， 这个函数需要抽象出来， 令其成为一个接口方法。 client 组合这个接口对象。 好像找了一个秘书，那些繁琐的事情全部交给秘书负责， client只需要看到最后的结果。 同时，这也是实现的封装， 当方法需要修改， 只需要修改 这个秘书， client什么也不用做。 

顾客是上帝， 怎么能让上帝调整自己的结构呢？

抽象尺度的把握, 这在简单的系统中有点多此一举。
