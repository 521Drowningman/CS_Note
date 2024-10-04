# CPP

## 头文件

````c++
#pragma once      //该行其实是一个预处理指令，通常在创建头文件时被自动加上。
                  //那么该指令的作用就是使其所处的头文件只被include 一次，即头文件保护符，防止在一个编译单元中                            //多次引用同一个头文件
另外一个头文件保护符是:
#if   #endif      //但是该用法已经不常用了，业界通常使用上面的那个。
#  //该符号是预处理符号，带有该符号的代码会由预处理器进行处理
````

## link

> `c++`是将每个单独的文件编译成`obj`之后通过`link`连接起来。

## 类

### static

> 1. 类外的static,表示这部分内存在`link`时只能在该编译单元内可见。
>
> 2. 类内的static,表示这部分的内存(**无论是变量还是方法都是如此**)是这个类的所有实例所共享的。
>
>   + 在这个情况下,静态方法是不能访问类的实例的，因为无法指向某一个实例。(所以静态方法不能访问非静态的变量)
>
>   + 静态变量只能在类内声明,在类外定义或初始化。(因为静态成员属于整个类，而不是某个对象。)
>
> 	
>
> ~~~c++
> struct A
> {
>  static int x;
> };
> int A::x = 1; //定义加初始化
> 
> 假设该 x 的初始化在类内进行，即 x = 1, 随后在一次修改中将x修改为2
> 如果在 new 一个实例那么 x 会再次被初始化为 1 ，这就导致了错误产生。
> ```
> ~~~
>
>   + 局部作用域内的`static`,其生命周期是整个程序，但是其作用范围是函数区域内。
>
> 	
>
> ~~~c++
> void push()
> {
>     int i = 0;
>     i++;
>     std::cout << i << std::endl;   //每次调用该函数输出的结果都是1
> }
> 
> void push()
> {
>     static int i = 0;
>     i++;
>     std::cout << i << std::endl;  //每次输出的结果都比上次多1
> }
> ```
> ~~~
>
> 3. 如果不希望变量是全局变量的话，自然是`static`用的越多越好。

### 构造函数

在创建实例时自动调用的初始化函数。

> + 没有返回类型，且方法名和类名相同。
> + 如果没有自定义构造函数，但是类仍然会有一个默认的构造函数。(只不过这个函数是空函数什么也没有做)
> + 构造函数只会在实例化对象时被调用。
>
> ```c++
> class Example
> {
>     Example()   //构造函数，可以有参数也可以无参数
>     {
>         
>     }
>     Example(int x)   //有参数和无参数的版本可以放在一起，当实例化对象时提供的参数有或无会分别调用不同的构造函数
>     {                //这是也是重载的概念
>         
>     }
> }
> 
> 构造函数初始化列表：
> class Point
> {
>     public:
>         Point(int xx, int yy) : x(xx), y(yy)   //这里列表中的顺序要和声明变量的顺序一样，否则会导致错误
>         {
>             cout << "Constructor of Point" << endl;
>         }
>  
>     private:
>         float x, y;
> };
> 
> 上面一段代码在功能上等价于下面的一段代码：
> 
> class Point
> {
>     public:
>         Point(int xx, int yy)
>         {
>             x = xx;
>             y = yy;
>             cout << "Constructor of Point" << endl;
>         }
>  
>     private:
>         float x, y;
> };
> ```
>
> 

### 析构函数

在销毁一个对象时运行

析构函数的作用并不是删除对象，而是在撤销对象占用的内存之前，完成一些清理工作。使这部分内存可以被程序分配给新对象使用。

> + 为什么要使用析构函数进行对象销毁？     如果不释放无用的内存会导致**内存泄漏**！！
>
> ```c++
> class Example 
> {
>     ~Example() //析构函数和构造函数的差别很小
>     {
>         
>     }
> }
> ```

### 类的继承

继承使类之间有了相互关联的层级关系

作用：可以避免写很多重复的代码。

```c++
class Entity
{
public:
    float X,Y;
};

//这里Player 不仅仅是Player类型还是Entity类型
class Player : public Entity      //: public Entity   这便表示继承Entity类
{             继承方式  基类        //对于Player来说Entity中的非私有的成员等等都是可以访问的
            //还有其他的继承方式如： private protected   
}

在继承的情况下, 创建子类对象,首先调用父类的构造函数然后是子类的构造函数,即由内到外进行构建.析构则是按相反的顺序进行调用,即由外到内.
复合和继承的情况相同.
```

### 虚函数

作用 ： 可以在子类中重写方法

核心目的：**通过基类访问派生类定义的函数**

