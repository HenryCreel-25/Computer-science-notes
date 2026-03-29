# C++核心编程

## 1、内存分区模型

c++程序在执行时，将内存分为**4个区域**

> + 代码区：存放代码的二进制代码，由操作系统管理
> + 全局区：存放全局变量、静态变量和常量
> + 栈区：由编译器自动分配释放，存放<u>函数的参数值、局部变量</u>等
> + 堆区：由程序员分配和释放，若程序员不释放，则由操作系统回收



### 1.1 程序运行前

在程序编译后，生成了exe可执行程序，**未执行该程序前**分为两个区域

> **代码区：**
>
> 存放CPU执行的机器指令
>
> 代码区是**共享**和**只读**的
>
> **全局区：**
>
> 全局变量和静态变量存放在此
>
> 全局区还包含常量区、字符串常量和其他常量
>
> ==该区域的数据在程序结束后由操作系统释放==
>
> ```cpp
> const int c_a;
> static int s_a;
> int global_a; //全局变量
> “hello world”//字符串常量
> ```
>



### 1.2 程序运行后

> **栈区：**
>
> 由编译器自动开辟和释放，存放**函数的参数值，局部变量**等
>
> 注意事项：==不要返回局部变量的地址==，栈区开辟的数据在函数执行完后自动释放
>
> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> int* func() {
> 	int a = 10;//局部变量
> 	return &a;//返回局部变量地址，错误
> }
> 
> int main()
> {
> 	int* p = func();//接收地址
> 
> 	cout << "*p=" << *p << endl;//*p的输出不是10
>  
> 	return 0;
> }
> ```
>
> 
>
> **堆区**：
>
> 由程序员分配和释放，若程序员不释放，则由操作系统回收
>
> 在c++中主要利用new在堆区开辟内存
>
> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> int* func() {
> 	int* p = new int(10);//new关键字开辟内存，可以将数据开辟到堆区
> 	return p;
> }
> 
> int main()
> {
>  //在堆区开辟数据
> 	int* p = func();
> 
> 	cout << "*p=" << *p << endl;//p的输出为10
> 	cout << "*p=" << *p << endl;
> 
> 	delete p;
> 
> 	return 0;
> }
> ```
>



### 1.3 new操作符

c++利用new操作符在堆区开辟数据

> 堆区开辟的数据由程序员手动开辟，手动释放，释放利用==delete==操作符
>
> 语法：`new 数据类型`
>
> 利用new创建的数据，会返回该数据**对应类型的指针**
>
> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> int main()
> {
> 	char* p = new char('a');//new开辟char类型指针
> 
> 	cout << "*p=" << *p << endl;
> 	(*p) += 12;
> 	cout << "*p=" << *p << endl;
> 
> 	delete p;//delete释放内存
> 
> 	int* arr = new int[10];//[]用来开辟数组
> 	for (int i = 0; i < 10; i++) {
> 		cin >> arr[i];
> 	}
> 
> 	for (int i = 0; i < 10; i++) {
> 		cout << arr[i] << endl;
> 	}
> 
>     //释放数组内存需要在数组名前加上[]
>     //若只有数组名则只会释放数组的首地址
> 	delete[]arr;
> 
> 	return 0;
> }
> ```



## 2、引用

### 1.引用的基本使用

> 作用：给变量取别名
>
> 语法：`数据类型 &别名 = 原名`
>
> ```cpp
> int a = 10;
> int &b = a;
> ```



### 2.引用注意事项

> + 引用必须初始化
> + 引用在初始化后，不可以改变
>
> ```cpp
> int a = 10;
> int b = 20;
> //int &c;错误，引用必须初始化
> int &c = a;//一旦初始化后，就不可以更改
> c = b;//这是赋值操作，并非更改引用
> //a,b,c的值全为20
> ```



### 3.引用做函数参数

作用：函数传参时，可以利用引用让形参修改实参

优点：可以简化指针修改实参

> ```cpp
> void swap(int &a,int &b){
>     int temp = a;
>     a = b;
>     b = temp;
> }
> 
> int main()
> {
>     int a = 25;
>     int b = 19;
>     
>     swap(a,b);
>     
>     return 0;
> }
> ```



### 4.引用做函数返回值

> 注意：**不要返回局部变量的引用**
>
> 其他用法：函数调用作为左值
>
> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> int& test01() {
> 	static int b = 10;//静态变量放在全局区，程序结束后才销毁
> 	return b;
> }
> 
> int main()
> {
> 	int &a = test01();
> 
> 	cout << a << endl;
> 	cout << a << endl;
> 	
>     //如果函数做左值，那么必须返回引用
> 	test01() = 2519;//函数调用做左值
> 	cout << a << endl;
> 
> 	return 0;
> }
> ```



### 5.引用的本质

本质：**引用的本质在c++内部是一个==指针常量==**

> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> void func(int& ref) {
> 	ref = 100;
> }
> 
> int main()
> {
> 	int a = 100;
> 
> 	//自动转换为int* const ref = &a;
> 	//指针常量是指针指向不可改，也说明为什么引用不可以更改
> 	int& ref = a;
> 	ref = 20;//编译器发现ref是引用，自动帮我们转换为：*ref = 20；
> 
> 	cout << a << endl;
> 	cout << ref << endl;
> 
> 	func(a);
> 
> 	return 0;
> }
> ```
>
> 引用本质是指针常量，但是所有的指针操作编译器都帮我们做了



### 6.常量引用

作用：在函数形参列表中，可以加const修饰形参，防止形参修改实参

> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> void showValue(const int& v) {
> //	v = 100;
> 	cout << v << endl;
> }
> 
> int main()
> {
> 	//int& ref = 10;引用本身需要一个合法的内存空间(被初始化的变量)，因此这行运行错误
> 
> 	const int& ref = 10;
> 	//加上const就行，编译器优化代码代码过程:int temp = 10;const int&ref = temp;
> 
> 	cout << ref << endl;
> 
> 	//利用常量引用防止误操作修改实参
> 	int a = 10; 
> 	showValue(a);
> 
> 	return 0;
> }
> ```

### 7.引用的使用时机

引用在两种时机的使用时需要区别于传值

> + 做函数参数
> + 做函数返回值

> ==做函数参数时：==
>
> 使用**引用**的情况：
>
> + **需要修改传入的参数**
> + **传递(大型的)自定义对象（避免拷贝的内存开销）**
> + 需要输出多个值（通过函数参数返回结果）
> + 传递不可复制的对象（unique_ptr）
>
> 使用**值传递**的情况：
>
> + 基本类型（int,char,double…）
> + 需要内部副本（需要修改副本而不影响原值）

> ==做函数返回值时：==
>
> 使用**引用**的情况：
>
> + 返回类成员
> + 返回静态或全局变量
> + 运算符重载（链式调用）
>
> 使用**值传递**的情况：
>
> + 返回局部计算结果



## 3、函数提高

### 3.1 函数的默认参数

函数的形参列表中的形参是可以有默认值的

> 语法:`返回值类型 函数名(参数=默认值){}`
>
> ```cpp
> int func(int a,int b,int c = 10){
>     return a+b+c;
> }
> 
> //注意:
> //1.如果某个位置参数有默认值,那么该参数右边的所以参数都必须有默认值
> int func2(int a,int b = 25,int c = 19){
>     return a+b+c;
> }
> //2.如果函数声明有默认值,函数实现时就不能有默认参数
> int func3(int a = 10,int b = 10);
> int func3(int a,int b){
>     return a+b;
> }
> ```
>



### 3.2 函数的占位参数

函数的形参列表里可以用占位参数,调用函数时必须填补该位置

> 语法:`返回值类型 函数名(数据类型){}`
>
> ```cpp
> #include <iostream>
> #include <string>
> 
> using namespace std;
> 
> void func(int a, int) {
> 	cout << "func的占位参数" << endl;
> }
> 
> //占位参数也可以有默认参数
> void func1(int a, int = 10) {
> 	cout << "func1的占位参数" << endl;
> }
> 
> int main()
> {
> 	func(10, 10);//占位参数必须填补
> 	func1(10);
> 
> 	return 0;
> }
> ```



### 3.3 函数重载

#### 1.函数重载概述

> 作用：函数名可以相同，提高复用性
>
> 函数重载（函数签名）满足条件：
>
> + 在同一个作用域下
> + 函数名称相同
> + 函数参数**类型不同** 或者 **个数不同** 或者 **顺序不同**
>
> **注意**：函数的返回值不同不能作为重载条件
>
> ```cpp
> #include <iostream>
> #include <string>
> 
> using namespace std;
> 
> void func() {
> 	cout << "func()的调用" << endl;
> }
> 
> void func(int a) {
> 	cout << "func(int a)的调用" << endl;//个数不同
> }
> 
> void func(double a) {
> 	cout << "func(double a)的调用" << endl;//类型不同
> }
> 
> void func(int a, double b) {
> 	cout << "func(int a,double b)的调用" << endl;
> }
> 
> void func(double a, int b) {
> 	cout << "func(double a,int b)的调用" << endl;//顺序不同
> }
> 
> //返回值不同无法作为重载条件
> //int func(double a, int b) {
> //	cout << "func(double a,int b)的调用" << endl;
> //}
> 
> int main()
> {
> 	func();
> 	func(5);
> 	func(2.5);
> 	func(25, 1.9);
> 	func(2.5, 19);
> 
> 	return 0;
> }
> ```
>

![image-20251025112629176](C:/Users/张翔/AppData/Roaming/Typora/typora-user-images/image-20251025112629176.png)



####  2.注意事项

