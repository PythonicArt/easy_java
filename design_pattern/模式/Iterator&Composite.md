定义
    The Iterator Pattern provides a way to access the elements of an aggregate object sequentially without exposing its underlying representation.


client 有多个成员对象，每个成员各自有一个数据成员， 可能由不同的集合实现的数据对象。 client需要分别对这些成员进行遍历。 


方案一：
    成员将数据成员 暴露给client， client 判断各个成员是由什么数据结构实现的， 然后再采取对应的遍历方法。 

    劣势
        1. client 负担较重， 它只是需要这个功能， 但是却需要掌握数据成员的类型和遍历方法。
        2. 代码很长。 每一个对象的遍历都要自己写一遍。 
        3. 对修改不友好。 当成员修改自己依赖的数据结构时，client也需要修改。


方案二： 
    大名鼎鼎的 迭代器模式。 

    可以看到， 以上变化的部分是 数据结构及其遍历方式。 把他们封装起来， 实现统一的接口。 比如 next(), hasNext()

    client 获得该迭代器对象， 这样就能进行统一的遍历即。
    while(oData.hasNext()){
        o = oData.next();
        do_something(o);
    }

    此时 client 无需知道 数据成员， 其采用的数据结构和遍历方法， 只要一个迭代成员即可.
    相当于找了一个迭代专家作为帮手， 它会源源不断地给我下一个数据。至于它怎么做到的， 我无需知道。 


一些思考
    1. 内部迭代器与外部迭代器的区别？
        上述的实现是外部迭代器， 即什么时候遍历交给client。 
        内部迭代器自己会遍历完整个数据容器，比如对每一个元素统一进行某个操作。 map，filter等。

    2. 遍历的顺序如何决定？
        client不知道数据集采用什么数据结构，和遍历方式， 所以连顺序也不知道的。 除非client与迭代器对象二者建立约定， 否则是未知的。


对成员对象的管理
    迭代器对象把 client 从遍历中 解救出来, 同时， client 需要对 支持迭代器的成员进行管理。 

    比如 client有 很多个这样的成员， 其都能够返回一个迭代器对象， 那么一次全部的遍历应该是把他们放到一个数组中， 逐一遍历。 而不是以一个个按照名称索引。 

    这样， 出现了两个数据集合。 

    一个是 client下的 数组， 管理 member, 每个member都能提供一个 迭代器， 负责对 member 的数据对象的成员 进行遍历。

    有一个升级的需求， member的数据对象成员 有可能是 具体的数据对象， 也有可能自身也是一个member类型的对象。 当遍历数据成员遇到member时， 不是简单的访问， 而应该把该member， 因为它自身也有一个数据集， 也能提供迭代器。

     这个需求可以理解成， 如何遍历一个集合， 该集合拥有不同类型的数据对象。 

    如果从整体来看， 上述的数据形成了一棵树。 client是根节点， 其数组管理的所有member是其子节点。 每个member又有自己的数据集合数据成员， 又成为他们的子节点。 这样在数据成员中， 如果是普通的数据对象， 那就相当于树中的叶子节点。 如果是member, 就相当于内部节点， 其数据成员是它的子节点。

    与树的区别。 每一个内部节点member, 可能管理不同的数据结构。不像真正意义上的树， 每个节点是相同的。 



The Composite Pattern
    allows you to compose objects into tree structures to represent part-whole hierarchies. Composite lets clients treat individual objects and compositions of objects uniformly.

    组合模式， 允许client统一的看待 普通数据成员和member.

    实现方式
        由于强类型语言的限制， 一个数据集只能放 相同类型的成员。所以， 必须抽象出一个公共的类， 数据结构实际存放该类对象。 即不管是普通数据成员， 还是member对象， 都要继承自这个父类。

        官方命名：
            父类： Component
                    子节点的管理
                    add(Component);
                    remove(Component);
                    getChild(int)

            叶子节点： Leaf
                print()// 访问方法
            member: Composite
                print()// 访问方法

        由于叶子节点没有数据集， 无需也不能进行子节点的管理。 所以， 上述管理方法可以留空覆盖， 也可以抛出异常禁止使用。

        由于不同的member可能拥有不同类型的数据集， 其需要自行管理子节点。

        综上， 叶子节点和内部节点就得到了统一处理。
        对于Composite 的 print()
        {
            iterator = data.iterator(); // 获取自身数据集合的迭代器。
            while(iterator.hasNext()){
                Component oComponent = (Component)iterator.next(); // 获取一个元素，并用父类对象限定。 
                oComponent.print(); 调用该对象的print方法， 如果待对象来自于一个leaf对象，就是访问其数据对象。 如果是一个Composite，就是本方法。 
            
            }
        }

        正如管理一棵树只需要知道其根节点， client就只需要知道其根 Component 即可。而根Component是一个 Composite 对象， 调用其print即可完成全员访问。 

        以上， 只是实现了一次性全员访问。 并不能像迭代器对象一样， 单一的获取元素。 

        暂且叫这棵特殊的树为组合树， 组合树迭代器的实现, 传入根Component对象的 iterator, 然后实现 hasNext, next 方法。其就是一个合格的迭代器了。        

        遍历顺序。 类似于深度优先遍历。 遇到一个节点， 
        如果是Composite, 则用下一步用其迭代器对象遍历元素， 当前迭代器先保存。等到该节点已经完全遍历再遍历当前迭代器的下一个元素。 
        如果是 Leaf, 其迭代器不能用来遍历元素， 仅作为一个标记， hasNext() 返回false

        
        class ComponentIterator{

            用一个栈来存放每次遇到的Composite 迭代器， 栈顶就是当前有效的 iterator
            用当前iterator获得元素， 如果是Composite， 则生成一个 CompositeIterator, 压栈。由于栈顶生效， 下一个元素就是这个Composite的数据集中的元素。 

            next(){
                if(hasNext()){
                    Iterator it = (Iterator) stack.peek();
                    Component oComponent = (Component) iterator.next(); // 获得一个元素

                    if(oComponent instanceof Composite){
                        stack.push(oComponent.createIterator()); // 这里 Composite返回时当前类的一个迭代器， Leaf返回的是一个 hasNext返回false的迭代器。
                    }
                    return oComponent;
                }else{
                    return null;
                }
            }

            如果栈为空， 无有效迭代器， 直接返回。 
            否则取出栈顶元素， 如果当前有效迭代器已经到结尾， 说明当前数据集的元素已经遍历完毕， 则弹出，判断上一级是否hasNext(), 容易得到这是一个递归的过程。 

            hasNext(){
                if(stack.empty()){
                    return false; 
                }else{
                    Iterator it = (Iterator) stack.peek();
                    if(!it.hasNext()){
                        stack.pop();
                        return hasNext();
                    }else{
                        return true;
                    }
                }
            }

        }

        
    使用情景
        一个集合中拥有不同类型的数据对象的迭代。 从强类型数据结构变成弱类型数据结构。而且实现方式相当于 把不同的对象(Composite, Leaf)放入一个个相同尺寸的盒子(Component), 通过继承的方式实现， 每个盒子都能提供一个迭代器， 遍历是通过迭代器， 而不是数据本身。 美哉。 

        继承主要是使用同一的对象管理方法， 而对于无需这些方法的节点， 则可以不实现或是抛出异常。 
        这极大的减轻了客户端对不同数据对象集合管理的负担， 只需一步一步地 next() 即可。 