声明基类指针，利用指针指向任意一个子类对象，调用相关的虚函数，动态绑定，由于编写代码时不能确定被调用的是基类函数还是那个派生类函数，所以被称为“”虚“”函数。如果没有使用虚函数的话，即没有利用`C++`多态性，则利用基类指针调用相应的函数的时候，将总被限制在基类函数本身，而无法调用到子类中被重写过的函数。
> 利用virtual关键字来定义虚函数，能增加代码的可读性。
>
> ```c++
> class base
> {
> public:
>  base();
>  virtual void test();  //定义了一个虚函数
> }
> ```
>
> 对于子类来说：要避免重写时出现一些不必要的`bug`可以使用override,final
>
> + override：保证在派生类中声明的重载函数，与基类的虚函数有相同的签名；
> + final：阻止类的进一步派生 和 虚函数的进一步重写。
>
> ```c++
> class Base {
> public:
>     virtual void Show(int x); // 虚函数
> };
> 
> class Derived : public Base {
> public:
>     virtual void Show(int x) const override; // const 属性不一样，新的虚函数 
> };
> ```
>
> 如果不希望某个类被继承，或不希望某个虚函数被重写，则可以在类名和虚函数后加上 final 关键字，加上 final 关键字后，再被继承或重写，编译器就会报错。
>
> ```c++
> class Base {
> public:
>     virtual void Show(int x) final; // 虚函数
> };
> 
> class Derived : public Base {
> public:
>     virtual void Show(int x) override; // 重写提示错误  
> };
> ```
>
> virtual 函数: 希望子类重新定义, 而且该函数已有默认定义.
>
> pure virtual 函数: 希望子类一定要重新定义, 而且该函数无默认定义.

### 接口类or纯虚函数

基类中有一些未实现的函数，强制子类实现实现这些函数。

> + 接口类不能被实例化，所以无构造函数，析构函数。 同时不能有变量。
> + 继承接口类的子类只有在实现了所有纯虚函数后，才能实例化。
> + 定义的接口方法使用virtual 修饰符 和 “=0” 修饰，表示该方法是纯虚的。
>
> ```c++
> class Example
> {
>   public:
>     virtual void Get() = 0;   //表示该方法是纯虚
> }
> ```
>
> + 如果父类实现了接口类的抽象方法，子类可以提供自己实现也可以不提供，如果不提供,子类会调用父类的实现方法。

### 可见性

三种：public  ,  private  ,  protected

> + public：可以被该类中的函数、子类的函数、友元函数访问，也可以由该类的对象访问；
> + protected：可以被该类中的函数、子类的函数、友元函数访问，但不可以由该类的对象访问；
> + private：可以被该类中的函数、友元函数访问，但不可以由子类的函数、该类的对象、访问。

### new

该关键字的主要目的：分配内存（在堆上分配）

分配后返回指向该内存空间的地址。

```c++
int* a = new int; 
int* a = new int[50];   //200 bytes

class Entity
{
 private:
     String m_Name;
 public:
     Entity() : m_Name("Unknown") {};
     ENtity(const String& name) : m_Name(name) {};
}

Entity* entity = new Entity();  //当 new 对类使用时不仅分配了内存还调用了类的构造函数
通常情况下，调用 new 就会调用底层的 c 函数：malloc
所以上述代码实际上是：
Entity* entity = (Entity*)malloc(sizeof(Entity));   //区别是该行只分配了内存，没有调用构造函数

****只要调用了 new 就一定要调用delete
delete entity;
delete[] entity;

可以重载全局性的new关键字或在类中以成员函数的形式进行重载. delete关键字也是如此.
在调用new关键字后,底层执行了三个步骤:
1.调用operator new 或 operator new[] 函数, 创建 void 类型的内存空间。
2.将该内存空间强转为需要的类型
3.调用构造函数创建对象
    
调用 delete 后,执行的步骤：
1.先调用析构函数
2.然后调用 operator delete 或 operator delete [] 删除内存空间.
    
placement new:
new() , delete()，  在用 new 创建指针时可以加入额外的参数.
```

### 隐式转换和构造函数

```c++
class Entity
{
private:
	std::string m_Name;
	int age;
public:
	Entity(int age) : m_Name("Unknown") {};
	Entity(const std::string& name) : m_Name(name) {};
};

Entity e = 22;   //这里就是22隐式转换为了Entity类
```

### explicit

> 这个关键字会禁用隐式转换，将该关键字放在构造函数的前面，则该函数不会进行隐式转换。（必须显示调用）
>
> 前缀，放在构造函数的前面。
>
> non-explicit-one-argument-ctor     非显性单参构造函数
>
> ```c++
> class Fraction
> {
> public:
>     Fraction(int num, int den = 1)
> 		:m_numerator(num), m_denominator(den) {}
>     Fraction operator+(const Fraction& f) 
>     {....}
> };
> 
> Fraction f(3,5);
> Fraction d = f + 4;   //这里会调用 非显性单参构造函数 将4转换为Fraction(4,1);然后调用operator+;
> ```
>
> 当给上述的 Fraction 构造函数加上 explicit 这个前缀 , 那么上面 4 的隐式转换就失效了.

### this