> + 引用作为重载条件
>
> ```cpp
> #include <iostream>
> #include <string>
> 
> using namespace std;
> 
> void func(int& a) {
> 	cout << "func(int& a)的调用" << endl;
> }
> 
> void func(const int& a) {
> 	cout << "func(const int& a)的调用" << endl;
> }
> 
> int main()
> {
> 	int a = 10;
> 	func(a);//无const调用
> 	func(10);//有const调用
> 
> 	return 0;
> }
> ```
>
> 
>
> + 函数重载碰到默认参数
>
> ```cpp
> #include <iostream>
> #include <string>
> 
> using namespace std;
> 
> void func(int a, int b = 10) {
> 	cout << "func有默认参数的调用" << endl;
> }
> 
> void func(int a) {
> 	cout << "func无默认参数的调用" << endl;
> }
> 
> int main()
> {
> 	func(10);//函数重载遇到默认参数容易出现二义性，尽量避免在重载时使用默认参数
> 
> 	return 0;
> }
> ```
>
> ![image-20251025114522544](C:/Users/张翔/AppData/Roaming/Typora/typora-user-images/image-20251025114522544.png)





## 4、类和对象

c++面向对象的三大特性：==封装，继承，多态==

### 4.1 封装

#### 1.封装的意义

> + 将属性和行为作为一个整体，表现生活中的事物
> + 将属性和行为加以权限控制
>
> 语法： `class 类名{ 访问权限： 属性 / 行为}`
>
> 
>
> 访问权限有三种：
>
> + public 公共权限
> + protected 保护权限
> + private 私有权限
>
> ```cpp
> //public 类内可以访问 类外可以访问
> //protected 类内可以访问 类外不可以访问 子类可以访问父类中的保护内容
> //private 类内可以访问 类外不可以访问
> ```
>



#### 2.struct和class的区别

> + 区别：
> 	+ struct默认权限为 public
> 	+ class默认权限为private



#### 3.成员属性设置为私有

> 1.将所有成员属性设置为私有，可以自己控制读写权限
>
> 2.对于写权限，我们可以检测数据的有效性



### 4.2 对象的初始化和清理

c++中每个对象也会有初始设置 以及 对象销毁前的清理数据的设置

#### 1.构造函数和析构函数

对象的初始化和清理时两个非常重要的安全问题

+ 一个对象或变量没有初始状态，对其使用后果是未知
+ 使用完一个对象或变量，没有及时清理，也会造成一定的安全问题

> 构造函数：在创建对象时为<u>对象的成员属性赋值</u>，由编译器自动调用
>
> 析构函数：在对象销毁前系统自动调用，执行清理工作
>
> 对象的初始化和清理工作是编译器**强制**要我们做的事情，如果程序员不提供构造与析构，编译器会提供，但是是**空实现**

> ==构造函数语法==：`类名 (){}`
>
> 1.没有返回值也没有函数类型
>
> 2.函数名与类名相同
>
> 3.可以有参数，因此可以发生重载
>
> 4.程序在调用对象时会自动调用构造，且只会调动一次，无序手动调用
>
> 5.无法多态
>
> 
>
> ==析构函数语法==：`~类名(){}`
>
> 1.没有返回值也没有函数类型
>
> 2.函数名与类名相同,名称前加上符号 ~
>
> 3.不可以有参数，因此没有重载
>
> 4.程序在对象销毁前会自动调用析构，且只会调动一次，无序手动调用
>
> 5.能够多态

```cpp
#include <iostream>

using namespace std;

class person {
public:
	person() {//构造函数
		cout << "person的构造函数调用" << endl;
	}

	~person() {//析构函数
		cout << "person的析构函数调用" << endl;
	}
};

void test01() {
	person p;
}

int main()
{
	test01();

	system("pause");

	return 0;
}
```



#### 2.构造函数的分类及调用

两种分类方式：

​	按参数：有参构造 / 无参构造

​	按类型：普通构造 / 拷贝构造

> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> class person {
> public:
> 	int age;
> 
> public:
> 	person() {//构造函数
> 		cout << "构造函数的无参调用" << endl;
> 	}
> 
> 	person(int a) {
> 		cout << "构造函数的有参调用" << endl;
> 	}
> 
> 	person(const person& p) {
> 		age = p.age;
> 		cout << "构造函数的拷贝调用" << endl;
> 	}
> 
> 	~person() {//析构函数
> 		cout << "person的析构函数调用" << endl;
> 	}
> };
> 
> void test01() {
> 	person p1;
> 	person p2(25);
> 	person p3(p2);
> }
> 
> int main()
> {
> 	test01();
> 
> 	system("pause");
> 
> 	return 0;
> }
> ```



三种调用方式：括号法 / 显示法 / 隐式转换法

> ```cpp
> void test01() {
> 
> 	//括号法
> 	person p1;//无参构造只有这种形式
> 	person p2(25);
> 	person p3(p2);
>     //注意1：调用无参构造函数不能加括号，否则编译器会认为这是一个函数声明
>     //person p1();
> 
> 	//显示法
> 	person p4 = person(10);
> 	person p5 = person(p5);
>     //注意2：
>     //person(10)单独写就是匿名对象，当前行结束之后立马析构
> 	
> 	//隐式转换法
> 	person p6 = 10;//person p6 = person(10)
> 	person p7 = p6;//person p7 = person(p6)
>     //注意3
>     //不能利用拷贝构造函数初始化匿名对象，编译器会认为是对象声明导致重复定义
> }
> ```



#### 3.拷贝函数调用时机

>  拷贝函数调用时机有三种情况
>
> + 使用一个已经创建完毕的对象来初始化一个新对象
> + 值传递的方式给函数参数传值
> + 以值方式返回局部对象
>
> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> class person {
> public:
> 	int age;
> 
> public:
> 	person() {//构造函数
> 		cout << "构造函数的无参调用" << endl;
> 	}
> 
> 	person(int a) {
> 		cout << "构造函数的有参调用" << endl;
> 	}
> 
> 	person(const person& p) {
> 		age = p.age;
> 		cout << "构造函数的拷贝调用" << endl;
> 	}
> 
> 	~person() {//析构函数
> 		cout << "person的析构函数调用" << endl;
> 	}
> };
> 
> //1.使用一个已经创建完毕的对象来初始化一个新对象，最常见的拷贝
> void test01() {
> 	person p1(10);
> 	person p2 = p1;
> }
> 
> //2.值传递的方式给函数参数传值
> void work(person ps) {
> 	cout << "aaaa" << endl;
> }
> 
> void test02() {
> 	person p;
> 	work(p);//拷贝发生在work函数被调用时，person ps = p;
> 	cout << "aaaaa" << endl;
> }
> 
> //3.以值方式返回局部对象
> person work() {
> 	cout << "aa" << endl;
> 	person p1;
> 	cout << "aaaaa" << endl;
> 	return person(p1);//不能写"return p1",原因下面解释
> }
> 
> void test03() {
> 	person p3 = work();
> 	cout << "aaaa" << endl;
> }
> 
> 
> int main()
> {
> 	test01();
> 	test02();
>    	test03();
>     
> 	system("pause");
> 
> 	return 0;
> }
> ```



#### 补充：命名返回值优化（NRVO）

> 在以值方式返回局部对象以调用拷贝构造函数时，work函数的返回值不能写作”return p1“
>
> 当使用 `return p1` 时，编译器会进行**命名返回值优化（NRVO）**(即下面的触发情况1)，而当使用 `return person(p1)` 时，会调用拷贝构造函数。
>
> **NRVO优化过程：**
>
> 1. 编译器直接在调用者（test01函数）的栈帧中为p3分配内存
> 2. p1实际上就是在p3的内存位置上构造的
> 3. `return p1` 时不需要任何拷贝，直接使用已有的对象
>
> 
>
> 若是”return person(p1)‘’，执行过程如下：
>
> 1. 在work函数中构造p1（无参构造）
> 2. `person(p1)` 创建一个临时对象，调用拷贝构造函数
> 3. 临时对象作为返回值



==**NVRO**的触发条件：==

> ```cpp
> // 情况1：直接返回同一个局部对象
> MyClass create() {
>     MyClass obj;
>     return obj;  // NRVO很可能发生
> }
> 
> // 情况2：通过单一路径返回
> MyClass create(int x) {
>     MyClass obj;
>     if (x > 0) {
>         return obj;  // 所有返回路径返回同一个对象
>     }
>     return obj;      // NRVO可能发生
> }
> ```
>



#### 4.构造函数调用规则

> 默认情况下，c++编译器至少给一个类添加3个函数
>
> 1.默认构造函数（无参，空实现）
>
> 2.默认析构函数（无参，空实现）
>
> 3.默认拷贝构造函数，对属性进行值拷贝
>
> 
>
> 调用规则：
>
> + 如果已经定义了**有参构造函数**，则c++**不再提供**默认无参构造，但还是会提供默认拷贝函数
> + 如果已经定义了拷贝构造函数，c++不再提供其他普通构造函数
>
> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> class person {
> public:
> 	int age;
> 
> public:
> 	person() {//构造函数
> 		cout << "构造函数的无参调用" << endl;
> 	}
> 
> 	person(int a) {
> 		cout << "构造函数的有参调用" << endl;
> 	}
> 
> 	person(const person& p) {
> 		age = p.age;
> 		cout << "构造函数的拷贝调用" << endl;
> 	}
> 
> 	~person() {//析构函数
> 		cout << "person的析构函数调用" << endl;
> 	}
> };
> 
> void test01() {
> 	person p1;
> 	p1.age = 20;
> 	person p2(p1);
> }
> 
> int main()
> {
> 	test01();
> 
> 	system("pause");
> 
> 	return 0;
> }
> ```
>
> 分别注释掉不同的构造函数，分析test01的报错



#### 5.深拷贝与浅拷贝

> 浅拷贝：简单的赋值拷贝操作
>
> 深拷贝：在堆区重新申请空间，进行拷贝操作
>
> 
>
> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> class person {
> public:
> 	int age;
> 	int* height;
> 
> public:
> 	person() {//构造函数
> 		cout << "构造函数的无参调用" << endl;
> 	}
> 
> 	person(int a,int b) {
> 		age = a;
> 		height = new int(b);//为指针成员开辟新内存
> 		cout << "构造函数的有参调用" << endl;
> 	}
> 
> 	person(const person& p) {
> 		age = p.age;
> 		height = new int(*p.height);
> 
> 		cout << "构造函数的拷贝调用" << endl;
> 	}
> 
> 	~person() {//析构函数
> 		cout << "person的析构函数调用" << endl;
> 		if (height != NULL) {
> 			delete height;
> 			height = NULL;
> 		}
> 	}
> };
> 
> void test01() {
> 	person p1(20,160);
> 	
> 	person p2(p1);
> 
> 	cout << "p1.age=" << p1.age << endl;
> 	cout << "p2.age=" << p2.age << endl;
> 	cout << "*p1.height=" << *p1.height << endl;
> 	cout << "*p2.height=" << *p2.height << endl;
> }
> 
> int main()
> {
> 	test01();
> 
> 	system("pause");
> 
> 	return 0;
> }	
> ```



