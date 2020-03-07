# swift面试题
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

```
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

```
func swapTwoInts(_ a: inout Int, _ b: inout Int) {
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

```
switch 80 {
case "eighty":
    //编译通过并且匹配
case "not eighty":
    //
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
    //
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