> 通过该关键字可以访问成员函数。
>
> 在函数内部，可以引用 this , this 是指向这个函数所属的当前对象实例的指针。
>
> ```c++
> class Entity 
> {
>  public:
>     int x,y;
>     
>     Entity(int x,int y)
>     {
>         this->x = x;
>         this->y = y;
>     }
> }
> ```
>



## 枚举

定义的一种新的数据类型，但是枚举是将整型常量整合在一起。 

本质上是为该类型的变量限制一个取值范围。

枚举的作用是：增加程序的可读性。

为什么叫枚举呢？  **可以将变量的值一一列出来，而且他的范围只限于列举出来的值的范围内取值**

```c++
enum Example
{
  A, B, C  
};
Example var = A;  //这个数据类型的变量取值只能是A,B,C

enum Example2 : unsigned char    //这里是将这个枚举类型设置为了  unsigned char类型
{                                注意：枚举的类型只能是整型的
    
}
```

## 数组

一些元素的集合

> 如果数组访问所用的索引超出界限，这称为内存访问冲突。 
>
> ```c++
> int a[5];   //在栈上创建数组
> int* a = new int [5];  //在堆上创建数组
> delete[] a;     
> ```
>

## 字符串

由字符组成的数组

> ```c++
> const char* a = "zmw" //指向字符串常量 右侧是一个 const char 数组
> char name2[4] = { 'z','m','w',0 };
> 上述两种是c风格的字符串
> 
> #include <string> //引入string
> std::string name = "zmw";  //同样这里并没有创建一个真的字符串，右侧同样是一个 const char 数组
> std::string("zmw");  //这是字符串构造函数 这里创建的是真正的字符串
> 
> "zmw";  //这是一个字符串字面量
> ```
>
> 在C语言中字符串常量的本质其实是一个地址。
>
> 如果直接输出是字符串，如果用*操作符的话输出的是字符串的第一个字符。
>
> ![image-20240515185405389](../../../AppData/Roaming/Typora/typora-user-images/image-20240515185405389.png)

## const和mutable

用于声明常量。

```c++
const int* a; ==  常量指针 == int const* a;
//a 可以指向其他的东西，但是不能修改指向的内容
int* const b;   指针常量
//b 可以修改指向的内容，但是不能指向其他的东西
const int* const a;  //就是都不能修改

class Entity
{
    private:
        int X;
    public:
        int GetX() const  //承诺这个方法不会更改类
        {
            X = 2;    //这是非法的
        }
}
mutable 允许const方法可以修改变量
mutable int a;
mutable 还有一个用法就是对lambda函数使用
    
    
int GetX() const   //在这种不修改数据的函数中，一定加上 const ，因为如果一个 const 对象想调用该函数时会因为该函数不是                      const 而失败 

const 对象只能调用 const版本的成员函数, non-const 对象都可以调用;
但是：当一个成员函数有const 和 nonconst版本, 则const对象只能调用const版本,而nonconst对象也只能调用nonconst版本；
```

## 三元运算符

> 是`if eles` 语句的语法糖(语法糖，也译为糖衣语法，是由英国计算机科学家彼得·兰丁发明的一个术语，指**计算机语言中添加的某种语法**，这种语法对语言的功能没有影响，但是更方便程序员使用。)
>
> ```c++
> if(level > 5)
> {
>     speed = 10;
> }else 
> {
>     speed = 5;
> }
> 
> 上述代码和下面的三元运算符的作用相同
> speed = level > 5 ? 10 : 5;
> 
> ```
>
> 

## 创建对象

> 创建对象区分为在栈上创建和在堆上创建。
>
> 在栈上创建：在栈上创建的对象随其所在的局部区域会有生命周期，当出区域后就会被销毁。而且栈的内存空间较小，如果要创建很多个对象那么栈可能容不下。
>
> ```c++
> class Entity
> {
>     private:
>         String m_Name;
>     public:
>         Entity() : m_Name("Unknown") {};
>         ENtity(const String& name) : m_Name(name) {};
> }
> 
> int main()
> {
>     Entity entity;  //调用无参数的构造函数
>     Entity entity("zmw");  //调用有参数的构造函数
>     Entity entity = Entity("zmw");
> }
> ```
>
> 在堆上创建：此时程序不会知道该对象你使用完了没有，所以需要手动删除对象。
>
> ```c++
> Entity* entity = new Entity("zmw");
> delete entity;   //用了new 就要用delete 清除
> ```
>
> 

## 操作符和操作符的重载

操作符是一种用来代替函数执行某些事情的符号。

简单来说 **操作符就是函数**。

```c++
struct Vector2
{
    float x,y;
    
    Vector2(float x , float y) 
        :x(x),y(y){}
    
    Vector2 Add(const Vector2& other) const
    {
        return Vector2(x + other.x, y + other.y);
    }
    
    通过重载 + 号来实现 Add 操作
    Vector2 operator+(const Vector2& other) const
    {
        return Add(other);
    }
    
}
```