> ```cpp
> ~person() {//析构函数
> 		cout << "person的析构函数调用" << endl;
> 		if (height != NULL) {
> 			delete height;
> 			height = NULL;
> 		}
> 	}//内存的释放类似栈，先进后出，即先创造的p1最后被销毁，后创造的p2先销毁
> ```
>
> 因为有指针成员，所以在对象销毁前需要将指针所指向的内存清理，需要手写析构函数的实现。
>
> 然而如果是使用浅拷贝（即简单赋值）创造的p2，则p1.height与p2.height的<u>地址一样</u>，会导致p2的析构函数将地址所指向的内存清理成功后，p1的析构函数又会再次清理同一块内存，这是**非法操作**。
>
> 此时就需要深拷贝，即上面的拷贝构造：
>
> ```cpp
> person(const person& p) {
> 		age = p.age;
> 		height = new int(*p.height);//重点的一行代码
> 
> 		cout << "构造函数的拷贝调用" << endl;
> 	}
> ```
>
> 因为有`height = new int(*p.height);`在拷贝时，对于指针成员，重新开辟了一块不同于`p.height`的地址，存放`*p.height`的值，此时就不会遇到上述浅拷贝的问题了



#### 6.初始化列表

> 作用：初始化列表语法，用来初始化属性‘
>
> 语法：`构造函数():属性1(值1),属性2(值2)...   //‘=’换为‘()’`
>
> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> class person {
> public:
> 	int m_a;
> 	int m_b;
> 	int m_c;
> 
> 	person(int a, int b, int c) {//传统初始化操作
> 		m_a = a;
> 		m_b = b;
> 		m_c = c;
> 	}
> 	
>     //初始化列表初始化属性
> 	person() :m_a(10), m_b(20), m_c(30) {//固定值
> 
> 	}
> 
> 	person(int a, int b, int c) :m_a(a), m_b(b), m_c(c) {//灵活性更高
> 
> 	}
> };
> 
> void test() {
> 	person p;
> 	cout << p.m_a << endl;
> 	cout << p.m_b << endl;
> 	cout << p.m_c << endl;
> }
> 
> int main()
> {
> 	test();
> 
> 	return 0;
> }
> ```



#### 7.类对象作为类成员

> 类中的成员可以是另一个类的对象，我们称该成员为对象成员
>
> eg：
>
> ```cpp
> class A{};
> class B{
>     A a;
> }
> ```
>
> 主要弄清“在创建B对象时，A与B的构造和析构顺序的先后”

> ```cpp
> #include <iostream>
> #include <string>
> 
> using namespace std;
> 
> class Phone {
> public:
> 
> 	Phone(string name) {
> 
> 		m_PhoneName = name;
> 		cout << "phone的构造函数的调用" << endl;
> 	}
> 
> 	~Phone() {
> 		cout << "phone的析构函数的调用" << endl;
> 	}
> 
> 	string m_PhoneName;
> };
> 
> class Person {
> public:
> 	Person(string name,string pName):m_Name(name),m_Phone(pName) {
> 		cout << "Person的构造" << endl;
> 	}
> 
> 	~Person() {
> 		cout << "Person的析构" << endl;
> 	}
> 
> 	void playGame() {
> 		cout << m_Name << "使用" << m_Phone.m_PhoneName << endl;
> 	}
> 
> 	string m_Name;
> 	Phone m_Phone;
> };
> 
> 
> 
> void test() {
> 	Person p("zyujian", "huawei");
> 	p.playGame();
> }
> 
> int main()
> {
> 	test();
> 
> 	return 0;
> }
> ```
>
> Phone先构造，其次时Person，只有成员完整了，才能组成类
>
> 析构则按照先进后出，Person先析构，然后是Phone



#### 8.静态成员

静态成员就是在成员变量和函数前加上关键字static，成为静态成员

> 静态成员分为：
>
> + 静态成员变量
> 	+ 所有对象共享同一份数据
> 	+ 在编译阶段分配内存
> 	+ **类内声明，类外初始化**
>
> 
>
> + 静态成员函数
> 	+ 所有对象共享同一份数据
> 	+ **静态成员函数只能访问静态成员变量**
>
> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> class Person {
> public:
> 	static int m_A;
> 	int m_C;
> 
> 	static void func() {
> 		m_A = 2519;//静态成员函数只能访问静态成员变量
> 	//	m_C = 1499;//因为无法确认该非静态成员属于哪个对象的，所以访问不了非静态成员
> 		cout << "static void func()的调用" << endl;
> 	}
> 
> private:
> 	static int m_B;
> };
> 
> int Person::m_A = 25;//类内声明，类外初始化
> int Person::m_B = 9;//类内声明，类外初始化
> 
> void test() {
> 	Person p;
> 	cout << p.m_A << endl;
> 
> 	Person p1;
> 	p1.m_A = 19;//所有对象共享同一份数据
> 	cout << p.m_A << endl;
> 
> }
> 
> void test1() {
> 	Person p;
>     //两种访问方式
> 	cout << p.m_A << endl;//通过对象访问
> 
> 	cout << Person::m_A << endl;//通过类名访问
> 
> //	cout << Person::m_B << endl;没有访问私有静态成员的权限
> }
> 
> void test_func() {
> 
> 	Person::func();//通过类名访问
> 
> 	Person p;
> 	p.func();//通过对象访问
> }
> 
> int main()
> {
> 	test();
> 	test1();
> 	test_func();
> 
> 	return 0;
> }
> ```



### 4.3 c++对象模型和指针

#### 1.成员变量和成员函数分开存储

在c++中，类内的成员变量和成员函数分开存储

只有非静态成员变量才属于类的对象上

> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> class Person {
> 
> };
> 
> class People {
> 	int m_A;//非静态成员变量，属于类的对象上
> 
>     void func(){}//非静态成员函数 不属于类对象上
>     
> 	static int m_B;//静态成员变量 不属于类对象上
> 
> 	static void fun2(){}//静态成员函数 不属于类对象上
> 
> };
> 
> void test() {
> 	Person p;
> 	
>     //空对象占用内存空间为1
>     //c++编译器会给每个空对象也分配一个字节的空间，为了区分空对象占内存的位置
> 	cout << "size of p = " << sizeof(p) << endl;
> }
> 
> void test01() {
> 	People l;
> 	cout << "size of l = " << sizeof(l) << endl;//int的4字节
> }
> 
> int main()
> {
> 	test();
> 	test01();
>     
> 	return 0;
> }
> ```



#### 2.this指针的概念

this指针**指向** 被调用的成员函数所属的 **对象**

this指针是隐含在**每一个非静态成员函数内**的一种指针

this指针不需要定义，直接使用即可

> this指针的本质是**指针常量**，即指针的指向是不可以修改的
>
> 在成员函数后面加const，修饰的是this指向的值，让指针指向的值也不可修改
>
> [const修饰成员函数](####4.const修饰成员函数)

> this指针的用途：
>
> + 当形参和成员变量同名时，可用this指针来区分
> + 在类的非静态成员函数中 返回对象本身，可使用`return *this` 
>
> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> class Person {
> public:
> 	int age;
> 	Person(int age) {
> 		this->age = age;//1.解决名称冲突
>       //age = age; 如此写法p.age的值就是乱的
>       //所以最好将成员变量名加“m_”,表示member
> 	}
>    
>   //函数返回值类型一定要是 类的引用
>     Person& addage(const Person& p) {//返回对象本身用this
>         this->age += p.age;
>         return *this;
>     }
> };
> 
> void test01() {
> 	Person p(20);
> 	cout << "p.age=" << p.age << endl;
> }
> 
> void test02() {
> 	Person p1(20);
> 	Person p2(18);
> 
>     //链式编程思想
> 	p2.addage(p1).addage(p1).addage(p1);
> 
> 	cout << "p2.age=" << p2.age << endl;
> }
> 
> int main()
> {
> 	test01();
> 	test02();
> 	return 0;
> }
> ```



#### 3.空指针访问成员函数

空指针也可以定义成员函数，但是要注意有没有用到this指针

如果用到this指针，需要加以判断来保证代码的稳健性

> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> class Person {
> public:
> 	int age;
> 	void showperson() {
> 		cout << "person类" << endl;
> 	 }
> 
> 	void showage() {
> 		if (this == NULL) {//不加判断就会报错
> 			return;
> 		}
> 		cout << "age=" << this->age << endl;
> 	}
> };
> 
> void test01() {
> 	Person* p = NULL;
> 	p->showperson();
> 	p->showage();//空指针调用了含有this指针的函数
> }
> 
> int main()
> {
> 	test01();
> 
> 	return 0;
> }
> ```



#### 4.const修饰成员函数

**常函数：**

+ 成员函数后加const就称为常函数
+ 常函数内**不可以修改**成员属性
+ 成员属性**声明时**加关键字mutable后，在常函数中就可以修改了



**常对象：**

+ 声明对象前加const就称该对象为常对象
+ 常对象只能调用常函数

> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> class Person {
> public:
> 	int m_A;
> 	mutable int m_B;
> 	void showperson() const{//常函数
> 		//this->m_A = 10;
> 		//this = NULL;
> 		m_B = 20;//加关键字mutable后，在常函数中就可以修改了
> 	}
> 
> 	void func() {
> 
> 	}
> };
> 
> void test01() {
> 	Person p;
> 	p.showperson();
> }
> 
> void test02() {
> 	const Person p;//常对象
> 	//p.m_A = 10;
> 	p.m_B = 20;
> 	p.showperson();
> 	//p.func();常对象只能调用常函数
> }
> 
> int main()
> {
> 	test01();
> 	test02();
> 
> 	return 0;
> }
> ```
>



