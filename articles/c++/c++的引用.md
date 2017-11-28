## C++的引用



#### 引用的概念

<T> & 变量名  = 某变量名

> int n = 4;
>
> int & r = n; // r引用了n，r的类型是int &

某个变量的引用，等价于这个变量，相当于该变量的一个别名。

```c++
int n = 7;
int & r = n;
r = 4;
cout<<r; //输出4
cout<<n; //输出4
n = 5;
cout<<r;//输出5
```

#### 引用的用法

定义引用时一定要将其初始化成引用某个变量。

初始化后，他就一直引用该变量，不会再引用其他变量了。(从一而终特性)

引用只能引用变量，不能引用常量的表达式。

```c++
double a =4 ,b=5;
double & r1 =a;
double & r2 =r1;
r2 = 10;
cout<<a; //输出10
r1 = b;
cout<<a; //输出5
cout<<r2;//输出5
```

#### Example

```c++
void swap(int & a,int & b)
{
  int temp;
  temp = a;a=b;b=temp;
}
int n1,n2;
swap(n1,n2); //n1,n2的值被交换
```

#### 引用作为函数的返回值

```c++
int n =4;
int & setValue(){return n;}
int main()
{
  setValue()=40; // 对函数的结果赋值，实际是对n赋值
  cout<<n;
  return 0;
}

```

#### 常引用

定义引用时，前面加const关键字，即“常引用”。

```c++
int n;
const int & r =n;
// r的类型 const int &
```

特点是不能通过常引用去修改其引用的内容。

```c++
int n = 100;
const int & r = n;
r = 200; //编译错，试图通过r这个常引用来修改n
n = 200; //正确
```

#### 常引用和非常引用的转换

`const T &`和`T & `是不同的类型！

`T &`类型的引用或`T`类型的变量可以用来初始化`const T &`类型的引用。

`const T`类型的常变量和`const T &`类型的引用则不能用来初始化`T &`类型的引用，除非进行**强制类型转换**。