> 返回值类型 类名::operator重载的运算符(参数表)    在类里面时可以省去类名
>    {
>    ……
>    }
>
> operator是关键字，它与重载的运算符一起构成函数名。
>
> 
>
> ```c++
> std::ostream& operator<<(std::ostream& out , const Vector2& other)   //用引用可以免于复制浪费时间
> {
>     out << other.x << "," << other.y;
>     return out;
> } 
> 重点：另外应该会有人对std::ostream& operator<<(std::ostream &out , const Point &a)函数感到疑惑，首先在重载<<时，返回值类型是ostream&, 第一个参数也是ostream& 。也就是说，表达式cout<<c的返回值仍是 cout，所以cout<<c<<endl;才能成立。
>     
> 当cout << <Vector2类的对象时> << std::endl;
>    //这里调用重载的<<       //这里相当于 cout << endl 调用重载前的 << 
> 所以当作是函数的重载来使用就好了
> 
> ```
>
> 

## 栈作用域的生存周期

> + 基于栈的变量在离开作用域时就会被摧毁，内存被释放。

## 智能指针

> 当在堆上分配内存时。即使用 new关键字来分配内存。
>
> 在释放时，需要人为手动使用 delete关键字来释放内存。
>
> **而智能指针的作用就是将该过程自动化。**
>
> pointer-like classes :智能指针是将一个类设计出来像指针.(将类设计为指针是因为想比指针多做一些事情)
> 
> ````c++
> template<class T>
> class shared_ptr
> {
> public:
>     T& operator*() const
>       {
>           return *px;
>        }
>       
>      T* operator->() const 
>      {
>            return px;
>      }
> private:
>     T* px;
>     
> };
>  ````
> 
> 

> ```c++
>唯一指针
> unique_ptr 是作用域指针，在超出作用域时会自动销毁。(最简单低功耗的智能指针)
>该指针是唯一的，不能将其复制给其他指针。 如果复制了，那么就会有两个指针指向同一个内存，如果一个释放了，那么另一个指针就指向了被释放的内存。
> 
>class Entity
> {
> public:
> Entity()
> {
>    std::cout << "created" <<std::endl;
> }
> ~Entity()
> {
>     std::cout << "destroyed" <<std::endl;
>   }
>    }
>   
>  int main()
>  {
>    std::unique_ptr<Entity> entity = std::make_unique<Entity>(); //这样创建会更安全
>  }
> 
> 共享指针
> shared_ptr 该指针的工作方式是通过 引用计数(用于跟踪指针有多少个引用) ，当引用的个数为0时，该指针就被删除了。
> 
>  共享指针需要分配另一块内存，叫控制块，用来存储引用计数，所以用下方的方法创建只需分配一次内存。
> std::shared_ptr<Entity> sharedEntity = std::make_shared<Entity>();
> 
> weak_ptr 当把共享指针复制给weak_ptr时不会增加引用计数。
> ```
> 

## 复制与拷贝构造函数

> 复制有时只是数据的拷贝。
>
> 浅拷贝：简单的复制拷贝操作，源对象与拷贝对象共用一份实体，仅仅是引用的变量不同（名称不同）。对其中任何一个对象的改动都会影响另外一个对象。
>
> 深拷贝：在堆区重新申请内存空间，进行拷贝操作，源对象与拷贝对象互相独立 ，其中任何一个对象的改动都不会对另外一个对象造成影响。
>
> ```c++
> int a = 2;
> int *b = &a;   //这里b其实复制的是 a 的地址
> 指针的大小取决于是64位编译模式还是32位编译模式
> 
> class String
> {
> private:
> 	char* m_Buffer;   //一个字符数组
> 	unsigned int m_Size;  //用于存储字符串的大小
> public:
> 	String(const char* string)
> 	{
> 		m_Size = strlen(string);
> 		m_Buffer = new char[m_Size + 1];
> 		memcpy(m_Buffer, string, m_Size+1);
> 	}
> 	friend std::ostream& operator<<(std::ostream& stream, const String& string);
> };
> 
> std::ostream& operator<<(std::ostream& stream, const String& string)  //重载 << 操作符用于输出String 对象
> {
> 	stream << string.m_Buffer;
> 	return stream;
> }
> 
> int main()
> {
> 	String string = "zmw";
>     String second = string;  //这是浅拷贝（只是新增了一个指针指向内存，而不是复制整个内存）
> 	std::cout << string << std::endl;
> 	std::cin.get();
> }
> ```
>
> 实现深拷贝的方法：
>
> 写一个拷贝构造函数：
>
> + C++在默认的情况下，会提供一个拷贝构造函数。但是这个默认的拷贝构造函数完成的就是浅拷贝。
>
> ````c++
> 如果将该默认的构造函数写出来的话形如：（上面那个String类的） 
> String(const String& other)
>  :m_Buffer(other.m_Buffer),m_Size(other.m_Size)  //因为这里对指针的拷贝也是直接赋值所以是浅拷贝
> {
> 
> }
> 
> String(const String& other)   //该拷贝构造函数就是用于深拷贝
>     :m_Size(other.m_Size)  //因为m_Size(该变量可以作浅拷贝)只是一个整数所以可以直接赋值
> {
>         m_Buffer = new char[m_Size + 1];   
>         memcpy(m_Buffer, other.m_Buffer,m_Size + 1);
> }
> String second = string;
> //这里的 = 不是赋值运算符，只是用于隐式调用构造函数的
> ````
>
> 深拷贝和浅拷贝的区别是在对象状态中包含其它**对象的引用**的时候，当拷贝一个对象时，如果需要拷贝这个对象引用的对象，则是深拷贝，否则是浅拷贝。