### 4.4 友元

作用：让一个**函数或者类**访问另一个类中**私有成员**

> 关键字：`friend`
>
> 友元的三种实现方式：
>
> + 全局函数做友元
> + 类做友元
> + 成员函数做友元

友元会破坏c++对象的封装性，不安全，但可以提升性能。



#### 1.全局函数做友元

实现：在类的开头加上`friend 全局函数的声明;`

> ```cpp
> #include <iostream>
> #include <string>
> using namespace std;
> 
> class Building {
> 	friend void goodguy(Building& building);//实现方法
> public:
> 	string m_SR;
> 
> 	Building() {
> 		m_SR = "客厅";
> 		m_BR = "卧室";
> 	}
> private:
> 	string m_BR;
> 	
> };
> 
> void goodguy(Building& building) {
> 	cout << building.m_SR << endl;
> 
> 	cout << building.m_BR << endl;
> }
> 
> void test01() {
> 	Building b;
> 	goodguy(b);
> }
> 
> int main()
> {
> 	test01();
> 
> 	return 0;
> }
> ```



#### 2.类做友元

> ```cpp
> #include <iostream>
> #include <string>
> 
> using namespace std;
> 
> class Building;//类的声明
> class goodguy {
> public:
> 	goodguy();
> 	void visit();
> 
> private:
> 	Building* building;
> };
> 
> class Building {
> 	friend class goodguy;//哈哈哈哈哈哈哈
> public:
> 	Building();
> public:
> 	string m_SR;
> private:
> 	string m_BR;
> };
> 
> //类外声明函数成员
> Building::Building() {
> 	m_SR = "客厅";
> 	m_BR = "卧室";
> }
> 
> goodguy::goodguy() {
> 	building = new Building;
> }
> 
> void goodguy::visit() {
> 	cout << building->m_SR << endl;
> 	cout << building->m_BR << endl;
> }
> 
> void test01() {
> 	goodguy gg;
> 	gg.visit();
> }
> 
> int main()
> {
> 	test01();
> 
> 	return 0;
> }
> ```



#### 3.成员函数做友元

> ```cpp
> #include <iostream>
> #include <string>
> 
> using namespace std;
> 
> class Building;//类的声明
> class goodguy {
> public:
> 	goodguy();
> 	void visit();//设置visit可以访问building的私有成员
> 	void visit2();//设置visit2不可以访问building的私有成员
> 
> private:
> 	Building* building;
> };
> 
> class Building {
>     //成员函数做友元
> 	friend void goodguy::visit();
> public:
> 	Building();
> public:
> 	string m_SR;
> private:
> 	string m_BR;
> };
> 
> Building::Building() {
> 	m_SR = "客厅";
> 	m_BR = "卧室";
> }
> 
> goodguy::goodguy() {
> 	building = new Building;
> }
> 
> void goodguy::visit() {
> 	cout << building->m_SR << endl;
> 	cout << building->m_BR << endl;
> }
> 
> void goodguy::visit2() {
> 	cout << building->m_SR << endl;
> //	cout << building->m_BR << endl；不是友元，访问不了building私有成员
> }
> 
> void test01() {
> 	goodguy gg;
> 	gg.visit();
> 	gg.visit2();
> }
> 
> int main()
> {
> 	test01();
> 
> 	return 0;
> }
> ```



### 4.5 运算符重载

概念：对已有的运算符重新进行定义，赋予其另一种功能，以适应不同的数据类型

**不可重载**的运算符：作用域运算符(::),成员访问运算符(.)，指向成员的指针(.*),条件运算符(?:)

#### 1. 加号运算符重载

作用：实现两个自定义数据相加的运算

> 两种方法实现重载：
>
> + 成员函数实现重载
> + 全局函数实现重载
>
> ```cpp
> #include <iostream>
> #include <string>
> 
> using namespace std;
> 
> class Person {
> public:
> 	int m_A;
> 	int m_B;
> 
> public:
> 	//成员函数重载+号
> 	Person operator+(Person& p) {
> 		Person temp;
> 		temp.m_A = this->m_A + p.m_A;
> 		temp.m_B = this->m_B + p.m_B;
> 		return temp;
> 	}
> };
> 
> void test01() {
> 	Person p1;
> 	p1.m_A = 25;
> 	p1.m_B = 19;
> 	Person p2;
> 	p2.m_A = 19, p2.m_B = 25;
> 	//两种赋值方法
> 	Person p3 = p1.operator+(p2);
> 
> 	cout << p3.m_A << endl;
> 	cout << p3.m_B << endl;
> 	//
> 	p3 = p1 + p2;
> 	cout << p3.m_A << endl;
> 	cout << p3.m_B << endl;
> }
> 
> int main()
> {
> 	test01();
> 
> 
> 	return 0;
> }
> ```
>
> ```cpp
> //全局函数重载+号
> Person operator+(Person &p1,Person &p2) {
> 	Person temp;
> 	temp.m_A = p1.m_A + p2.m_A;
> 	temp.m_B = p1.m_B + p2.m_B;
> 	return temp;
> }
> 
> void test01() {
> 	Person p1;
> 	p1.m_A = 25;
> 	p1.m_B = 19;
> 	Person p2;
> 	p2.m_A = 19, p2.m_B = 25;
> 	//全局函数重载本质调用
> 	Person p3 = operator+(p1,p2);
> 
> 	cout << p3.m_A << endl;
> 	cout << p3.m_B << endl;
> 
> 	p3 = p1 + p2;
> 	cout << p3.m_A << endl;
> 	cout << p3.m_B << endl;
> }
> ```
>
> 注意：1.对于内置的数据类型的表达式的运算符是不可改变的(+，-，*，/ 这些)
>
> 2.不要滥用运算符重载，即operator+的函数内容却实现两数据类型相减



#### 2.左移运算符重载

作用：可以输出自定义数据类型

类外实现

> ```cpp
> #include <iostream>
> #include <string>
> 
> using namespace std;
> 
> class Person {
> 	friend ostream& operator<<(ostream& manbo, Person p);
> private:
> 	int m_A;
> 	int m_B;
> public:
> 	Person(int a, int b) {
> 		this->m_A = a;
> 		this->m_B = b;
> 	}
> };
> 
> //一般用全局函数实现，能输出cout在前
> //返回引用可以实现链式编程，即可以一直<<输出，同时也是ostream对象只能在程序中出现一次
> //若是void，则只能使用一次<<
> ostream& operator<<(ostream& manbo, Person p) {
> 	manbo << p.m_A << p.m_B << endl;
> 	return manbo;
> }
> 
> void test01() {
> 	Person p(25,19);
> 
> 	cout << p << "hello world" << endl;
> }
> 
> int main()
> {
> 	test01();
> 
> 	return 0;
> }
> ```



#### 3.递增运算符重载

作用：通过查找递增运算符，实现自己的整型数据

类内实现：

> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> class Myinteger {
> 	friend ostream& operator<<(ostream& cout, Myinteger myint);
> private:
> 	int Myint;
> public:
> 	Myinteger() {
> 		Myint = 0;
> 	}
> 	//前置++
>  //返回类的引用,对同一个变量操作
> 	Myinteger& operator++() {
>      //先++
> 		this->Myint++;
>      //再返回
> 		return *this;
> 	}
>  
> 	//后置++
>  //返回类,因为temp是临时变量,返回其引用是违法操作
>     //int为占位参数，用来区分前加和后加
> 	Myinteger operator++(int) {
> 		//先记录当前的数,然后让本身的值+1,达到先返回原本的值再++的目的
> 		Myinteger temp = *this;
> 		//再++
> 		Myint++;
> 		//返回记录值
> 		return temp;
> 	}
> };
> 
> ostream& operator<<(ostream& cout, Myinteger myint) {
> 	cout << myint.Myint << endl;
> 	return cout;
> }
> 
> void test01() {
> 	Myinteger myint;
> 
> 	cout << (++myint)++ << endl;
> 	cout << myint << endl;
> }
> 
> void test02() {
> 	Myinteger myint;
> 	cout << myint++ << endl;
> 	cout << myint << endl;
> }
> 
> int main()
> {
> 	test01();
> 	test02();
> 	
> 	return 0;
> }
> ```
>

> 后置递增无法做到链式，因为其返回的是值。
>
> ```cpp
> Myinteger myint;//myint初始值为0
> ++(myint++);
> cout << myint <<endl;
> ```
>
> 上述代码myint的输出为1，以下为拆解步骤
>
> ```cpp
> ++(myint++);
> 先执行括号里的表达式，myint的值变为1，temp值为0。
> 然后返回temp，表达式变为++(temp);
> 此时对temp进行++，temp变为1，且临时变量temp生命周期结束。
> 因为myint已经值为1了，所以表达式的结果就是1.
> ```
>
> 

类外实现：

需要在类内先声明，函数实现不变

> ```cpp
> class MyValue
> {
> 	friend ostream& operator<<(ostream& out, MyValue v);
> private:
> 	int m_Value;
> public:
> 	MyValue()
> 	{
> 		this->m_Value = 0;
> 	}
> 	MyValue(int value)
> 	{
> 		this->m_Value = value;
> 	}
> 	//类内先声明
> 	MyValue& operator++();//前置++
> 	MyValue operator++(int);//后置++
> };
> 
> //递增运算符的重载的类外实现
> MyValue& MyValue::operator++()
> {
> 	this->m_Value++;
> 	return *this;
> }
> 
> MyValue MyValue::operator++(int)
> {
> 	MyValue temp;
> 	temp.m_Value = this->m_Value;
> 	this->m_Value++;
> 	return temp;
> }
> ```
>
> 

#### 4.赋值运算符重载

重点：防止自赋值，以及delete原内存

> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> class Person {
> 	friend void test01();
> private:
> 	int* m_Age;
> 
> public:
> 	Person(int age) {
> 		m_Age = new int(age);
> 	}
> 	~Person() {
> 		if (m_Age != NULL) {
> 			delete m_Age;
> 			m_Age = NULL;
> 		}
> 	}
> 	Person& operator=(Person &p) {
> 		if (m_Age != NULL) {
> 			delete m_Age;
> 			m_Age = NULL;
> 		}
> 		m_Age = new int(*p.m_Age);
> 		return *this;
> 	}
> };
> 
> void test01() {
> 	Person p1(25);
> 	Person p2(19);
> 	p1 = p2;
> 	cout << "p1.age=" << *p1.m_Age << endl;
> 	cout << "p2.age=" << *p2.m_Age << endl;
> }
> 
> int main()
> {
> 	test01();
> 
> 	return 0;
> }
> ```
>
> 



