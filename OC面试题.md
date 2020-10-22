<!--
 * @Author: zluof
 * @Date: 2020-10-21 10:33:33
 * @LastEditTime: 2020-10-22 15:35:48
 * @LastEditors: zluof
 * @Description: 
 * @FilePath: /undefined/Users/zhongxiaoxi/Swift-Interview-questions/OC面试题.md
-->
#### 1. KVO
* `KVO全称KeyValueObserving`，是苹果提供的一套事件通知机制。允许监听对象特定属性的改变，并在改变时接收到事件。
* 本质：利用runtimeAPI动态生成一个子类，并让instance对象的isa指向这个全新的子类，当修改instance对象的属性时，在全新的子类重写set并调用willChangeValueForKey和didChangeValueForKey并调用内部会触发监听器的监听方法（observerValueForKeyPath:）

* 区别:
    - KVO和NSNotificationCenter都是iOS中观察者模式的一种实现。

    - 相对于被观察者和观察者之间的关系，KVO是一对一的，而NSNotificationCenter一对多的。

    - KVO对被监听对象无侵入性，不需要修改其内部代码即可实现监听。

    - KVO可以监听单个属性的变化，也可以监听集合对象的变化。(通过KVC的mutableArrayValueForKey)
* KVO的触发模式 
    - 在将要观察的对象里添加`+(BOOL)automaticallyNotifiesObserversForKey:(NSString *)key`  `(return YES;//默认，自动模式)  (return NO;//手动模式)`

    - 在属性变化前，调用`willChangeValueForKey`

    - 在属性变化后，调用`didChangeValueForKey`
    
    - 无论属性的值是否发生改变,是否调用Setter方法，只要调用了`willChangeValueForKey`和`didChangeValueForKey`就会触发回调
* KVO原理
    - KVO 底层实现：首先KVO需要创建一个子类(NSKVONotyfing_Class)，这个子类是继承于被观察对象的，这个子类需要重写属性的setter方法，这个时候，外界在调用setter方法的时候，调用的是子类重写的setter方法。就是让外界的Class的对象的isa指针指向这个子类

    - 官文指出KVO的实现，使用了isa-swizzling。当一个object被观察后，该object的 isa 指针将会被修改指向新生成的中间类，而非之前的类；
    ![](https://img2020.cnblogs.com/blog/764024/202004/764024-20200416095844348-793562801.png)

* 注意点
    - 成员变量不能使用KVO，KVO的本质是动态生成一个子类，重写父类的setter方法，实现新值旧值的回调，而成员变量的修改不是setter方法赋值，成员变量没有setter

    - 手动创建`NSKVONotyfing_Class`,KVO不能生效，但是可以编译通过。系统生成的`NSKVONotyfing_Class`是运行时动态创建的

#### 2. KVC
* `KVC(KeyValueCoding)`键值编码，可以动态地访问和修改对象的属性，简单的来说，就是通过对象的属性名（key）给属性赋值，也可以通过属性名（key）获取值。

* 底层的执行机制(赋值操作)
    - 当调用setValue：属性值 forKey：@”name“的代码时，如下：

    - 程序优先调用`set<Key>:`属性值方法，代码通过`setter`方法完成设置

    - 如果没有找到`setName：`方法，KVC机制会检查`+ (BOOL)accessInstanceVariablesDirectly`方法有没有返回YES，默认该方法会返回YES，如果你重写了该方法让其返回NO的话，那么在这一步KVC会执行`setValue：forUndefinedKey：`方法，不过一般开发者不会这么做。所以KVC机制会搜索该类里面有没有名为`_<key>`的成员变量，无论该变量是在类接口处定义，还是在类实现处定义，也无论用了什么样的访问修饰符，只在存在以`_<key>`命名的变量，KVC都可以对该成员变量赋值。

    - 如果该类即没有`set<key>：`方法，也没有`_<key>`成员变量，KVC机制会搜索`_is<Key>`的成员变量。

    - 如果该类即没有`set<Key>：`方法，也没有`_<key>`和`_is<Key>`成员变量，KVC机制再会继续搜索`<key>`和`is<Key>`的成员变量。再给它们赋值。

    - 如果上面列出的方法或者成员变量都不存在，系统将会执行该对象的`setValue：forUndefinedKey：`方法，默认是抛出异常。

* 底层的执行机制(取值操作)
    - 当调用`valueForKey：@”name“`的代码

    - 首先按`get<Key>`,`<key>`,`is<Key>`的顺序方法查找getter方法，找到的话会直接调用。如果是BOOL或者Int等值类型， 会将其包装成一个NSNumber对象。
    
    - 如果上面的`getter`没有找到，KVC则会查找`countOf<Key>`,`objectIn<Key>AtIndex`或`<Key>AtIndexes`格式的方法。如果`countOf<Key>`方法和另外两个方法中的一个被找到，那么就会返回一个可以响应NSArray所有方法的代理集合(它是NSKeyValueArray，是NSArray的子类)，调用这个代理集合的方法，或者说给这个代理集合发送属于NSArray的方法，就会以`countOf<Key>`,`objectIn<Key>AtIndex`或`<Key>AtIndexes`这几个方法组合的形式调用。还有一个可选的`get<Key>:range:`方法。所以你想重新定义KVC的一些功能，你可以添加这些方法，需要注意的是你的方法名要符合KVC的标准命名方法，包括方法签名。

    - 如果上面的方法没有找到，那么会同时查找`countOf<Key>`，`enumeratorOf<Key>`,`memberOf<Key>`格式的方法。如果这三个方法都找到，那么就返回一个可以响应NSSet所的方法的代理集合，和上面一样，给这个代理集合发NSSet的消息，就会以`countOf<Key>`，`enumeratorOf<Key>`,`memberOf<Key>`组合的形式调用。

    - 如果还没有找到，再检查类方法`+ (BOOL)accessInstanceVariablesDirectly`,如果返回YES(默认行为)，那么和先前的设值一样，会按`_<key>`,`_is<Key>`,`<key>`,`is<Key>`的顺序搜索成员变量名，这里不推荐这么做，因为这样直接访问实例变量破坏了封装性，使代码更脆弱。如果重写了类方法`+ (BOOL)accessInstanceVariablesDirectly`返回NO的话，那么会直接调用`valueForUndefinedKey`:

    - 还没有找到的话，调用`valueForUndefinedKey`:

* KVC常用场景
    - 运行时动态地取值和赋值
    - 用KVC来访问和修改私有变量
    - Model和字典转换
    - 修改控件的内部属性

