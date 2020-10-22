* 总结关于swift的面试题------持续更新
* 来源于网上、书籍等
* [侵权即删-联系我](741136856@qq.com)

#### 1.Class 和 Struct 的区别
* 类是引用类型, 结构体为值类型

* 结构体不可以继承
* 值类型被赋予给一个变量、常量或者被传递给一个函数的时候，其值会被拷贝
* 引用类型在被赋予到一个变量、常量或者被传递到一个函数时，其值不会被拷贝。因此，引用的是已存在的实例本身而不是其拷贝

#### 2.理解Swift值类型的写时复制
* 只有当一个结构体发生了写入行为时才会有复制行为。

* 在结构体内部用一个引用类型来存储实际的数据，在不进行写入操作的普通传递过程中，都是将内部的reference的应用计数+1，在进行写入操作时，对内部的reference做一次copy操作用来存储新的数据，防止和之前的reference产生意外的数据共享。

* swift中提供该[isKnownUniquelyReferenced]函数，他能检查一个类的实例是不是唯一的引用，如果是，我们就不需要对结构体实例进行复制，如果不是，说明对象被不同的结构体共享，这时对它进行更改就需要进行复制。
 	
#### 3.defer的用法
* 使用defer代码块来表示在函数返回前，函数中最后执行的代码。无论函数是否会抛出错误，这段代码都将执行。

* defer 语句块中的代码, 会在当前作用域结束前调用。每当一个作用域结束就进行该作用域defer执行。

	```swift
	func doSomethingFile{
    openDirectory()
    defer{
        closeDirectory()
    }
    openFile()
    defer{
        closeFile()
    }
    // do other things
	}
	```

#### 4.inout 输入输出参数
* 函数参数默认为常量。试图从函数主体内部更改函数参数的值会导致编译时错误。这意味着您不能错误地更改参数的值。如果您希望函数修改参数的值，并且希望这些更改在函数调用结束后仍然存在，请将该参数定义为输入输出参数。

* 您可以通过将inout关键字放在参数类型的前面来编写输入/输出参数。一个在出参数具有传递的值中，由函数修改的功能，并将该部分送回出的功能来代替原来的值。有关输入输出参数的行为以及相关的编译器优化的详细讨论，请参见输入输出参数。

* 您只能将变量作为输入输出参数的参数传递。您不能将常量或文字值作为参数传递，因为无法修改常量和文字。当您将一个与号（&）作为变量传入in-out参数时，将它放在变量名的前面，以表明该变量可以被函数修改。

* 注意:输入输出参数不能具有默认值，并且可变参数不能标记为inout。

	```swift
    let temporaryA = a
    a = b
    b = temporaryA
	}
	// 参数a本身定义是常量，inout修饰，可以修改a的值
	var someInt = 3
	var anotherInt = 107
	swapTwoInts(&someInt, &anotherInt)
	print("someInt is now \(someInt), and anotherInt is now \(anotherInt)")
	// Prints "someInt is now 107, and anotherInt is now 3"
	```
#### 5.什么是高阶函数
* 一个函数如果可以以某一个函数作为参数, 或者是返回值, 那么这个函数就称之为高阶函数

#### 6. static和class的区别
* 在Swift中static和class都表示“类型范围作用域”的关键字。在所有类型中（class、static、enum）中，我们可以使用static来描述类型作用域。class是专门用于修饰class类型的。
* 1.static可以修饰属性和方法
	- 所修饰的属性和方法不能够被重写。
	- static修饰的类方法和属性包含了final关键字的特性，重写会报错
* 2.class修饰方法和计算属性
	- 我们同样可以使用class修饰方法和计算属性，但是不能够修饰存储属性。
 	- 类方法和计算属性是可以被重写的，可以使用class关键字也可以是static