#### 5.关系运算符重载

> 对`== > < >= <=`等关系运算符重新定义
>
> ```cpp
> #include <iostream>
> #include <string>
> using namespace std;
> 
> class Person {
> public:
> 	int m_Age;
> 	string m_Name;
> 
> 	Person(int age,string name):m_Age(age),m_Name(name) {
> 
> 	}
> 	
>     //重载相等
>     //注意返回的是布尔值
> 	bool operator==(Person& p) {
> 		if (this->m_Age == p.m_Age && this->m_Name == p.m_Name) {
> 			return true;
> 		}
> 		return false;
> 	}
> 
>     //重载不相等
> 	bool operator!=(Person& p) {
> 		if (this->m_Age == p.m_Age && this->m_Name == p.m_Name) {
> 			return false;
> 		}
> 		return true;
> 	}
> };
> 
> void test01() {
> 	Person p1(20, "zyujian");
> 	Person p2(20, "zyujian");
> 
> 	if (p1 == p2) {
> 		cout << "p1和p2相等" << endl;
> 	}
> }
> 
> void test02() {
> 	Person p1(18, "zx");
> 	Person p2(18, "zs");
> 
> 	if (p1 != p2) {
> 		cout << "p1和p2不相等" << endl;
> 	}
> }
> 
> int main()
> {
> 	test01();
> 	test02();
> 
> 	return 0;
> }
> ```
>
> 



#### 6.函数调用运算符重载

+ 仿函数没有固定写法，非常灵活
+ 由于使用起来非常类似于函数调用，因此称为仿函数

> ```cpp
> #include <iostream>
> #include <string>
> 
> using namespace std;
> 
> class MyPrint {
> public:
> 	//重载函数调用运算符()
> 	void operator()(string test) {
> 		cout << test << endl;
> 	}
> };
> 
> class MyAdd {
> public:
> 	//仿函数没有固定写法，非常灵活
> 	int operator()(int num1,int num2) {
> 		return num1 + num2;
> 	}
> };
> 
> void test01() {
> 	//由于使用起来非常类似于函数调用，因此称为仿函数
> 	MyPrint myprint;
> 	myprint("hello world");
> }
> 
> void test02() {
> 	MyAdd myadd;
> 	int ret = myadd(25, 19);
> 	cout << ret << endl;
> 
> 	ret = MyAdd()(100, 200);//匿名对象调用
> 	cout << ret << endl;
> }
> 
> int main()
> {
> 	test01();
> 	test02();
> 
> 
> 	return 0;
> }
> ```





### 4.6 继承

作用：减少重复代码

语法：`class 子类：继承方式 父类`

> 子类 也称为 派生类
>
> 父类 也称为 基类



#### 1.继承方式

> 继承方式一共有三种：
>
> + 公共继承
> + 保护继承
> + 私有继承
>
> 继承后在子类中父类对象的权限如下
>
> ![屏幕截图 2025-11-04 161339](C:/Users/张翔/Pictures/Screenshots/屏幕截图 2025-11-04 161339.png)
>
> 



#### 2.继承中的对象模型

+ 父类中所有==非静态成员==属性都会被子类继承
+ 父类中的私有成员属性被编译器隐藏了，因此访问不到，但仍然被继承



#### 3.构造和析构顺序

> ```cpp
> #include <iostream>
> #include <string>
> 
> using namespace std;
> 
> class Base {
> public:
> 	Base() {
> 		cout << "Base的构造函数" << endl;
> 	}
> 	~Base() {
> 		cout << "Base的析构函数" << endl;
> 	}
> };
> 
> class Son :public Base {
> public:
> 	Son() {
> 		cout << "Son的构造函数" << endl;
> 	}
> 	~Son() {
> 		cout << "Son的析构函数" << endl;
> 	}
> };
> 
> void test01() {
> //	Base b;
> 
> 	Son s;
> 
> }
> 
> int main()
> {
> 	test01();
> 
> 	return 0;
> }
> ```
>
> 继承中，先调用父类构造函数，再调用子类构造函数，析构反之



#### 4.继承同名成员处理方式

> + 访问子类同名成员，直接访问
> + 访问父类同名成员，需要加作用域
>
> ```cpp
> #include <iostream>
> #include <string>
> 
> using namespace std;
> 
> class Base {
> public:
> 	int m_A;
> 
> 	Base() {
> 		m_A = 25;
> 	}
> 
> 	void func() {
> 		cout << "Base-func的调用" << endl;
> 	}
> 
> 	void func(int a) {
> 		cout << "Base-func(int a)的调用" << endl;
> 	}
> };
> 
> class Son :public Base {
> public:
> 	int m_A;
> 
> 	Son() {
> 		m_A = 19;
> 	}
> 
> 	void func() {
> 		cout << "Son-func的调用" << endl;
> 	}
> };
> 
> void test01() {
> 	Son s;
> 	cout << s.m_A << endl;//直接调用 调用的是子类中的同名成员
> 	cout << s.Base::m_A << endl;//加作用域才能调用父类中的成员
> }
> 
> void test02() {
> 	Son s;
> //	s.func(100);
> /*如果子类中出现和父类同名的成员函数，子类的同名成员会隐藏
> 父类中所有页面成员函数，如果想要访问父类的同名成员函数，需
> 要加作用域*/
> 	s.Base::func();
> 	s.Base::func(100);
> }
> 
> int main()
> {
> 	test01();
> 	test02();
> 
> 	return 0;
> }
> ```



#### 5.继承同名静态成员处理方式

同上

> ```cpp
> #include <iostream>
> #include <string>
> 
> using namespace std;
> 
> class Base {
> public:
> 	static int m_A;
> 
> 	static void func() {
> 		cout << "Base-func()的调用" << endl;
> 	}
> 
> 	static void func(int a) {
> 		cout << "Base-func(int a)的调用" << endl;
> 	}
> };
> 
> class Son:public Base {
> public:
> 	static int m_A;
> 
> 	static void func() {
> 		cout << "Son-func()的调用" << endl;
> 	}
> };
> 
> int Base::m_A = 25;
> int Son::m_A = 19;
> 
> void test01() {
> 	//类名
> 	cout << Son::m_A << endl;
> 	cout << Son::Base::m_A << endl << endl;
> 	//第一个::代表通过类名访问，第二个::代表访问父类作用域下
> 
> 	//对象
> 	Son s;
> 	cout << s.m_A << endl;
> 	cout << s.Base::m_A << endl;
> }
> 
> void test02() {
> 	//类名
> 	Son::func();
> 	Son::Base::func();
> 	Son::Base::func(200);
> 
> 	cout << endl;
> 
> 	//对象
> 	Son s;
> 	s.func();
> 	s.Base::func();
> 	s.Base::func(100);
> }
> 
> int main()
> {
> 	test01();
> 	test02();
> 
> 	return 0;
> }
> ```



#### 6.多继承语法

c++允许一个类继承多个类

语法:`class 子类:继承方式 父类1, 继承方式 父类2...`

多继承可能会引发父类中有同名成员出现，需要加作用域区分

实际开发中不建议用多继承

> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> class Base1 {
> public:
> 	int m_A;
> 
> 	Base1() {
> 		m_A = 25;
> 	}
> };
> 
> class Base2 {
> public:
> 	int m_A;
> 
> 	Base2() {
> 		m_A = 19;
> 	}
> };
> 
> class Son :public Base1, public Base2 {
> public:
> 	int m_B;
> 	int m_C;
> 
> 	Son() {
> 		m_B = 100;
> 		m_C = 200;
> 	}
> };
> 
> void test() {
> 	Son s;
> 	cout << "sizeof(Son) = " << sizeof(s) << endl;
> }
> 
> void test01() {
> 	Son s;
>     //标明作用域
> 	cout << s.Base1::m_A << endl;
> 	cout << s.Base2::m_A << endl;
> 	cout << s.m_B << endl;
> 	cout << s.m_C << endl;
> }
> 
> int main()
> {
> 	test();
> 	test01();
> 
> 	return 0;
> }
> ```



#### 7.菱形继承

> 概念：两个子类继承同一个父类，又有某个类同时继承这两个子类
>
> 语法：`class 类名:virtual 继承方式 父类{}`
>
> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> class Animal{//无虚基类表指针
> public:
> 	int m_Age;
> };
> 
> //利用虚继承 解决菱形继承问题
> //继承前 加上关键字 virtual 变为虚继承
> //Animal类称为 虚基类
> class Sheep:virtual public Animal{};//有虚基类表指针
> 
> class Tuo:virtual public Animal{};//有虚基类表指针
> 
> class SheepTuo:public Sheep,public Tuo{};//有虚基类表指针
> 
> void test01() {
> 	SheepTuo st;
> 	st.Sheep::m_Age = 25;
> 	st.Tuo::m_Age = 19;
> 	cout << st.Sheep::m_Age << endl;
> 	cout << st.Tuo::m_Age << endl;
> 	/*对于SheepTuo类只需要一份Age数据就可以，
> 	菱形继承导致数据有两份，资源浪费*/
> 	st.m_Age = 100;
> 	cout << st.m_Age << endl;
> }
> 
> int main()
> {
> 	test01();
> 
> 	return 0;
> }
> ```
>