## 箭头操作符

> 简化解引用的过程：
>
> ```c++
> class Entity
> {
> public:
>     void print() const {std::cout << "Hello!" << std::endl}
> };
> 
> int main()
> {
>     Entity e;
>     e.print();
>     
>     Entity *ptr = &e;
>     (*e).print();
>     //箭头操作符简化
>     ptr->print();  //相当于先对 ptr 进行解引用然后再调用 print 函数
> }
> ```
>
> 

## Vector

> vector 是一段连续的内存
>
> vector 如何工作： 创建了一个vector,通过push_back添加元素，如果vector的容量不足以容纳新元素，此时vector 将分配一个新的足以容纳下新元素的内存，然后将内存中旧位置的vector的内容复制到新位置中，然后将旧位置的内存删除。
>
> 优化：就是因为内存不足后需要重新分配内存以及复制元素，这就导致了运行速度的下降
>
> ```c++
> struct Vertex
> {
> 	float x, y, z;
> 
> 	Vertex(float x, float y, float z)
> 		: x(x), y(y), z(z)
> 	{
> 
> 	}
> 
> 	Vertex(const Vertex& vertex)
> 		: x(vertex.x),y(vertex.y),z(vertex.z)
> 	{
> 		std::cout << "copied!" << std::endl;
> 	}
> };
> 
> int main()
> {
> 	std::vector<Vertex> vertexs;
> 	vertexs.push_back({ 1,2,3 });
> 	vertexs.push_back({ 4,5,6 });
> 
> 	std::cin.get();
> }
> 
> 这里产生了三次复制，因为我们在main函数中创建了 vertex 的对象，现在我们要将该对象放到vector中去，从 main 到vector所在的内存就产生了一次复制。还有的复制就是因为 vector 内存不足后需要重新分配内存复制导致的结果。
>     
> 优化方法：
>     给出一个预估的vector大小
>     vertexs.reserve(数量);
> 
>     使用 emplace_back
>     vertexs.emplace_back(1,2,3);  //这不是传递对象，而是传递参数让vector使用这些参数自己去创建对象
> ```
>
> 

## 使用c++库

> 如何选择使用的库的二进制文件是32还是64进制的呢？
>
> + 这取决于你的项目是32位还是64位的。
>
> 库主要是两部分组成：includes(包含目录)   library(库目录)
>
> + 包含目录： 是一堆头文件
> + 库目录：包含预先构建的二进制文件(通常包括动态库和静态库)
>
> 静态链接和动态链接的区别： 头文件同时支持动态链接和静态链接
>
> + 静态链接：意味着库会被放到可执行文件中，如exe文件
> + 动态链接：库在运行时被链接

## 处理多返回值

> 假设你有一个函数 , 此时你希望该函数能返回 int 或者 string , 那么可以使用结构体将想要返回的变量放入结构体中 , 然后将返回的类型改成结构体名即可。
>
> `pair 和 tuple :`
>
> + pair 是将 2 个数据合成一组数据，pair的实现是一个结构体，主要的两个成员变量是first second
> + pair 将一对值组合成一个值,这一对值可以具有不同的数据类型.
> + 必须提供两个类型名，两个对应的类型名的类型不必相同
>
> ```c++
> pair 也是一种模板类型
> pair<T1, T2> p1;            //创建一个空的pair对象（使用默认构造），它的两个元素分别是T1和T2类型，采用值初始化。
> pair<T1, T2> p1(v1, v2);    //创建一个pair对象，它的两个元素分别是T1和T2类型，其中first成员初始化为v1，second成员初始化为v2。
> make_pair(v1, v2);          // 以v1和v2的值创建一个新的pair对象，其元素类型分别是v1和v2的类型。
> p1 < p2;                    // 两个pair对象间的小于运算，其定义遵循字典次序：如 p1.first < p2.first 或者 !(p2.first < p1.first) && (p1.second < p2.second) 则返回true。
> p1 == p2；                  // 如果两个对象的first和second依次相等，则这两个对象相等；该运算使用元素的==操作符。
> p1.first;                   // 返回对象p1中名为first的公有数据成员
> p1.second;                 // 返回对象p1中名为second的公有数据成员
> ```
>
> + tuple是一个固定大小的不同类型值的集合，是泛化的std::pair。
> + std::tuple理论上可以有无数个任意类型的成员变量
>
> ```c++
> 
> std::tuple<T1, T2, TN> t1;            //创建一个空的tuple对象（使用默认构造），它对应的元素分别是T1和T2...Tn类型，采用值初始化。
> std::tuple<T1, T2, TN> t2(v1, v2, ... TN);    //创建一个tuple对象，它的两个元素分别是T1和T2 ...Tn类型; 要获取元素的值需要通过tuple的成员get<Ith>(obj)进行获取(Ith是指获取在tuple中的第几个元素，请看后面具体实例)。
> std::tuple<T1&> t3(ref&); // tuple的元素类型可以是一个引用
> std::make_tuple(v1, v2); // 像pair一样也可以通过make_tuple进行创建一个tuple对象
> ```
>
> 

