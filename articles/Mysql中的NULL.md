## 探索下Mysql中的Null值

最近小白跟同事聊到一个问题，Mysql的null底层是什么呢？同事说是特殊的空字符串""，Oracle的Null确实是空字符串""，但对于Mysql可不好说，我觉得有些疑惑就去查了下Mysql的开发语言，居然是我由爱生恨的C++。。

### C++中的Null

先来说说C++中的Null吧。

先说说空指针，是由系统来保证空指针不指向任何实际的对象或者函数。反过来说，任何对象或者函数的地址都不可能是空指针。

NULL 是一个标准规定的宏定义，用来表示空指针常量。因此，除了上面的各种赋值方式之外，还可以用 p = NULL；来使 p 成为一个空指针。（tyc：很多系统中的实现：#define NULL (void*)0，与这里的“a null pointer constant”并不是完全一致的）

在标准中并没有对空指针指向内存中的哪个地方做出规定，也就是说用哪个具体的地址值（0x0 地址还是某一特定地址）表示空指针取决于系统的实现。

我们常见的空指针一般指向 0 地址，即空指针的内部用全 0 来表示（zero null pointer，零空指针）；也有一些系统用一些特殊的地址值或者特殊的方式表示空指针（nonzero null pointer，非零空指针），具体请参见C FAQ。

一般来说，在实际编程中不需要了解在我们的系统上空指针到底是一个 zero null pointer 还是 nonzero null pointer，我们只需要了解一个指针是否是空指针就可以了——编译器会自动实现其中的转换，为我们屏蔽其中的实现细节。注意：不要把空指针的内部表示等同于整数 0 的对象表示，有时它们是不同的。

在C语言中，NULL被定义为(void*)0，而在C++中，NULL被定义为整数0，编译器一般对其实际定义如下：

```cpp
#ifdef __cplusplus
#define NULL 0
#else
#define NULL ((void *)0)
#endif
```

出现C++和C定义不一致的原因是，在C++中不允许(void*)类型进行隐式转换，在C++中指针必须有明确的类型定义。不过也带来一个问题，在C++中NULL无法跟整数0进行区分，而且C++允许函数重载，如果同个函数名虽然参数类型不同，当以下情况输入NULL时，编译器分不清哪个函数执行时便会报错。

```cpp
void func(int data);
void func(char* data);
```

所以C++11有了nullptr一个新的空指针的常量值，虽然实际使用还是会有一点问题，就不详聊了。

### Mysql中的Null

先用Mysql试下Null和0的区别。

![](http://graysliver.oss-cn-shenzhen.aliyuncs.com/undefined/null.JPG)

Mysql中的NULL是占用空间的，下面是来自于MYSQL官方的解释。

>“NULL columns require additional space in the row to record whether their values are NULL. For MyISAM tables, each NULL column takes one bit extra, rounded up to the nearest byte.”

实际上，在我的探索中，发现Mysql的Null跟C++的null是不同的，所以有了上面0和NULL的区别，MYSQL的官方文档中有提及过：NULL不等于任何其他值，它是未知的。

>The `NULL` value means “no data.” `NULL` can be written in any lettercase. A synonym is `\N` (case sensitive). Treatment of `\N`as a synonym for `NULL` in SQL statements is deprecated as of MySQL 5.7.18 and is removed in MySQL 8.0; use `NULL`instead.

NULL应该被认为是指“没有指定的值”或“未知的值”（意味不一定没有，0或”“意味着没有）。 所以，这并不意味着它没有用，它只是意味着你非正式地将一些特殊的语义附加到null值，比较类似于true Or False（isNull函数）或者我就是想占个位告诉自己这里是没有指定值。



#### Reference

https://mahmudahsan.wordpress.com/2009/01/10/mysql-null-vs-vs-null/

http://itindex.net/detail/49930-%E7%AC%94%E8%AE%B0-mysql-null

https://dev.mysql.com/doc/refman/5.7/en/null-values.html

https://dev.mysql.com/doc/refman/5.7/en/problems-with-null.html