> - **vbptr**：隐藏指针，指向虚基类表。**直接使用虚继承/间接继承设计虚继承的类，编译器就会自动添加虚基类表指针**
> - **vbtable**：存储偏移量的表格，解决共享基类定位问题。通过计算偏移量，可以找到真正储存数据的类
> - **目的**：实现虚继承，解决菱形继承的数据冗余
> - **开销**：每个虚继承的类增加一个指针大小，多一次间接访问



### 4.7 多态

#### 1.多态的基本概念

**多态是C++面向对象三大特性之一**

> 多态分为两类：
>
> + 静态多态：==函数重载== 和 ==运算符重载== 属于静态多态，复用函数名
> + 动态多态：派生类 和 ==虚函数== 实现运行时多态
>
> 两者区别：
>
> + 静态多态：函数地址**早**绑定——编译阶段确定函数地址
> + 动态多态：函数地址**晚**绑定——运行阶段确定函数地址
> + 多态的"动态"体现在**绑定时机**，而不是函数地址的变化
>
> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> class Animal {
> public:
>     //为了不让地址早绑定，加virtual关键字变成虚函数，编译器在编译时就不能确定函数调用了，变为动态多态
> 	virtual void Speak(){
> 		cout << "动物在说话" << endl;
> 	}
> };
> 
> class Cat :public Animal {
> public:
>     //函数重写：函数返回值类型，函数名，参数列表都完全相同，只有函数实现不同
> 	void Speak() {
> 		cout << "小猫在说话" << endl;
> 	}
> };
> 
> class Dog :public Animal {
> public:
> 	void Speak() {
> 		cout << "小狗在说话" << endl;
> 	}
> };
> 			//动态多态的使用：父类引用
> void doSpeak(Animal& animal) {//Animal & animal = cat;隐式转换
> 	animal.Speak();
> }
> 
> void test01() {
> 	Cat cat;
> 	doSpeak(cat);
> 	Dog dog;
> 	doSpeak(dog);
> }
> 
> int main()
> {
> 	test01();
>     
> 	return 0;
> }
> ```
>

> **动态多态满足条件：**
>
> **1.有继承关系**
>
> **2.子类重写父类的虚函数**
>
> 
>
> 动态多态的使用：
>
> 通过父类的指针或者引用，执行子类对象



#### 2.多态的原理剖析

C++中，**子类的对象**可以被赋值给父类的==**指针对象**==，这称为向上转型。这是因为子类对象包含了所有的父类成员，因此可以被视为父类的对象



==**virtual关键字**==(虚函数的virtual，而非虚继承的virtual)：给类中成员加virtual后，编译器自动给该类添加vptr成员，并且给生成虚函数表

**==虚函数表==**：储存该类中虚函数的地址

**==虚函数表指针==**：作为类成员，无访问控制，完全隐藏，指向对应类的虚函数表

==**重写**==：子类和父类使用同一个函数签名，仅修改父类虚函数的实现，重写了的虚函数在子类的虚函数表中有新地址，未重写的虚函数仍然是父类虚函数表的地址



**在单继承中，同一个对象内部只有一个vptr，这个vptr在对象==完全构造==后指向==最终派生类==的虚函数表**



> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> class Base
> {
> public:
> 	virtual void func() {
> 		cout << "Base-func的调用" << endl;
> 	}
> 
> 	void func2()
> 	{
> 		cout << "Base-func2的调用" << endl;
> 	}
> };
> 
> class Son :public Base
> {
> public:
> 	void func() override
> 	{
> 		cout << "Son-func的调用" << endl;
> 	}
> };
> 
> int main()
> {
>     //同一个对象，继承链中的所有类共用一个vptr指针，指向当前类对应的虚函数表
> 	Base* b = new Base;
> 	b->func();
> 	b->func2();
> 	
>     //对象变化了，不是同一个tptr指针了
> 	b = new Son;
> 	b->func();
>     //子类未重写func2，func2仍然是父类的内容
> 	b->func2();
> 
> 	return 0;
> }
> ```
>
> 





#### 多态案例一——计算器类

案例描述：

分别利用普通写法和多态技术，实现两个操作数进行运算的计算器类



> ```cpp
> #include <iostream>
> #include <string>
> 
> using namespace std;
> 
> //普通写法
> class Calculator {
> private:
> 	int num1;
> 	int num2;
> public:
> 	int getRusult(char oper) {
> 		if (oper == '+') {
> 			return num1 + num2;
> 		}
> 		else if (oper == '-') {
> 			return num1 - num2;
> 		}
> 		else if (oper == '*') {
> 			return num1 * num2;
> 		}
> 	}
> 
> 	Calculator(int a, int b) {
> 		num1 = a;
> 		num2 = b;
> 	}
>     //如果想要扩展新的功能，需要修改源码
>     //真实开发中提倡 开闭原则：对扩展进行开发，对修改进行关闭
> };
> 
> void test01() {
> 	Calculator calculator(25, 19);
> 	cout << calculator.getRusult('+') << endl;
> 	cout << calculator.getRusult('-') << endl;
> 	cout << calculator.getRusult('*') << endl;
> }
> 
> //多态写法
> class AbstractCal {
> public:
> 	int num1;
> 	int num2;
> 
> 	virtual int getResult() {
> 		return 0;
> 	}
> };
> 
> class Add :public AbstractCal {
> public:
> 	int getResult() {
> 		return num1 + num2;
> 	}
> };
> 
> class Sub :public AbstractCal {
> public:
> 	int getResult() {
> 		return num1 - num2;
> 	}
> };
> 
> class Mulp :public AbstractCal {
> public:
> 	int getResult() {
> 		return num1 * num2;
> 	}
> };
> 
> void test02() {
> 	//动态多态的使用：父类的指针或者引用指向子类对象
> 	AbstractCal* a = new Add;
> 	a->num1 = 25;
> 	a->num2 = 19;
> 	int ret = a->getResult();
> 	cout << ret << endl;
> 	//创建的指针记得销毁
> 	delete a;
> 
> 	AbstractCal* m = new Mulp;
> 	m->num1 = 10;
> 	m->num2 = 5;
> 	cout << m->getResult() << endl;
> 	delete m;
> }
> 
> 
> int main()
> {
> 	test01();
> 	cout << endl;
> 	test02();
> 	return 0;
> }
> ```
>
> 多态的优点：
>
> + 代码组织结构清晰
> + 可读性强
> + 利于前后期的扩展和维护



#### 3.纯虚函数和抽象类

多态中，父类中虚函数的实现是没有意义的，主要是调用子类重写的内容，因此可以将虚函数改为**纯虚函数**

> 纯虚函数语法：`virtual 返回值类型 函数名 (参数列表) = 0;`
>
> 当类中有纯虚函数，这个类也成为==抽象类==
>
> 抽象类特点：
>
> + 无法实例化对象
> + 子类必须重写抽象类中的纯虚函数，否则也属于抽象类
>
> 
>
> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> class Base {
> public:
> 	//纯虚函数
> 	virtual void func() = 0;
> };
> 
> class Son :public Base {
> public:
> 	void func(){
> 		cout << "func的调用" << endl;
> 	}
> };
> 
> void test01() {
> //	Base b;
> //	new Base;无法实例化对象
> 
> 	Base* b = new Son;
> 	b->func();
> }
> 
> int main()
> {
> 	test01();
> 
> 	return 0;
> }
> ```
>



#### 多态案例二——制作饮品

> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> class AbstractDrinking {
> public:
> 	//煮水
> 	virtual void Boil() = 0;
> 	//冲泡
> 	virtual void Brew() = 0;
> 	//倒入杯中
> 	virtual void PourintoCup() = 0;
> 	//加入辅料
> 	virtual void Put() = 0;
> 
> 	void doDrinking() {
> 		Boil();
> 		Brew();
> 		PourintoCup();
> 		Put();
> 	}
> };
> 
> class Coffee:public AbstractDrinking
> {
> public:
> 	void Boil() override{
> 		cout << "煮水" << endl;
> 	}
> 	void Brew() override {
> 		cout << "冲泡咖啡" << endl;
> 	}
> 	void PourintoCup() override {
> 		cout << "倒入杯子" << endl;
> 	}
> 	void Put() override {
> 		cout << "加入牛奶" << endl;
> 	}
> };
> 
> class Tea:public AbstractDrinking
> {
> public:
> 	virtual void Boil()
> 	{
> 		cout << "煮水" << endl;
> 	}
> 	
> 	virtual void Brew()
> 	{
> 		cout << "冲泡茶叶" << endl;
> 	}
> 	
> 	virtual void PourintoCup() {
> 		cout << "倒入茶杯" << endl;
> 	}
> 	
> 	virtual void Put() {
> 		cout << "加入枸杞" << endl;
> 	}
> };
> 
> void doWork(AbstractDrinking* a) {
> 	a->doDrinking();
> 	delete a;
> }
> 
> void test01() {
> 	doWork(new Coffee);
> 	doWork(new Tea);
> }
> 
> int main()
> {
> 	test01();
> 
> 	return 0;
> }
> ```



#### 4.虚析构和纯虚析构

多态使用时，如果子类中有属性开辟到堆区，那么父类指针在释放时无法调用子类的析构代码

解决方式：将父类中的析构函数改为**虚析构**或者**纯虚析构**



虚析构和纯虚析构共性：

+ 可以使得通过父类指针释放子类对象
+ 都需要**具体的函数实现**

区别：

+ 如果是纯虚析构，该类属于抽象类，无法实例化对象



纯虚析构需要在类外实现：

`作用域::~类名(){}`