## 模板

> 模板允许定义一个根据用途进行编译的模板.
>
> 编译器会自动的根据模板创建需要的函数或类.
>
> 举例：
>
> 当你需要多次重载一个函数时，可以将该函数变成模板。(还有模板类)
>
> ```c++
> template<typename T>
> void print(T value)
> {
> std::cout<<value<<std::endl;
> }
> 
> int main()
> {
> print(5);    //此时编译器根据模板创建一个参数是 int 类型的 print 函数
> print("zmw");  //该行同理
> }
> ```
>
> 成员模板, 多数出现在类的构造函数上.
>
> 
>
> 模板特化,与模板的范型编程相反.
>
> ```c++
> template <class Key>  //范型编程
> struct hash{};
> 
> template <>
> struct hash<long>    //对 long 类型的特化 
> {
> size_t operator()(long x) const {return x;}  
> };
> ```
>
> 当调用 hash<long> 时, 不是选择的范型,而是选择下面那个对long类型的特化版本.
>
> 
>
> 模板偏特化,- - 个数的偏
>
> ```c++
> template<typename T, typename Alloc=...>
> class vector
> {
>  ....
> };
> 
> template<typename Alloc=...>
> class vector<bool,Alloc>    //这里T被指定为bool,还有Alloc没有被指定所以是偏特化 
> {};                         //适用场景举例：已知bool只使用1bit，而其他类型最少也要1byte，所以如果用其他类型做bool会浪费内存
> ```
>
> 模板偏特化- - 范围的偏
>
> ```c++
> template <typename T>     //可以取任意的类型
> class C
> {};
> 
> template <typename T>     //只可以取指针类型
> class C<T*>
> {};
> //在未偏特化之前, T可以取任意的类型,但下面进行了偏特化后,只要是指针类型就要用下面那套模板.
> 
> C<string> obj1;   //用第一套模板
> C<string*> obj2;  //用第二套模板
> ```
>
> 模板模板参数
>
> ```c++
> template<typename T,
>          template <typename T> typename Container>    //指模板的参数还是一个模板
> class XCls
> {
> private:
>     Container<T> c;
> };
> 
> //template <typename T>  指出Container的类型是模板
> ```
>
> 数量不定的模板参数
>
> ```c++
> void print(){}
> 
> template <typename T,typename... U>     //typename...  表示一个模板参数包
> void print(const T& a, const U&... b)   //这里表示一个函数参数包   
> {
> 	cout << a << endl;
> 	print(b...);    //这里就形成了递归  
>     //sizeof...(b)  通过该函数得到这一包参数总共有多少个
> }
> 
> int main()
> {
> 	print(7.5, "hello", 9888);
> 	system("pause");
> }
> ```
>
> 

## 堆和栈的内存比较

堆和栈是ram中实际存在的两个区域，栈通常是一个预定义大小的内存区域，堆也是一个预定义的了默认值的区域，但是堆的大小可以随着应用程序的进行而改变。

> 二者区别：
>
> + 在内存分配时最大的不同是，是否用 new 来分配内存。 
> + 栈的内存在出作用域后是可以自动释放的，而堆需要手动才行。
> + 在栈上分配实际上就是一条 cpu 指令，而堆上分配是一堆操作的集合

new 实际调用了malloc 函数。

## 宏

在编译之前进行，是纯文本替换。

```c++
#define WAIT std::cin.get()

int main()
{
    WAIT;   //当预处理器看见该句时会自动将 WAIT 替换为 std::cin.get()
}

当然你也可以将 ; 同样放入宏中
#define WAIT std::cin.get();

int main()
{
    WAIT   //由于分号定义在宏中，所以在替换时，自带分号
}

多行宏的实现方法：
通过 \ 换行符进行换行即可
#define WAIT int main()\
{\
}
```

## auto

自动推断数据类型，auto 不处理引用，所以如果要引用要自己加 &号.