#### 7.自定义模式匹配模式
* 可参考 [swift模式和模式匹配](https://www.cnblogs.com/wjw-blog/p/11674857.html)
* 模式：
代表单个或者复合值得结构，也就是说模式不是一个特定的值，它是一种抽象的结构，【一句话，不是特指，是泛指】。这样就可以用模式来匹配各种各样的值。
	- 例如：(x,y)可以匹配元祖（1.2），以及任何包含两个元素的元组。
除了利用模式匹配一个值以外，你可以从复合值中提取出部分或全部值，然后把各个部分的值和一个常量或变量绑定起来。

* swift中的模式分为两类：
	- 一种能匹配任何类型的值，另一种在运行时匹配某个特定的值，可能会失败。
	- 第一种模式用于结构简单变量，常量和可选绑定中的值。此类模式包括通配符模式，标识符模式，以及包含前两种模式的值绑定模式和元组模式。你可以为这类模式指定一个类型标注，从而限制它们只能匹配某种特定类型的值。
	- 第二种模式用于全局模式匹配。这种情况下，你试图匹配的值在运行时可能不存在。此类模式包括枚举用例模式，可选模式，表达式模式和类型转换模式。你在switch语句的case标签中，do语句的catch 子句中，或者再if,while,guard,for-in语句的case条件语句中使用这类模式。
	
* 重载 ～= 该运算符

	```swift
	switch 80 {
	   case "eighty":
    //编译通过并且匹配
	   case "not eighty":
	   default:
	     break
	}
	//以上代码编译直接失败失败
	//重载 ～= 函数
	func ~= (pattern: String, value: Int) -> Bool {
   	   if pattern == "eighty" {
       	return value == 80
       } else if pattern == "not eighty" {
            return value != 80
       } else {
            return false
       }
	}

	switch 80 {
	   case "eighty":
     //编译通过并且匹配
	   case "not eighty":
	   default:
   	     break
	}
	该switch编译通过
	```

#### 8.dynamic framework 和 static framework 的区别是什么
* 可参考[该文章](https://www.cnblogs.com/junhuawang/p/7598236.html)

* 静态库和动态库, 静态库是每一个程序单独打包一份, 而动态库则是多个程序之间共享

* 静态库和动态库是相对编译期和运行期的：静态库在程序编译时会被链接到目标代码中，程序运行时将不再需要改静态库；而动态库在程序编译时并不会被链接到目标代码中，只是在程序运行时才被载入，因为在程序运行期间还需要动态库的存在。

* 静态库 好处：
	- 模块化，分工合作，提高了代码的复用及核心技术的保密程度
	- 避免少量改动经常导致大量的重复编译连接
	- 也可以重用，注意不是共享使用
* 动态库 好处：
	- 使用动态库，可以将最终可执行文件体积缩小，将整个应用程序分模块，团队合作，进行分工，影响比较小
	- 使用动态库，多个应用程序共享内存中得同一份库文件，节省资源
	- 使用动态库，可以不重新编译连接可执行程序的前提下，更新动态库文件达到更新应用程序的目的。

* 不同点：
	- 静态库在链接时，会被完整的复制到可执行文件中，如果多个App都使用了同一个静态库，那么每个App都会拷贝一份，缺点是浪费内存。类似于定义一个基本变量，使用该基本变量是是新复制了一份数据，而不是原来定义的；
	- 动态库不会复制，只有一份，程序运行时动态加载到内存中，系统只会加载一次，多个程序共用一份，节约了内存。类似于使用变量的内存地址一样，使用的是同一个变量；

* 共同点：
	- 静态库和动态库都是闭源库，只能拿来满足某个功能的使用，不会暴露内部具体的代码信息

#### 9. Swift 与 Objective-C 的联系与区别？
* Swift和Objective-C 共用一套运行时环境，Swift 的类型可以桥接到Objective-C（下面我简称OC），反之亦然。两者可以互相引用混合编程。
其次就是，OC 之前积累的很多类库，在 Swift 中大部分依然可以直接使用，当然，Swift3之后，一些语法改变了很多，不过还是有迹可循的。OC出现过的绝大多数概念，比如引用计数、ARC、属性、协议、接口、初始化、扩展类、命名参数、匿名函数等，在Swift中继续有效（可能最多换个术语）。Swift大多数概念与OC一样。当然Swift也多出了一些新兴概念，这些在OC中是没有的，比如范型、元组等。

#### 10. Swift 比 Objective-C 有什么优势？
- Swift 容易阅读，语法和文件结构简易化。
- Swift 更易于维护，文件分离后结构更清晰。
- Swift 更加安全，它是类型安全的语言。
- Swift 代码更少，简洁的语法，可以省去大量冗余代码。
- Swift 速度更快，运算性能更高。

#### 11.Swift 是面向对象还是函数式的编程语言?
* Swift 既是面向对象的，又是函数式的编程语言。
说 Swift 是面向对象的语言，是因为 Swift 支持类的封装、继承、和多态，从这点上来看与 Java 这类纯面向对象的语言几乎毫无差别。

* 说 Swift 是函数式编程语言，是因为 Swift 支持 map, reduce, filter, flatmap 这类去除中间状态、数学函数式的方法，更加强调运算结果而不是中间过程。

#### 12.请说明并比较以下关键词：Open, Public, Internal, File-private, Private
* Swift 有五个级别的访问控制权限，从高到底依次为比如 Open, Public, Internal, File-private, Private。

* 他们遵循的基本原则是：高级别的变量不允许被定义为低级别变量的成员变量。比如一个 private 的 class 中不能含有 public 的 String。反之，低级别的变量却可以定义在高级别的变量中。比如 public 的 class 中可以含有 private 的 Int。

* Open 具备最高的访问权限。其修饰的类和方法可以在任意 Module 中被访问和重写；它是 Swift 3 中新添加的访问权限。
* Public 的权限仅次于 Open。与 Open 唯一的区别在于它修饰的对象可以在任意 Module 中被访问，但不能重写。
* Internal 是默认的权限。它表示只能在当前定义的 Module 中访问和重写，它可以被一个 Module 中的多个文件访问，但不可以被其他的 Module 中被访问。
* File-private 也是 Swift 3 新添加的权限。其被修饰的对象只能在当前文件中被使用。例如它可以被一个文件中的 class，extension，struct 共同使用。
* Private 是最低的访问权限。它的对象只能在定义的作用域内使用。离开了这个作用域，即使是同一个文件中的其他作用域，也无法访问。

#### 13.请说明并比较以下关键词：strong, weak, unowned
* Swift 的内存管理机制与 Objective-C一样为 ARC（Automatic Reference Counting）。它的基本原理是，一个对象在没有任何强引用指向它时，其占用的内存会被回收。反之，只要有任何一个强引用指向该对象，它就会一直存在于内存中。

* strong 代表着强引用，是默认属性。当一个对象被声明为 strong 时，就表示父层级对该对象有一个强引用的指向。此时该对象的引用计数会增加1。
* weak 代表着弱引用。当对象被声明为 weak 时，父层级对此对象没有指向，该对象的引用计数不会增加1。它在对象释放后弱引用也随即消失。继续访问该对象，程序会得到 nil，不亏崩溃
* unowned 与弱引用本质上一样。唯一不同的是，对象在释放后，依然有一个无效的引用指向对象，它不是 Optional 也不指向 nil。如果继续访问该对象，程序就会崩溃。
* 加分回答：

	- weak 和 unowned 的引入是为了解决由 strong 带来的循环引用问题。简单来说，就是当两个对象互相有一个强指向去指向对方，这样导致两个对象在内存中无法释放。
	- weak 和 unowned 的使用场景有如下差别：

		- 当访问对象时该对象可能已经被释放了，则用 weak。比如 delegate 的修饰。
		- 当访问对象确定不可能被释放，则用 unowned。比如 self 的引用。
		- 实际上为了安全起见，很多公司规定任何时候都使用 weak 去修饰。

#### 14. 说说Swift为什么将String，Array，Dictionary设计成值类型？

要解答这个问题，就要和Objective-C中相同的数据结构设计进行比较。Objective-C中，字符串，数组，字典，皆被设计为引用类型。

* 值类型相比引用类型，最大的优势在于内存使用的高效。值类型在栈上操作，引用类型在堆上操作。栈上的操作仅仅是单个指针的上下移动，而堆上的操作则牵涉到合并、移位、重新链接等。也就是说Swift这样设计，大幅减少了堆上的内存分配和回收的次数。同时copy-on-write又将值传递和复制的开销降到了最低。

* String，Array，Dictionary设计成值类型，也是为了线程安全考虑。通过Swift的let设置，使得这些数据达到了真正意义上的“不变”，它也从根本上解决了多线程中内存访问和操作顺序的问题。

* 设计成值类型还可以提升API的灵活度。例如通过实现Collection这样的协议，我们可以遍历String，使得整个开发更加灵活高效。

#### 15. 闭包是引用类型吗？ 

* 闭包是引用类型。如果一个闭包被分配给一个变量，这个变量复制给另一个变量，那么他们引用的是同一个闭包，他们的捕捉列表也会被复制。

#### 16.Swift mutating关键字的使用？
* 类是引用类型，而结构和枚举是值类型。默认情况下，不能在其实例方法中修改值类型的属性。为了修改值类型的属性，必须在实例方法中使用mutating关键字。使用此关键字，您的方法将能够更改属性的值，并在方法实现结束时将其写回到原始结构

#### 17.Swift定义常量 和 OC定义常量的区别？
```
OC:
const int price = 0;
Swift:
let price = 0
```
* OC中用 const 来表示常量，而 Swift 中用 let 来判断是不是常量
* OC中 const 常量类型和数值是在编译时确定的
* Swift 中 let 常量（只能赋值一次),其类型和值既可以是静态的，也可以是一个动态的计算方法，它们在运行时确定的。

#### 18. 闭包
* `闭包和函数是引用类型`，将函数或闭包赋值给一个常量还是变量，实际上都是将常量或变量的值设置为对应函数或闭包的引用。
  ```swift
  func makeInCount(count: Int) -> () -> Int {
      var total = 0
      func incrementer() -> Int {
          total += count
          return count
      }
      return incrementer
  }
  let incrementBySeven = makeInCount(count: 7)
  incrementBySeven()
  let alsoIncrementBySeven = incrementBySeven
  alsoIncrementBySeven()
  ```
* `逃逸闭包`，当一个闭包作为参数传到一个函数中，但是这个闭包在函数返回之后才被执行，我们称该闭包从函数中逃逸。当你定义接受闭包作为参数的函数时，你可以在参数名之前标注 @escaping，用来指明这个闭包是允许“逃逸”出这个函数的。 例如网络请求⬇️
  ```swift
  func request(result:@escaping((String)->())){
      DispatchQueue.main.asyncAfter(wallDeadline: DispatchWallTime.now() + 10) {
          result("数据结果")
      }
  }
  ```

* `非逃逸闭包`, 永远不会离开一个函数的局部作用域的闭包就是非逃逸闭包。
  ```swift 
  func player(complete:(Bool)->()){
      complete(true)
  }
  ```
* `自动闭包`，自动闭包是一种自动创建的闭包，用于包装传递给函数作为参数的表达式。这种闭包不接受任何参数，当它被调用的时候，会返回被包装在其中的表达式的值。当闭包作为参数传入 可用@autoclosure标记闭包参数 ，可将参数当函数调用而并非以闭包的形式。这种便利语法让你能够省略闭包的花括号，用一个普通的表达式来代替显式的闭包

   ```swift 
    var customersInLine = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]
    print(customersInLine.count)
    // 打印出“5”

    let customerProvider = { customersInLine.remove(at: 0) }
    print(customersInLine.count)
    // 打印出“5”

    print("Now serving \(customerProvider())!")
    // 打印出“Now serving Chris!”

    print(customersInLine.count)
    // 打印出“4”
    
    // customersInLine is ["Ewa", "Barry", "Daniella"]
    func serve(customer customerProvider: @autoclosure () -> String) {
        print("Now serving \(customerProvider())!")
    }
    serve(customer: customersInLine.remove(at: 0)) 
    // 打印“Now serving Ewa!”

    //不用  @autoclosure 修饰 
    func serve(customer customerProvider: () -> String) {
        print("Now serving \(customerProvider())!")
    }
    serve(customer: { customersInLine.remove(at: 0) } )
    //打印“Now serving Ewa!”
  ```