> ```cpp
> #include <iostream>
> #include <string>
> using namespace std;
> 
> class Animal
> {
> public:
> 	virtual void Speak() = 0;
> 
> 	Animal() {
> 		cout << "Animal构造函数的调用" << endl;
> 	}
> 
> 	/*virtual ~Animal() {
> 		cout << "Animal析构函数的调用" << endl;
> 	}*/
> 	virtual ~Animal() = 0;//纯虚析构
> 	
> };
> 
> //纯虚析构也需要函数实现
> Animal::~Animal()
> {
> 	cout << "Animal 纯虚 析构函数调用" << endl;
> }
> 
> class Cat:public Animal
> {
> public:
> 	Cat(string name) {
> 		cout << "Cat构造函数的调用" << endl;
> 		m_Name = new string(name);
> 	}
> 
> 	void Speak() override {
> 		cout << *m_Name << "小猫在说话" << endl;
> 	}
> 
> 	~Cat() {//重写父类析构函数
> 		cout << "Cat析构函数的调用" << endl;
> 		if (m_Name != nullptr) {
> 			delete m_Name;
> 			m_Name = nullptr;
> 		}
> 	}
> 
> 	string* m_Name;
> };
> 
> void doSpeak(Animal& animal) {
> 	animal.Speak();
> }
> 
> void test01()
> {
> 	Animal* a = new Cat("Tom");
> 	a->Speak();
>  //通过父类指针去释放会导致子类对象可能清理不干净，造成内存泄漏
>  //虚析构函数能够使得通过父类指针释放子类对象
> 	delete a;//堆对象需要手动释放内存
> }
> 
> void test02() {
> 	Cat c("Jack");
> 	doSpeak(c);//栈对象系统自动释放
> }
> 
> int main()
> {
> 	test01();
> 	cout << endl;
> //	test02();
> 	system("pause");
> 	return 0;
> }
> ```

> + 子类中没有堆区数据，可以不写为虚析构或纯虚析构
> + 拥有纯虚析构函数的类也属于抽象类



#### 多态案例三——组装计算机

```cpp
#include <iostream>

using namespace std;

class CPU
{
public:
	virtual void calculate() = 0;
};

class VideoCard
{
public:
	virtual void display() = 0;
};

class Memory
{
public:
	virtual void storage() = 0;
};

class Computer 
{
private:
	CPU* cpu;
	VideoCard* vc;
	Memory* mm;

public:
	Computer(CPU* cpu, VideoCard* vc, Memory* mm) {
		this->cpu = cpu;
		this->vc = vc;
		this->mm = mm;
	}

	~Computer() {
		if (this->cpu != nullptr) {
			delete this->cpu;
			this->cpu = nullptr;
			cout << "cpu析构成功" << endl;
		}
		if (this->vc != nullptr) {
			delete this->vc;
			this->vc = nullptr;
			cout << "vc析构成功" << endl;
		}
		if (this->mm != nullptr) {
			delete this->mm;
			this->mm = nullptr;
			cout << "mm析构成功" << endl;
		}
	}

	void doWork() {
		cpu->calculate();
		vc->display();
		mm->storage();
	}
};

class Intel_CPU :public CPU
{
	void calculate() override {
		cout << "这是Intel的CPU" << endl;
	}
};

class Intel_VideoCard :public VideoCard
{
	void display() override {
		cout << "这是Intel的显卡" << endl;
	}
};

class Intel_Memory :public Memory
{
	void storage() override {
		cout << "这是Intel的内存条" << endl;
	}
};

class lenovo_CPU :public CPU
{
	void calculate() override {
		cout << "这是lenovo的CPU" << endl;
	}
};

class lenovo_VideoCard :public VideoCard
{
	void display() override {
		cout << "这是lenovo的显卡" << endl;
	}
};

class lenovo_Memory :public Memory
{
	void storage() override {
		cout << "这是lenovo的内存条" << endl;
	}
};


int main()
{

	CPU* l_cpu = new lenovo_CPU;
	VideoCard* l_vc = new lenovo_VideoCard;
	Memory* l_mm = new lenovo_Memory;

	CPU* i_cpu = new Intel_CPU;
	VideoCard* i_vc = new Intel_VideoCard;
	Memory* i_mm = new Intel_Memory;
	
    //使用指针，可能好些
	Computer* c1 = new Computer(l_cpu, i_vc, i_mm);
	c1->doWork();
	delete c1;

	cout << endl;
	//可以直接在构造函数里开辟内存
	Computer* c2 = new Computer(new Intel_CPU, new Intel_VideoCard, new lenovo_Memory);
	c2->doWork();
	delete c2;

	return 0;
}
```



#### 补充

**子类不会自动继承基类的构造函数**。在C++中，构造函数不是普通成员函数，它们不会被继承。每个类都必须定义自己的构造函数。



## 5、文件操作

程序运行时产生的数据都属于临时数据，随着程序的结束就会被释放

通过**文件**可以将**数据持久化**

C++中对文件操作需要包含头文件==`<fstream>`==



> 文件类型分为两者：
>
> + 文本文件：文件以文本的**ASCII码**形式存储在计算机中
> + 二进制文件：文件以文本的**二进制**形式存储在计算机中，用户一般不能直接读懂



> 文件操作的三大类：
>
> 1.ofstream:写操作
>
> 2.ifstream:读操作
>
> 3.fstream:读写操作 



### 5.1 文本文件

#### 1 写文件

写文件步骤如下：

> 1.包含头文件：#include <fstream>
>
> 2.创建流对象：==**o**==fstream ofs;
>
> 3.打开文件：ofs.open(“文件路径”,打开方式);
>
> 4.写数据：ofs<<“写入的数据”;
>
> 5.关闭文件：ofs.close();



文件打开方式：

| 打开方式    | 解释                       |
| ----------- | :------------------------- |
| ios::in     | 为读文件而打开文件         |
| ios::out    | 为写文件而打开文件         |
| ios::ate    | 初始位置：文件尾           |
| ios::app    | 追加方式写文件             |
| ios::trunc  | 如果文件存在先删除，再创建 |
| ios::binary | 二进制方式打开文件         |

**注意**：文件打开方式可以配合使用，利用`|`操作符

eg:用二进制方式写文件 `ios::binary | ios::out`，binary和out/in的顺序无所谓



> ```cpp
> //写文件
> #include <iostream>
> #include <string>
> //包含头文件
> #include <fstream>
> 
> using namespace std;
> 
> int main()
> {
>     //创建流对象
> 	ofstream ofs;
> 
>     //打开文件：ofs("文件路径","打开方式")
>     //文件路径分相对路径和绝对路径，若只写文件名，则文件创建在和项目同一个文件夹中
> 	ofs.open("text.txt", ios::out);
> 	
>     //输入数据
> 	ofs << "姓名：张三" << endl;
> 	ofs << "性别：男" << endl;
> 	ofs << "年龄：20" << endl;
> 	
>     //关闭文件
> 	ofs.close();
> 
> 	return 0;
> }
> ```



#### 2 读文件

步骤与写文件相似，第四步读取方式有四种



> 1.包含头文件：#include <fstream>
>
> 2.创建流对象：==**i**==fstream ifs;
>
> 3.打开文件：ifs.open(“文件路径”,打开方式);
>
> 4.读数据：四种方式
>
> 5.关闭文件：ifs.close();

> ```cpp
> #include <iostream>
> #include <string>
> //1
> #include <fstream>
> 
> using namespace std;
> 
> int main()
> {
>     //2
> 	ifstream ifs;
> 
>     //3
> 	ifs.open("text.txt", ios::in);
> 
> 	if (!ifs.is_open()) {
> 		cout << "文件打开失败" << endl;
> 	}
> 
>     //4.读数据
>     //第一种
> 	char buf[1024] = { 0 };
> 	while (ifs >> buf) {
> 		cout << buf << endl;
> 	}
>     
>     //第二种
>     char buf[1024] = { 0 };
>     while (ifs.getline(buf, sizeof(buf))) {
>         cout << buf << endl;
>     }
> 
>     //第三种
>     string buf;
>     while (getline(ifs, buf)) {
>         cout << buf << endl;
>     }
>     
>     //第四种
>     char c;
>     while ((c = ifs.get()) != EOF)
>     {
>         cout << c;
>     }    
>     
>     //5
> 	ifs.close();
> 
> 	return 0;
> }
> ```



### 5.2 二进制文件

以二进制文件对文件进行读写操作

打开方式要指定为==ios::binary==



#### 1.写文件

> 二进制方式写文件主要利用流对象调用成员函数**write**
>
> 函数原型:`ostream& write(const char* buffer,int len);`
>
> 参数解释：字符指针buffer指向内存中的一段储存空间。len是读写的字节数

> ```cpp
> #include <iostream>
> #include <fstream>
> 
> using namespace std;
> 
> class Person
> {
> public:
> 	char m_Name[64];//string里包含指针，在转为二进制文件时可能报错，所以使用char类型
> 	int m_Age;
> };
> 
> void test01()
> {
> 	Person p = { "zyujian",20 };
> 
> 	ofstream ofs;
> 
> 	ofs.open("C:\\Users\\张翔\\Desktop\\新建文件夹\\binary.txt", ios::binary | ios::out);
> 	
>     		  //强制类型转换
> 	ofs.write((const char*)&p, sizeof(Person));
> 
> 	ofs.close();
> }
> 
> int main()
> {
> 	test01();
> 
> 	return 0;
> }
> ```
>
> 

+ 文件输出流对象，可以通过write函数，以二进制方式写数据



#### 2.读文件

> 二进制方式读文件主要利用流对象调用成员函数**read**
>
> 函数原型：`istream& read(char* buffer,int len)`