auto在推断时,会丢弃const和references.

## 静态数组

> 一个标准数组类，是 c++ 标准模板的一部分。
>
> 该静态数组指的是不增长的数组。
>
> array 与 vector 不同，array 是存在栈里面的。

```c++
#include <array>

int main()
{
    std::array<int,5> data;  //创建了一个整数数组，大小为5
    data[0] = 1;    //可以像传统数组一样用下标访问
    data.size();   //由于array 是一个类所以其有很多方法供其使用
}
```

## 函数指针

> 原始函数指针(来自 c 语言)：
>
> + 将函数赋值给变量
> + 将函数作为参数
>
> ````c++
> void HelloWorld()
> {
>     std::cout << "Hello World!" << std::endl;
> }
> 
> void PrintValue(int value)   //将该函数作为参数的类型是 void(*函数名)(int)
> {
>     std::cout<<"Value: "<< value << std::endl;
> }
> 
> void ForEach(const std::vector<int>& values, void(*func)(int))
> {
>     for(int value: values)
>         func(value);
> }
> 
> int main()
> {
>     auto function = HelloWorld;   //这里 auto 的类型是 void(*function)()
>     function();
>     std::vector<int> values = { 1, 2, 3};
>     ForEach(values,PrintValue);
> }
> ````
>
> 

## lambda函数

本质上是一种定义匿名函数的方式。

> + 像是一个一次性的函数，更多的是将其看作一个变量而不是真正意义上的函数。
>
> ````c++
> void ForEach(const std::vector<int>& values, void(*func)(int))
> {
>     for(int value: values)
>         func(value);
> }
> 
> int main()
> {
>     ForEach(values,[](int value){std::cout<<"Value: "<< value << std::endl});   //这就是lambda函数
> }
> ````

## namespace

尽量不要在全局使用`using namespace std`包括头文件 , 这样使用全局的情况,易导致错误。

如：当你引入了两个库,这两个库中有一个函数名相同的函数,当你给这两个库都是用了`using namespace`  后那么程序分不出来你到底想要调用哪个库中的函数。

```c++
//using namespace std 是可以在局部的作用域使用的

int main()
{
    using namespace std;    //当我在这里使用后，其作用域也只限制于main函数中
    cout<<"zmw"<<endl;
    return 0;
}
```

> + 名称空间的主要目的是避免命名冲突。
> + 在 c 语言中,是不存在命名空间的，所以在 c 语言中想要避免命名冲突的唯一方法就是在 名称 上下功夫。
> + 命名空间中可以嵌套别的命名空间。
> + 类也是一种命名空间。

## 线程

实现并行化

```c++
#include <thread>

void DoWork()
{
    
}

int main()
{
    std::thread worker(DoWork);   //传入的是函数指针，这个工作线程会执行DoWork中所有的工作
    
    worker.join();    //这是等待前一个线程结束，该行的作用是：当前的主线程暂停，等待工作线程全部执行完后在恢复执行
    
    std::cin.get();
}
```

## 计时

> + 计算完成某个操作或执行某个代码所需要的时间
> + 使用 `chrono` 库
> + chrono 的中文意思即：计时码表

```c++
#include <chrono>

int main()
{
    auto start = std::cherono::high_resolution_clock::now();
    //其他代码
    auto end = std::cherono::high_resolution_clock::now();
    
    std::chrono::duration<float> duration = end - start;   //duration的中文意思即：工期
    
    std::cin.get();
}
```

## 多维数组

````c++
int main()
{
    int** a2d = new int*[50];
    for(int i = 0; i < 50;i++)
    {
        a2d[i] = new int[50];
    }
}
````

## 排序

除了自己写排序的算法，还可以用 `c++`所提供的排序函数 `std::sort` 

````c++
int main()
{
    std::vector<int> values = { 3, 5, 1, 4, 2};
    std::sort(values.begin(), values.end());
}
````

## 类型双关

把拥有的这段内存，当作不同类型的内存来对待。

## 联合体

只能有一个成员的内存。 联合体经常和类型双关一起使用。

````c++
union
{
    float a;
    int b;
}
````

## inline内联函数

函数如果在类内定义完成 , 便成为inline候选人.

有些函数即使有inline的前缀 , 最后在使用时也不一定是内联的 , 因为过于复杂的函数编译器没有能力将其内联展开.

## 友元

相同 class 的各个 objects 互为friends. 

如果想让类外的函数或者别的类,访问到某个类中的 private 部分下的数据,则可以通过声明友元的方式.

```c++
如访问A类中的内容
class A
{ 
    friend xx;   //则该函数可以访问该类的私有部分
private:
    int b;
};
```

## 临时对象

通过 typename() ; 来创建临时对象.

## 转换函数

可以将某个类转换为别的类型.

如： 设置一个分数类(Fraction), 则该类应能被转换为double类型.

```c++
class Fraction
{
public:
	Fraction(int num, int den = 1)
		:m_numerator(num), m_denominator(den) {}
	operator double() const       //operator double就为该函数的名字,没有返回类型,因为函数名字中的double表示了返回的类型
	{
		return (double) m_numerator / m_denominator;
	}

private:
	int m_numerator;  //分子
	int m_denominator; //分母
};

Fraction f(3, 5);
double d = 4 + f;    //这里调用了 operator double这个函数
```

## 迭代器

用于容器中, 用于指向容器中的某个元素.

迭代器的区间一般是“前闭后开”; 即 end 指向容器中最后一个元素的下一个位置.

```c++
Container<T> c;
...
Container<T>::iterator ite = c.begin();
for(;ite != c.end();++ite) ... 
```

## 范围for循环

```c++
for(int i : {1,2,3,4,5,6,7,8,9})
{
    std::cout << i << std::endl;
}
```

## 虚指针和虚表

即 vptr 和 vtbl

当创建某个有虚函数的类的对象时, 该对象的内存空间中有一部分空间,将留给虚指针使用.该虚指针指向一个虚表, 该虚表维护着,该对象所处类的各个虚函数的地址.

![image-20240729221250226](../../../AppData/Roaming/Typora/typora-user-images/image-20240729221250226.png)

## streams

将流看作一个字符缓冲区,可以将缓冲区里的内容进行类型转换:将其转换为所需要的类型.

```c++
#include <iostream>
#include <sstream>
using namespace std;
int main()
{
    ostringstream oss("zmw", ostream::ate); //ate,将缓冲区的光标设置在后侧,此时每次向缓冲区中写入的内容都是紧接在原                                             内容的后面的
    ostringstream oss("zmw");  //调用输出字符串流的构造函数,将一个字符串输入至缓冲区中
    cout << oss.str() << endl; //想要从缓冲区中将字符串输出,就要先将缓冲区中的内容转为字符串,输出zmw
    oss << "666zmw"; //将原来的zmw给覆盖了,而不是接在zmw后面;在每次向缓冲区中写入时总是从头开始,所以会导致覆盖.
    cout << oss.str() << endl;//输出666zmw;
    
    istringstream iss("16.9 fly");
    double amount;
    string word;
    iss >> amount;   //这里会尽可能的寻找数字转换成double,因为16.9的后面是空格,所以这里double取16.9
    iss >> word;     //取fly;从iss中提取内容,然后将其转换为需要的类型.
    return 0;
}

void printStateBits(const istringstream &iss) // 用于查看当前的状态位
{
    cout << (iss.good() ? "G" : "-");
    cout << (iss.fail() ? "F" : "-");  //iss >> value 返回iss本身,所以可以当boolean使用;当fail()位打开时,为                                            false,关闭时为true.
    cout << (iss.eof() ? "E" : "-");
    cout << (iss.bad() ? "B" : "-");
    cout << endl;
}
```

### cin&cout

> + cout有缓冲区,在将字符推送到外部源之前,会将其先存储在缓冲区中.缓冲区中的内容要通过flush刷新出去.
>
> `cout<<flush`
>
> ````c++
> void bufferExperiment(ostream &oss)
> {
>     oss << "CS";
>     mindlessWork();
> 
>     oss << "106";
>     mindlessWork();
> 
>     oss << "L";
>     mindlessWork();
>     oss << flush;   
>     oss << endl;  //endl包括了flush.
> }
> ````
>
> + cerr无缓冲区,送入该流的字符会直接被打印到控制台上,而不会通过缓冲区.
> + clog有缓冲区.
>
> ```c++
> int num;
> cout << "CS";
> cout << "106";
> cout << "L";
> cin >> num;   //在等待用户输入时,会刷新cout
> ```
>
> 

### getline

getline和箭头提取符之间的区别:

+ 箭头分隔符以空格作为分界点.
+ getline以换行符为分界,getline会读入换行符,而且会消耗该换行符.
+ 但是两者最后都会返回流.

### cin和getline进行混用时可能出现的bug

```c++
istringstream iss("16.9\n44");
double val;
string line;
iss >> val;   //16.9 此时在缓冲区中指针指向换行符;因为 >> 不会跳过空格
getline(iss , line);  //"" 换行符被输入进了line中;getline最多读到换行符或缓冲区结束
iss.ignore(); //这个函数会跳过流中的一个字符.
```









vscode配置c/c++环境步骤:

1. 首先下载mingw-w64;配置环境变量
2. 在vscode中按下 shift+ctrl+p 选择c/c++编辑配置.
3. C语言用GCC , C++ 用G++
4. 运行程序步骤: 首先点击终端中的配置任务,然后再点击终端中的运行生成任务.

![image-20240731230434216](../../../AppData/Roaming/Typora/typora-user-images/image-20240731230434216.png)

+ ![image-20240504220223890](../../../AppData/Roaming/Typora/typora-user-images/image-20240504220223890.png)