> ```cpp
> #include <iostream>
> #include <fstream>
> 
> using namespace std;
> 
> class Person
> {
> public:
> 	char m_Name[64];//string里包含指针，在转为二进制文件时可能报错，所以使用char类型
> 	int m_Age;
> };
> 
> void test02()
> {
>     //创建对象来储存文件内容
> 	Person p;
>     
> 	//第二种打开文件的方式，在创建对象的时候直接打开
> 	ifstream ifs("C:\\Users\\张翔\\Desktop\\新建文件夹\\binary.txt", ios::binary | ios::in);
> 
> 	if (!ifs.is_open()) {
> 		cout << "文件打开失败" << endl;
> 		return;
> 	}
> 
> 	ifs.read((char*)&p, sizeof(p));
> 
> 	cout << "年龄" << p.m_Age << "姓名" << p.m_Name << endl;
> 
> 	ifs.close();
> 
> }
> 
> int main()
> {
> 	test02();
> 
> 	return 0;
> }
> ```
>



## 6、智能指针

以下例子中所需要的头文件和源文件

Cat.h

```cpp
#pragma once
#include <iostream>
#include <string>

using namespace std;

class Cat
{
public:
	Cat() = default;
	Cat(string name);
	~Cat();

	void cat_info() const
	{
		cout << "cat_info name: " << name << endl;
	}

	string get_name() const
	{
		return name;
	}

	void set_cat_name(const string& name)
	{
		this->name = name;
	}

private:
	string name = "zx";
};
```

Cat.cpp

```cpp
#include "Cat.h"

Cat::Cat(string name) :name(name)
{
	cout << "Cat的构造函数调用" << endl;
}

Cat::~Cat()
{
	cout << "Cat:" << name << "的析构函数的调用" << endl;
}
```

### 6.1 智能指针

> 作用：解决原始指针会出现的几类问题：
>
> + 忘记释放堆区内存
> + 重复释放堆区内存
> + 指针指向混乱

> 分类：
>
> + unique_ptr
> + shared_ptr
> + weak_ptr

使用智能指针时一定需要引用`<memory>`头文件



### 6.2 unique_ptr

#### 1.unique_ptr的创建

unique指针有**独占**的作用，即被unique指针指向的内存只允许该unique指针管理

其创建方式有三种：

> + `unique_ptr<类名> 指针名(类对象名)`（类对象需要提前构造并开辟内存）
> + `unique_ptr<类名> 指针名{new 类名()}`（new之后接类的构造函数（普通/默认/拷贝））
> + ==推荐使用：==`unique_ptr<类名> 指针名 = make_unique<类名>() `（类名接构造函数）

> 第一种方法
>
> ```cpp
> #include <iostream>
> #include <memory>//使用智能指针一定要引用的头文件
> #include "Cat.h"
> 
> using namespace std;
> 
> int main()
> {
> 	//unique指针的创建
> 	//第一种方法
> 	Cat* c_p2 = new Cat("zyj");
> 	unique_ptr<Cat> u_c_p2(c_p2);
> 	c_p2->cat_info();
> 	u_c_p2->cat_info();
> 	c_p2->set_cat_name("hahaha");
> 	u_c_p2->cat_info();
> 	//设置为unique指针后，仍能通过原指针操作对象
> 	//未达到独占的效果，同时也不安全，所以应该改变原指针的指向
> 	//delete c_p2;直接delete会使指向的内存也被清空，导致unique指针悬空
>     //所以应该只改变原指针的指向，即让原指针变为空指针
> 	c_p2 = nullptr;
> 	u_c_p2->set_cat_name("xixi");
> 	u_c_p2->cat_info();//可以通过unique指针来操作内存了
> 
> 	cout << "------yz--------"<< endl;
> 	return 0;
> }
> ```

> 第二种方法
>
> ```cpp
> unique_ptr<Cat> u_c_p3{ new Cat("xixi") };
> unique_ptr<int> u_i_p2{ new int(25) };
> u_c_p3->set_cat_name("haha");
> u_c_p3->cat_info();
> ```

> 第三种方法（推荐）
>
> ```cpp
> unique_ptr<Cat> u_c_p4 = make_unique<Cat>();
> unique_ptr<int> u_i_p3 = make_unique<int>(19);
> u_c_p4->cat_info();
> u_c_p4->set_cat_name("wudi");
> string temp = u_c_p4->get_name();
> cout << temp << endl;
> ```

> 补充：
>
> ```cpp
> //unique指针的 .get()函数用来获取指针地址
> cout << "int address " << u_i_p3.get() << endl;
> cout << "cat address " << u_c_p4.get() << endl;
> ```
>

#### 2.unique_ptr与函数调用

注意：unique_ptr对象之间不可以进行赋值操作，只可以move转用指针所有权

> + 传值
> 	+ 需要用std::move来转移内存拥有权
> 	+ 如果参数直接传入std::move_unique语句 ，会自动转换为move
> + 传引用
> 	+ 不需要使用move，因为引用是别名
> 	+ 如果参数前加上const修饰则不能改变指针指向了（使用不了reset（））
> + unique做函数返回值
> 	+ 可以用作链式函数

> 传值：
>
> ```cpp
> //传值
> void do_with_cat_pass_value(unique_ptr<Cat> c)
> {
> 	c->cat_info();
> }
> 
> //main函数中调用该函数的方法
> do_with_cat_pass_value(move(c1));
> ```
>
> 传引用：
>
> ```cpp
> void do_with_cat_pass_ref(const unique_ptr<Cat> &c)
> {
> 	c->set_cat_name("zjz");
> 	c->cat_info();
> //	c.reset();//加了const就使用不了
> }
> 
> do_with_cat_pass_ref(c2);
> ```
>
> 做返回值：
>
> ```cpp
> unique_ptr<Cat> get_unique_ptr()
> {
> 	unique_ptr<Cat> p_dog = make_unique<Cat>("Local cat");
> 	cout << "unique address: " << p_dog.get() << endl;
> 	cout << "unique address: " << &p_dog << endl;
> 
> 	return p_dog;
> }
> 
> unique_ptr<Cat> c3 = get_unique_ptr();
> ```



### 6.3 shared_ptr

#### 1.shared_ptr计数指针

> + shared_ptr计数指针又称为共享指针
> + 可以共享数据，即不同shared_ptr对象间可以进行赋值（copy）操作，也不需要使用move
> + copy则计数器+1，对象销毁则-1

> ```cpp
> #include <iostream>
> #include <memory>//使用智能指针一定要引用的头文件
> #include "Cat.h"
> 
> using namespace std;
> 
> int main()
> {
>     shared_ptr<int> i_p1 = make_shared<int>(10);
> //	shared_ptr<int> i_p2 = make_shared<int>(new int(10));
> 	cout << "value: " << *i_p1 << endl;
> 	cout << "i_p1 count: " << i_p1.use_count() << endl;
> 
> 	shared_ptr<int> i_p3 = i_p1;
> 	*i_p3 = 25;//指向同一块内存的所有计数指针所指向的值都被修改
> 
> 	cout << "i_p1 count: " << i_p1.use_count() << endl;
> 	cout << "i_p3 count: " << i_p3.use_count() << endl;
> 	cout << *i_p1 << endl;
> 	cout << *i_p3 << endl;
> 
> 	//i_p3 = nullptr;
> 	//cout << "i_p1 count: " << i_p1.use_count() << endl;
> 	//cout << "i_p3 count: " << i_p3.use_count() << endl;
> 
> 	shared_ptr<int> i_p4 = i_p1;
> 	i_p1 = nullptr;
> 	cout << "i_p1 count: " << i_p1.use_count() << endl;
> 	cout << "i_p3 count: " << i_p3.use_count() << endl;
> 	cout << "i_p4 count: " << i_p4.use_count() << endl;
> 
> 	cout << "count: " << i_p1.use_count() << endl;
>     //以上是常量类型的计数指针
>     
>     //下面是自定义类型的计数指针
> 	shared_ptr<Cat> c_p1 = make_shared<Cat>();
> 	cout << "c_p1 count: " << c_p1.use_count() << endl;
> 
> 	shared_ptr<Cat> c_p2 = c_p1;
> 	shared_ptr<Cat> c_p3 = c_p1;
> 
> 	cout << "c_p1 count: " << c_p1.use_count() << endl;
> 	cout << "c_p2 count: " << c_p2.use_count() << endl;
> 	cout << "c_p3 count: " << c_p3.use_count() << endl;
>     
>     
> 	c_p1.reset();
> 	cout << "c_p1 count: " << c_p1.use_count() << endl;
> 	cout << "c_p2 count: " << c_p2.use_count() << endl;
> 	cout << "c_p3 count: " << c_p3.use_count() << endl;
> 
> 	return 0;
> }
> ```



#### 2.shared_ptr的函数调用

> + 传值
> 	+ 进行拷贝
> 	+ 计数器加一
> + 传引用不会使得计数器+1
> + 返回值同unique

> ```cpp
> #include <iostream>
> #include <memory>//使用智能指针一定要引用的头文件
> #include "Cat.h"
> 
> using namespace std;
> 
> //传值
> void cat_by_value(shared_ptr<Cat> c)
> {
> 	cout << c->get_name() << endl;
> 	c->set_cat_name("zyj");
> 	cout << "c count: " << c.use_count() << endl;
> }
> 
> //传引用
> void cat_by_ref(const shared_ptr<Cat>& c)
> {
> 	cout << c->get_name() << endl;
> //	c.reset(new Cat());
> 	cout << "func c count: " << c.use_count() << endl;//不加1
> }
> 
> //
> shared_ptr<Cat> get_shared_cat()
> {
> 	shared_ptr<Cat> temp = make_shared<Cat>();
> 	return temp;
> }
> 
> int main()
> {
> 	shared_ptr<Cat> c1 = make_shared<Cat>();
> 	cat_by_value(c1);
> 	cout << "c1 count: " << c1.use_count() << endl;
> 	cout << "c1 name: " << c1->get_name() << endl;
> 
> 	cat_by_ref(c1);
> 	cout << "c1 count: " << c1.use_count() << endl;
> 	c1->cat_info();
> 
> 	cout << "--------end---------" << endl;
> 
> 	return 0;
> }
> ```
>
> 



### 6.4 weak_ptr







