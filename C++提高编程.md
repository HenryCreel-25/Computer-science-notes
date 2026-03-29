[toc]

# C++提高编程

+ 本阶段对C++==**泛型编程**==和==**STL技术**==做详细解释



## 1.模板

### 1.1 模板的概念

模板就是建立通用的模具，大大**提高复用性**



> 模板的特点：
>
> + 模板不可以直接使用，它只是一个框架
> + 模板的通用不是万能的



### 1.2 函数模板

C++的一种编程思想称为 ==泛型编程==，主要利用的技术就是模板

两种模板机制： **函数模板** 和 **类模板**



#### 1.函数模板语法

> 函数模板作用：建立一个通用函数，其函数返回值类型和形参类型可以不具体制定，用一个**虚拟的类型**来代表

> 语法：
>
> ```cpp
> template<typename T>
> 函数声明或定义
> ```
>
> template——声明创建模板
>
> typename——表明其后的符号是一种数据类型，可以用class代替
>
> T——通用的数据类型，名称可以替换，通常为大写字母
>
> 
>
> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> //函数模板
> template<class T>
> void Swap(T& a, T& b) {
> 	T temp = a;
> 	a = b;
> 	b = temp;
> }
> 
> void test01()
> {
> 	
> 	int a = 25;
> 	int b = 19;
> 	
> 	//1.自动类型推导
> 	Swap(a, b);//编译器自动推导T的数据类型
> 	//2.显示指定类型
> 	Swap<int>(a, b);//让编译器将T推为指定数据类型
> 	cout << a << endl;
> 	cout << b << endl;
> 
> 	double c = 3.14;
> 	double d = 1.89;
> 	swap(c, d);
>    	cout << c << endl;
>    	cout << d << endl;
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



#### 2.函数模板注意事项

> + 自动类型推导，必须推导出一致的数据类型T，才可以使用
> + 模板必须要确定出T的数据类型，才可以使用
>
> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> //函数模板
> template<class T>
> void Swap(T& a, T& b) {
> 	T temp = a;
> 	a = b;
> 	b = temp;
> }
> 
> void test01()
> {
> 
> 	int a = 25;
> 	int b = 19;
> 	char c = 'c';
> 	Swap(a, b);
> //	Swap(a, c);推导不出一致的T类型
> }
> 
> template <class T>
> void func()//该模板的实现没有使用到T数据类型
> {
> 	cout << "func()的调用" << endl;
> }
> 
> void test02()
> {
> //	func();
> 	func<int>();//显示指定类型后才正确
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



#### 3.普通函数与函数模板的区别

> + 普通函数调用时可以**发生自动类型转换**（隐式类型转换）
> + 函数模板调用时，如果利用<u>自动类型推导，不会发生隐式类型转换</u>
> + 如果用<u>显示指定类型的方式，可以发生隐式类型转换</u>

> ```c++
> #include <iostream>
> 
> using namespace std;
> 
> int MyAdd01(int a, int b)
> {
> 	return a + b;
> }
> 
> template<class T>
> int MyAdd02(T a, T b)
> {
> 	return a + b;
> }
> 
> void test01()
> {
> 	int a = 25;
> 	int b = 19;
> 	char c = 'c';
> 	cout << MyAdd01(a, b) << endl;
> 	cout << MyAdd01(a, c) << endl;//将char类型的'c'隐式转换为int类型 c -- 99
> 
> 	cout << MyAdd02(a, b) << endl;
> 
> 	//自动类型推导 ，不会发生隐式类型转换
> //	cout << MyAdd02(a, c) << endl;
> 
> 	//显示指定类型 ，会发生隐式类型转换
> 	cout << MyAdd02<int>(a, c) << endl;
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
> 总结：在调用函数模板时，推荐使用==显示指定类型==的方式，可以自己确定通用类型T



#### 4.普通函数与函数模板的调用规则

> 1.如果模板与普通函数都可以实现，优先调用普通函数
>
> 2.可以通过空模板参数列表来强制调用函数模板
>
> 3.函数模板也可以发生重载
>
> 4.如果函数模板可以产生更好的匹配，优先调用函数模板

> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> void myPrint(int a, int b)
> {
> 	cout << "调用的普通函数" << endl;
> }
> 
> template<class T>
> void myPrint(T a,T b)
> {
> 	cout << "调用的模板" << endl;
> }
> 
> template<class T>
> void myPrint(T a, T b,T c)//模板也可以发生重载
> {
> 	cout << "调用重载的模板" << endl;
> }
> 
> void test01()
> {
> 	int a = 25;
> 	int b = 19;
> 	
>     //1.如果模板与普通函数都可以实现，优先调用普通函数
> 	myPrint(a, b);
> 
>     //2.可以通过空模板参数列表来强制调用函数模板
> 	myPrint<>(a, b);
> 
>     //3.函数模板也可以发生重载
> 	myPrint(a, b, 100);
> }
> 
> void test02()
> {
> 	char a = 'a';
> 	char b = 'b';
>     //4.如果函数模板可以产生更好的匹配，优先调用函数模板
> 	myPrint(a, b);//此处如果调用普通函数，则要将int类型转为char
>     			  //而使用模板就可以直接让T变为char类型
> }
> 
> int main()
> {
> //	test01();
> 
> 	test02();
> 
> 	return 0;
> }
> ```
>



#### 案例：函数模板实现数组的选择排序

> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> template <class T>
> void sort(T arr[],int len)
> {
> 	for (int i = 0; i < len - 1; i++)
> 	{
> 		int loc_min = i;
> 		for (int k = i + 1; k < len; k++)
> 		{
> 			if (arr[k] < arr[loc_min])
> 			{
> 				loc_min = k;
> 			}
> 		}
> 		int temp = arr[i];
> 		arr[i] = arr[loc_min];
> 		arr[loc_min] = temp;
> 	}
> }
> //以上是模板
> 
> int main()
> {
> 	int size;
> 	cin >> size;
> 	//char型
> 	char* arr = new char[size];
> 	for (int i = 0; i < size; i++)
> 	{
> 		cin >> arr[i];
> 	}
> 
> 	sort(arr,size);
> 
> 	for (int i = 0; i < size; i++)
> 	{
> 		cout << arr[i] << "  ";
> 	}
> 	cout << endl;
> 
> 	//int型
>     cin >> size;
> 	int* arr_int = new int[size];
> 	for (int i = 0; i < size; i++)
> 	{
> 		cin >> arr_int[i];
> 	}
> 
> 	sort(arr_int, size);
> 
> 	for (int i = 0; i < size; i++)
> 	{
> 		cout << arr_int[i] << "  ";
> 	}
> 	cout << endl;
> 
> 	system("pause");
> 
> 	return 0;
> }
> //其实交换两数 和 打印结果 的操作都可以使用模板
> ```



#### 5.模板的局限性

模板的通用性并不是万能的

> **例如**：
>
> ```cpp
> template<class T>
>     void f(T a,T b)
> {
>     a = b;
> }
> ```
>
> 如果传入的a和b是一个数组，就无法实现了

> 再例如：
>
> ```cpp
> template<class T>
>     void f(T a,T b)
> {
>     if(a>b)
>     {...}
> }
> ```
>
> 如果T的数据类型是Person这样的自定义数据类型，也无法正常运行

所以cpp提供模板的重载来解决上述问题，可以为这些**特定的类型**提供**具体化的模板**

> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> class Person
> {
> public:
> 	string m_Name;
> 	int m_Age;
> 	Person(string name, int age) :m_Name(name), m_Age(age)
> 	{
> 
> 	}
> };
> 
> //普通函数模板
> template<class T>
> bool myCompare(T& a, T& b)
> {
> 	if (a == b)
> 	{
> 		return true;
> 	}
> 	else
> 	{
> 		return false;
> 	}
> }
> 
> //具体化，显示具体化的原型，以template开头，并通过名称来指出类型
> //具体化优先于常规模板
> template <> bool myCompare(Person& p1, Person& p2)
> {
> 	if (p1.m_Name == p2.m_Name && p1.m_Age == p2.m_Age)
> 	{
> 		return true;
> 	}
> 	else
> 	{
> 		return false;
> 	}
> }
> 
> void test01()
> {
> 	Person p1("Tom", 20);
> 	Person p2("Tom", 20);
> 
> 	if (myCompare(p1, p2))
> 	{
> 		cout << "p1 == p2" << endl;
> 	}
> 	else
> 	{
> 		cout << "p1 != p2" << endl;
> 	}
> }
> 
> 
> int main()
> {
> 	
> 	test01();
> 
> 	return 0;
> }
> ```
>
> + 利用具体化的模板，可以解决自定义类型的通用化
> + 学习模板是为了在STL能狗运用系统提供的模板



### 1.3 类模板

#### 1.类模板语法

作用：建立一个**通用类**，类中的成员 数据类型可以不具体写明，用一个虚拟的类型代替

**语法：**

```cpp
template<typename T>
类的创建
```

> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> template<class NameType,class AgeType>
> class  Person
> {
> public:
> 	Person(NameType name,AgeType age)
> 	{
> 		this->m_Age = age;
> 		this->m_Name = name;
> 	}
> 	void showInfo()
> 	{
> 		cout << "name: " << this->m_Name 
> 			<< " age: " << this->m_Age << endl;
> 	}
> 
> 	NameType m_Name;
> 	AgeType m_Age;
> 
> };
> 
> void test01()
> {
> 	//指定NameType为string类型，AgeType为int类型
> 	Person<string, int> p1("me", 20);
> 	p1.showInfo();
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



#### 2.类模板函数模板区别

> 两点区别：
>
> + 类模板没有自动类型推导的使用方式
> + 类模板在模板参数列表中可以有默认参数

> 类模板没有自动类型推导的使用方式
>
> ```cpp
> Person p1("me", 20);//不能通过编译
> p1.showInfo();
> ```
>
> 

> 类模板在模板参数列表中可以有默认参数：
>
> ```cpp
> 						//默认参数是数据类型
> template<class NameType,class AgeType = int>
> class  Person
> {
> public:
> 	Person(NameType name,AgeType age)
> 	{
> 		this->m_Age = age;
> 		this->m_Name = name;
> 	}
> 	void showInfo()
> 	{
> 		cout << "name: " << this->m_Name 
> 			<< " age: " << this->m_Age << endl;
> 	}
> 
> 	NameType m_Name;
> 	AgeType m_Age;
> 
> };
> 
> void test01()
> {
> 	//省略了int的声明
> 	Person<string> p1("me", 20);
> 	p1.showInfo();
> }
> ```
>
> 

#### 3.类模板中成员函数创建时机

类模板成员函数 和 普通类中成员函数 创建时机是有区别的

+ 普通类中成员函数 一开始就可以创建
+ 类模板成员函数 在调用时才创建

> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> class Person1
> {
> public:
> 	void showPerson1()
> 	{
> 		cout << "showing Person1" << endl;
> 	}
> };
> 
> class Person2
> {
> public:
> 	void showPerson2()
> 	{
> 		cout << "showing Person2" << endl;
> 	}
> };
> 
> template<class Type>
> class Person
> {
> public:
> 	Type obj;
> 	void func1()
> 	{
> 		obj.showPerson1();
> 	}
> 	void func2()
> 	{
> 		obj.showPerson2();
> 	}
> };
> 
> int main()
> {
> 
> 	return 0;
> }
> ```
>
> 尽管类模板里obj在数据类型不确定的情况下，声明使用了两个不同类的成员函数，但还是能通过调试

>  ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> class Person1
> {
> public:
> 	void showPerson1()
> 	{
> 		cout << "showing Person1" << endl;
> 	}
> };
> 
> class Person2
> {
> public:
> 	void showPerson2()
> 	{
> 		cout << "showing Person2" << endl;
> 	}
> };
> 
> template<class Type>
> class Person
> {
> public:
> 	Type obj;
> 	void func1()
> 	{
> 		obj.showPerson1();
> 	}
> 	void func2()
> 	{
> 		obj.showPerson2();
> 	}
> };
> 
> void test01()
> {
> 	Person<Person1> m;
> 	m.func1();
> 	m.func2();
> }
> //test01里调用了类模板的成员函数，此时obj的数据类型确定了，就只能调用对应的类成员函数了
> 
> int main()
> {
> 	test01();
> 
> 	return 0;
> }
>  ```
>
> 



#### 4.类模板对象做函数参数

类模板对象做函数参数 即 用类模板实例化出的对象，向函数传参

> 一共有三种传入方式：
>
> 1.指定传入的类型 	—— 直接显示对象的数据类型
>
> 2.参数模板化 			—— 将对象中的参数变为模板进行传递
>
> 3.整个类模板化 		——将这个对象类型 模板化进行传递

> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> template<class NameType,class AgeType>
> class Person
> {
> public:
> 	Person(NameType name, AgeType age)
> 	{
> 		this->m_Name = name;
> 		this->m_Age = age;
> 	}
> 
> 	void showPerson()
> 	{
> 		cout << "name: " << this->m_Name 
> 			<< " age: " << this->m_Age << endl;
> 	}
> 
> 	NameType m_Name;
> 	AgeType m_Age;
> };
> 
> //1.指定传入的类型 —— 直接显示对象的数据类型
> void printPerson1(Person<string,int> &p)
> {
> 	p.showPerson();
> }
> 
> void test01()
> {
> 	Person<string, int> p1("zyujian", 20);
> 	printPerson1(p1);
> }
> 
> int main()
> {
> //	test01();
> //	test02();
> 	test03();
> 
> 	return 0;
> }
> ```

> 2.参数模板化 			—— 将对象中的参数变为模板进行传递
>
> ```cpp
> //2.参数模板化
> template<class T1,class T2>
> void printPerson2(Person<T1,T2>&p2)
> {
> 	p2.showPerson();
> 	cout << "T1的数据类型为：" << typeid(T1).name() << endl;
> 	cout << "T2的数据类型为：" << typeid(T2).name() << endl;
> }
> 
> void test02()
> {
> 	Person<string, int> p2("zjingzhao", 21);
> 	printPerson2(p2);
> }
> ```

> ```cpp
> //3.整个类模板化
> template<class T>
> void printPerson3(T &p3)
> {
> 	p3.showPerson();
> 	cout << "T的数据类型为：" << typeid(T).name() << endl;
> }
> 
> void test03()
> {
> 	Person<string, int> p3("zfugui", 99);
> 	printPerson3(p3);
> }
> ```
>
> 最常用的即**指定传入类型**



#### 5.类模板的继承

> 当类模板内发生继承时，需要注意两点：
>
> + 当子类继承的父类是类模板时，子类在声明的时候，要知道出父类中T的类型
> 	+ 如果不指定，编译器无法给子类分配内存
> + 如果想灵活指出父类中T的类型，子类也需要变为类模板

> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> template<class T>
> class Base
> {
> 	T m;
> };
> 
> //class Son :public Base // 错误，必须要指定Base的参数类型
> class Son:public Base<int>
> 	//Base的数据类型不可修改了
> {
> 	
> };
> 
> void test01()
> {
> 	Son s1;
> }
> 
> //想要灵活指定父类中T类型，子类也需要变类模板
> template<class T1,class T2>
> class Son2 :public Base<T2>
> {
> 	T1 obj;
> public:
> 	Son2()
> 	{
> 		cout << "T1的数据类型为：" << typeid(T1).name() << endl;
> 		cout << "T2的数据类型为：" << typeid(T2).name() << endl;
> 	}
> };
> 
> void test02()
> {
> 	Son2<int, char> s2;
> }
> 
> int main()
> {
> 	test01();
> 
> 	test02();
> 
> 	return 0;
> }
> ```
>
> 



#### 6.类模板成员函数的类外实现

首先，需要在类外实现的类模板函数 需要在模板里先声明

其次，类外实现和函数模板的实现相似

> ```cpp
> template<class T>
> 返回值类型 作用域<T>::函数名(参数列表)
> {
>     
> }
> ```

> ```cpp
> #include <iostream>
> #include <string>
> 
> using namespace std;
> 
> template<class T1,class T2>
> class Person
> {
> public:
>     //成员函数类内声明
> 	Person(T1 name, T2 age);
> 	void showPerson();
> public:
> 	T1 m_Name;
> 	T2 m_Age;
> };
> 
> //类似函数模板的实现
> template<class T1,class T2>
> //与函数模板的区别在于作用域后加上数据类型参数
> Person<T1, T2>::Person(T1 name, T2 age)
> {
> 	this->m_Age = age;
> 	this->m_Name = name;
> }
> 
> template<class T1,class T2>
> void Person<T1, T2>::showPerson()
> {
> 	cout << "姓名：" << this->m_Name << "\t年龄：" << this->m_Age << endl;
> }
> 
> void test01()
> {
> 	Person<string, int> p1("zyujian", 20);
> 	p1.showPerson();
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



#### 7.类模板分文件编写

因为类模板中的成员函数 创建时机是在调用阶段，所以会导致在分文件编写时链接不到 成员函数

![image-20251203203406131](C:/Users/张翔/AppData/Roaming/Typora/typora-user-images/image-20251203203406131.png)

> 解决方式：
>
> 1.直接包含.cpp源文件（头文件还是需要编写必要内容）
>
> + 因为cpp文件会包含对应的.h头文件，
>
> 2.将声明和实现写在同一个文件中，并更改后缀名为.hpp（hpp是约定名称，并非强制）



> **1.包含.cpp源文件**
>
> .h文件
>
> ```cpp
> #pragma once
> #include <iostream>
> 
> using namespace std;
> 
> template<class T1,class T2>
> class Person
> {
> public:
> 	Person(T1 name,T2 age);
> 	void showPerson();
> public:
> 	T1 m_Name;
> 	T2 m_Age;
> };
> ```
>
> .cpp文件
>
> ```cpp
> #include "Cat.h"
> 
> template<class T1,class T2>
> Person<T1, T2>::Person(T1 name,T2 age)
> {
> 	this->m_Age = age;
> 	this->m_Name = name;
> }
> 
> template<class T1, class T2>
> void Person<T1, T2>::showPerson()
> {
> 	cout << "姓名：" << this->m_Name << 
> 		"\t年龄：" << this->m_Age << endl;
> }
> ```
>
> 测试文件
>
> ```cpp
> #include <iostream>
> #include "Cat.cpp"//直接包含.cpp文件
> 
> using namespace std;
> 
> int main()
> {
> 	Person<string, int> p1("zyujian", 20);
> 	p1.showPerson();
> 	return 0;
> }
> ```



> **2.将声明和实现写在同一个文件中，并更改后缀名为==.hpp==**
>
> ==.hpp==文件
>
> ```cpp
> #pragma once//声明与实现写在同一个文件中
> #include <iostream>
> 
> using namespace std;
> 
> template<class T1,class T2>
> class Person
> {
> public:
> 	Person(T1 name, T2 age)
> 	{
> 		this->m_Age = age;
> 		this->m_Name = name;
> 	}
> 	void showPerson()
> 	{
> 		cout << "姓名：" << this->m_Name <<
> 			"\t年龄：" << this->m_Age << endl;
> 	}
> public:
> 	T1 m_Name;
> 	T2 m_Age;
> };
> ```
>
> 测试文件
>
> ```cpp
> #include <iostream>
> #include "Cat.hpp"//此处引用.hpp文件
> 
> using namespace std;
> 
> int main()
> {
> 	Person<string, int> p1("zyujian", 20);
> 	p1.showPerson();
> 	return 0;
> }
> ```
>
> 主流方式为第二种，即写为.hpp文件



#### 8.类模板与函数友元

全局函数类内实现 - 直接在类内声明实现友元即可

全局函数类外实现 - 要提前让编译器直到全局函数的存在（比较麻烦）

以下 “通过全局函数 实现打印Person信息”为例

> **1.全局函数类内实现**
>
> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> template<class T1, class T2>
> class Person
> {
>     //友元的作用是为了让函数在类外被调用时能访问private成员
> 	friend void printPerson(Person<T1, T2> p)//类内声明和实现
> 	{
> 		cout << "姓名：" << p.m_Name
> 			<< "\t年龄：" << p.m_Age << endl;
> 	}
> public:
> 	Person(T1 name, T2 age)
> 	{
> 		this->m_Age = age;
> 		this->m_Name = name;
> 	}
> 	
> private:
> 	T1 m_Name;
> 	T2 m_Age;
> };
> 
> void test01()
> {
> 	Person<string, int> p1("zyujian", 20);
> 	printPerson(p1);
> }
> 
> int main()
> {
> 	test01();
> 
> 	return 0;
> }
> ```



> **2.全局函数类外实现**
>
> ```cpp
> #include <iostream>
> 
> using namespace std;
> //通过全局函数 实现打印Person信息
> 
> template<class T1, class T2>
> class Person
> {
> 	friend void printPerson(Person<T1, T2> p);//友元声明在此
> public:
> 	Person(T1 name, T2 age)
> 	{
> 		this->m_Age = age;
> 		this->m_Name = name;
> 	}
> private:
> 	T1 m_Name;
> 	T2 m_Age;
> };
> 
> template<class T1,class T2>//类外的实现
> void printPerson(Person<T1,T2> p)
> {
> 	cout << "姓名：" << p.m_Name
> 		<< "\t年龄：" << p.m_Age << endl;
> }
> 
> void test01()
> {
> 	Person<string, int> p1("zyujian", 20);
> 	printPerson(p1);
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
> 以上代码语法正确，但编译会报错，原因在于
>
> `friend void printPerson(Person<T1, T2> p);`此处的printPerson就是一个**普通的函数**，而
>
> ```cpp
> template<class T1,class T2>
> void printPerson(Person<T1,T2> p)
> {
> 	cout << "姓名：" << p.m_Name
> 		<< "\t年龄：" << p.m_Age << endl;
> }
> ```
>
> 此处的printPerson变为了一个**函数模板**。上下所指并非一个东西，所以编译报错

> 解决办法:
>
> 给友元声明的函数加空模板参数列表，使其变为函数模板
>
> ​                                               此处
>
> `friend void printPerson<> (Person<T1, T2> p);`
>
> 
>
> 而模板需要先用 <u>template的函数声明</u>，所以要将<u>template的函数声明</u>移至Person类创建前
>
> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> template<class T1, class T2>//函数模板声明前移
> void printPerson(Person<T1, T2> p)
> {
> 	cout << "姓名：" << p.m_Name
> 		<< "\t年龄：" << p.m_Age << endl;
> }
> 
> template<class T1, class T2>
> class Person
> {
> 	friend void printPerson<> (Person<T1, T2> p);
> public:
> 	Person(T1 name, T2 age)
> 	{
> 		this->m_Age = age;
> 		this->m_Name = name;
> 	}
> private:
> 	T1 m_Name;
> 	T2 m_Age;
> };
> 
> void test01()
> {
> 	Person<string, int> p1("zyujian", 20);
> 	printPerson(p1);
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
> 但仍然会报错，因为将 <u>template函数声明</u> 放在 <u>Person类创建前</u> 类模板里使用的Person对象就没有意义，所以再前移的 <u>template函数声明</u> 前再加上Person类的声明
>
> 最终代码
>
> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> template<class T1,class T2>//先声明类模板
> class Person;
> 
> template<class T1, class T2>//再声明全局函数的模板
> void printPerson(Person<T1, T2> p)
> {
> 	cout << "姓名：" << p.m_Name
> 		<< "\t年龄：" << p.m_Age << endl;
> }
> 
> template<class T1, class T2>
> class Person
> {
> 	friend void printPerson<> (Person<T1, T2> p);
> public:
> 	Person(T1 name, T2 age)
> 	{
> 		this->m_Age = age;
> 		this->m_Name = name;
> 	}
> private:
> 	T1 m_Name;
> 	T2 m_Age;
> };
> 
> void test01()
> {
> 	Person<string, int> p1("zyujian", 20);
> 	printPerson(p1);
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



## 2.初识STL

### 1.STL基本概念

+ STL从广义上分为：**容器(container)，算法(algorithm)，迭代器(iterator)**
+ 三者关系：**容器**和**算法**之间通过**迭代器**进行无缝连接
+ STL几乎所有的代码都采用 模板类 或 模板函数

### 2.STL六大组件

STL大体分为六大组件，分别是：**容器，算法，迭代器，仿函数，适配器（配接器），空间配置器**

> + **容器**：数据结构
> + **算法**：sort,find,copy,for_each等等
> + **迭代器**：容器与算法的桥梁
> + **仿函数**：行为类似函数，可作为算法的某种策略
> + 适配器：一种用来修饰容器 或者 仿函数 或者 迭代器接口 的东西
> + 空间配置器：负责空间的管理与配置



### 3.STL中容器，算法，迭代器

> STL**容器**就是将**运用最广泛的一些数据结构**实现出来
>
> 常用的数据结构：数组，链表，树，栈，队列，集合，映射表
>
> 容器分为两种：
>
> + **序列式容器**：强调值的排序，序列式容器中的每个元素均有固定的位置
> + **关联式容器**：二叉树结构，各元素之间没有严格的物理上的顺序关系

> 算法就是在有限的步骤里解决逻辑或数学问题
>
> 算法分为两种：
>
> + **质变算法**：指运算过程中**会**更改区间内的元素的内容，如拷贝，替换，删除…
> + **非质变算法**：指运算过程中**不会**更改区间内的元素的内容，如查找，计数，遍历…

> **迭代器**提供一种方法，使其能够依序寻访某个容器所含的各个元素，而又无需暴露该容器的内部表示方式。
>
> 每个容器都有自己专属的迭代器
>
> 迭代器的使用非常类似于指针，可以先理解迭代器为指针方便学习

> 迭代器种类：
>
> | 种类           | 功能                                                     | 支持运算                             |
> | -------------- | -------------------------------------------------------- | ------------------------------------ |
> | 输入迭代器     | 对数据的只读访问                                         | 只读，支持++、==、！=                |
> | 输出迭代器     | 对数据的只写访问                                         | 只写，支持++                         |
> | 前向迭代器     | 读写操作，并能向前推进迭代器                             | 读写，支持++、==、！=                |
> | 双向迭代器     | 读写操作，并能向前和向后操作                             | 读写，支持++、– –                    |
> | 随机访问迭代器 | 读写操作，可以以跳跃的方式访问任意数据，功能最强的迭代器 | 读写，支持++，– –，[n],-n,关系运算符 |
>
> 

### 4.容器算法迭代器初识

STL中最常用的容器为vector，可以理解为数组。以下以vector为例进行学习

#### 1.vector存放内置数据类型

> 容器：`vector<数据类型>`
>
> 算法：`for_each`
>
> 迭代器：`vector<数据类型>::iterator`

> ```cpp
> #include <iostream>
> #include <vector>
> using namespace std;
> 
> void test01()
> {
> 	vector<int> v;
>     //尾插法
> 	v.push_back(10);
> 	v.push_back(20);
> 	v.push_back(30);
> 	v.push_back(40);
> 	//iterator先理解为指针
>     //v.end()指向的是最后一个元素的下一个位置
> 	for (vector<int>::iterator it = v.begin(); it != v.end(); it++)
> 	{
>         //解指针操作
> 		cout << *it << endl;
> 	}
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

#### 2.vector存放自定义数据类型

> ```cpp
> #include <iostream>
> #include <vector>
> 
> using namespace std;
> 
> class Person
> {
> public:
> 	string m_Name;
> 	int m_Age;
> 
> 	Person(string name, int age)
> 	{
> 		this->m_Name = name;
> 		this->m_Age = age;
> 	}
> 	void showInfo()
> 	{
> 		cout << "Name: " << this->m_Name
> 			<< " Age: " << this->m_Age;
> 	}
> };
> 
> void test01()
> {
> 	Person p1("aaa", 20);
> 	Person p2("bbb", 30);
> 	Person p3("ccc", 40);
> 	Person p4("ddd", 50);
> 	Person p5("eee", 60);
> 
> 	vector<Person> v;
> 	v.push_back(p1);
> 	v.push_back(p2);
> 	v.push_back(p3);
> 	v.push_back(p4);
> 	v.push_back(p5);
> 
> 	for (vector<Person>::iterator it = v.begin(); it != v.end(); it++)
> 	{
> 		(*it).showInfo();
>    //或者it->showInfo();
> 		cout << endl;
> 	}
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

#### 3.vector容器嵌套容器

> ```cpp
> #include <iostream>
> #include <vector>
> using namespace std;
> 
> void test02()
> {
>     //可以理解为二维数组
> 	vector<vector<int>> v;
> 
> 	vector<int> v1;
> 	vector<int> v2;
> 	vector<int> v3;
> 	vector<int> v4;
> 
> 	for (int i = 0; i < 4; i++)
> 	{
> 		v1.push_back(i + 1);
> 		v2.push_back(i + 2);
> 		v3.push_back(i + 3);
> 		v4.push_back(i + 4);
> 	}
> 	
> 	v.push_back(v1);
> 	v.push_back(v2);
> 	v.push_back(v3);
> 	v.push_back(v4);
> 	
>     
> 	for (vector<vector<int>>::iterator it = v.begin(); it != v.end(); it++)
> 	{
> 		for (vector<int>::iterator vit = (*it).begin(); vit != (*it).end(); vit++)
> 		{
> 			cout << *vit << " ";
> 		}
> 		cout << endl;
> 	}
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
> 



## 3.STL常用容器

### 3.1 string容器

#### 1.string的基本概念

本质：

+ string是C++风格的字符串，而string本质上是一个类

**string和char*的区别：**

+ char*是一个指针
+ string是一个类，类内部封装了char*，管理这个字符串，是一个char\*型的容器

**特点：**

string类内部封装了很多成员方法

例如：查找find，拷贝copy，删除delete，替换replace，插入insert。

并且string管理char*所分配的内存，不用担心赋值越界和取值越界等，由类内部进行负责



#### 2.构造函数

构造函数原型：

> + `string();  //创建空字符串`
>
> 	`string(const char* s);   //使用字符串s初始化`
>
> + `string(const string& str);`
> + `string(int n,char c);   //使用n个字符c初始化`

> ```cpp
> #include <iostream>
> #include <string>
> using namespace std;
> 
> void test01()
> {
> 	string str1();
> 
> 	string str2("Hello world"); //普通构造
> 
> 	string str3(str2); //拷贝构造
> 
> 	string str4(5, 'd');
> 
> //	cout << str1 << endl;
> 
> 	cout << str2 << endl 
> 		<< str3 << endl << str4 << endl;
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

#### 3.赋值操作

赋值函数原型：

> + `string& operator=(const char* s);`
> + `string& operator=(const string &s);`
> + `string& operator=(char c);`

> + `string& assign(const char* s)    //把字符串s赋给当前字符串`
> + `string& assign(const char* s,int n) //把字符串s的前n个字符付给当前字符串`
> + `string& assign(const string &s) `
> + `string& assign(int n,char c)    //把n个字符c赋给当前字符串` 

> ```cpp
> #include <iostream>
> #include <string>
> using namespace std;
> 
> void test01()
> {
> 	string s1;
> 	s1 = "hello world";
> 	cout << "s1 = " << s1 << endl;
> 
> 	string s2;
> 	s2 = s1;
> 	cout << "s2 = " << s2 << endl;
> 
> 	string s3;
> 	s3 = 'n';
> 	cout << "s3 = " << s3 << endl;
> 
> 	string s4;
> 	s4.assign("hello C++");
> 	cout << "s4 = " << s4 << endl;
> 
> 	string s5;
> 	s5.assign("hello C++", 7);
> 	cout << "s5 = " << s5 << endl;
> 
> 	string temp;//????
> 	temp.assign(s4, 7);
> 	cout << "temp = " << temp << endl;
> 
> 	string s6;
> 	s6.assign(s4);
> 	cout << "s6 = " << s6 << endl;
> 	
> 	string s7;
> 	s7.assign(4, '9');
> 	cout << "s7 = " << s7 << endl;
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

#### 4.字符串拼接

功能：实现在字符串**末尾**拼接字符串

> 函数原型：
>
> + `string& operator+=(const char* str);`
> + `string& operator+=(const char c);`
> + `string& operator+=(const string& str);    //以上三个为重载+=运算符`
> + `string& append(const char* s);  //把字符串s连接到当前字符串结尾`
> + `string& append(const char* s,int n);  //把s的前n个字符连接到当前字符串结尾`
> + `string& append(const string& s);  //同operator+=`
> + `string& append(const string& s,int pos,int n);   //s中从pos开始的n个字符连接到字符串结尾`

> ```cpp
> #include <iostream>
> #include <string>
> using namespace std;
> 
> void test01()
> {
> 	string s1 = "hello";
> 	string s2 = " world";
> 	
> 	s1 += s2;
> 	cout << "s1 = " << s1 << endl;
> 
> 	char c = '!';
> 	s1 += c;
> 	cout << "s1 = " << s1 << endl;
> 
> 	s1 += "yysls";
> 	cout << "s1 = " << s1 << endl;
> }
> 
> void test02()
> {
> 	string s1 = "I";
> 	s1.append(" love");
> 	cout << "s1 = " << s1 << endl;
> 
> 	s1.append(" games daiofbog", 5);
> 	cout << "s1 = " << s1 << endl;
> 
> 	string s2 = " yysls";
> 	s1.append(s2);
> 	cout << "s1 = " << s1 << endl;
> 
> 	string s3 = " uncle jiang and shaodongjia";
> 	s1.append(s3, 0, 12);
> 	cout << "s1 = " << s1 << endl;
> }
> 
> int main()
> {
> //	test01();
> 
> 	test02();
> 
> 	return 0;
> }
> ```
>
> 

#### 5.查找和替换

+ 查找：查找指定字符串是否存在，若存在，返回**第一次出现的==起始==位置**；若不存在，返回-1；

+ 替换：在指定位置替换字符串

> 函数原型：
>
> + `int find(const string& str,int pos = 0) const;`
> + `int find(const char* s,int pos = 0) const;`
> + `int find(const string& str,int pos,int n) const;`
> + `int find(const char c,int pos = 0) const;`
> + 如果是rfind，则是从pos开始，查找**最后**的出现位置
> + `string& replace(int pos,int n,const string& str);  //替换从pos开始的n个字符为字符串str`
> + `string& replace(int pos,int n,const char* s);  //替换pos开始的n个字符为字符串s`



#### 6.字符串比较

> 函数原型：
>
> + `int compare(const string& s) const;`
> + `int compare(const char* s) const;`



#### 7.字符存取

string中单个字符存取方式有两种

+ `char& operator[](int n);`
+ `char& at(int n);`

> ```cpp
> //at的使用方式
> void test01()
> {
> 	string s1 = "yysls";
> 	s1.at(0) = 'x';
> 	cout << s1 << endl;
> 
> 	for (int i = 0; i < s1.size(); i++)
> 	{
> 		s1.at(i) = 'x' + i;
> 	}
> 	cout << s1 << endl;
> }
> ```
>
> 

#### 8.插入和删除

> 函数原型：
>
> + `string& insert(int pos,const char* s);`
> + `string& insert(int pos,const string& str); //从pos的位置插入字符串`
> + `string& insert(int pos,int n,char c); //在指定位置插入n个字符c`
> + `string& erase(int pos,int n = npos);  //删除从pos开始的n个字符`

> ```cpp
> void test01()
> {
> 	string s1 = "uncle jiang and shaodongjia";
> 	s1.insert(12, "aunt han ");
> 	cout << s1 << endl;
> 
> 	s1.erase(25, 8);
> 	cout << s1 << endl;
> }
> ```
>
> 



#### 9.获取子串

> 函数原型：
>
> + `string substr(int pos = 0,int n = npos) const; //返回由pos开始的n个字符串组成的字符串`

> ```cpp
> void test01()
> {
> 	string s1 = "I love yysls";
> 	string s2 = s1.substr(0,8);
> 	cout << s2 << endl;
> }
> 
> void test02()
> {
> 	string email = "zyujian@qq.com";
> 	int pos = email.find('@');
> 	string name = email.substr(0, pos);
> 	cout << name << endl;
> }
> ```
>
> 



### 3.2 vector容器

#### 1.vector基本概念

功能：vector数据结构和数组非常相似，也称为**单端数组**

与普通数组区别：数组是静态空间，而vector可以**动态扩展**

动态扩展：并不是在原空间之后续接新空间，而是<u>找更大的内存空间</u>，然后将原数据**拷贝**到新空间，释放原空间

![image-20251209103229699](C:/Users/张翔/AppData/Roaming/Typora/typora-user-images/image-20251209103229699.png)

+ vector容器的迭代器是支持随机访问的迭代器

#### 2.构造函数

> 函数原型：
>
> + `vector<T> v;       //采用模板实现 类实现，默认构造函数`
> + `vector<T>(v.begin(),v.end());   //将v[begin(),end())区间中的元素拷贝给本身`
> + `vector<T>(n,elem);   //构造函数将n个elem拷贝给本身`
> + `vector<T>(const vector &vec);  //拷贝构造`

> ```cpp
> #include <iostream>
> #include <vector>
> 
> using namespace std;
> 
> void showVector(vector<int>& v)
> {
> 	for (vector<int>::iterator it = v.begin(); it != v.end(); it++)
> 	{
> 		cout << *it << " ";
> 	}
> 	cout << endl;
> 
> }
> 
> void test01()
> {
> 	vector<int>v1;
> //	cout << v1.size() << endl;
> 	for (int i = 0;i < 10;i++)
> 	{
> 		v1.push_back(i);
> 	}
> 	showVector(v1);
> 
> 	vector<int>v2(v1.begin(), v1.end());
> 	showVector(v2);
> 
> 	vector<int>v3(4, 25);
> 	showVector(v3);
> 
> 	vector<int>v4(v2);
> 	showVector(v4);
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

#### 3.赋值操作

> 函数原型：
>
> + `vector& operator=(const vector &vec);`
> + `assign(v.beg(),v.end());`
> + `assign(n,elem);`

> ```cpp
> void test01()
> {
> 	vector<int>v1;
> 	for (int i = 0; i < 10; i++)
> 	{
> 		v1.push_back(i);
> 	}
> 	showVector(v1);
> 
> 	vector<int> v2;
> 	v2 = v1;
> 	showVector(v2);
> 
> 	vector<int> v3;
> 	v3.assign(4, 19);
> 	showVector(v3);
> 
> 	vector<int> v4;
> 	v4.assign(v2.begin(), v2.end());
> 	showVector(v4);
> }
> ```
>
> 

#### 4.容量和大小（capacity  /  size）

==capacity >= size==

> 函数原型：
>
> + `empty();  //判断容器是否为空`
> + `capacity();  //容器的容量`
> + `size();   /返回容器中元素的个数`
> + `resize(int num);`
> 	+ 重新指定容器长度为num，若容器变长，则以默认值(一般为0)填充新位置
> + `resize(int num,int elem);`
> 	+ 重新指定容器长度为num，若容器变长，则以elem值填充新位置

> ```cpp
> void test01()
> {
> 	vector<int>v1;
> 	for (int i = 0; i < 10; i++)
> 	{
> 		v1.push_back(i);
> 	}
> 	showVector(v1);
> 
> 	if (v1.empty())
> 	{
> 		cout << "v1 is empty" << endl;
> 	}
> 	else
> 	{
> 		cout << "v1 isn't empty" << endl;
> 	}
> 
> 	cout << v1.capacity() << endl << v1.size() << endl;
> 	v1.resize(16,'A');
> 	showVector(v1);
> 
> 	v1.resize(5);
> 	showVector(v1);
> }
> ```
>
> 

#### 5.插入和删除

> 函数原型：
>
> 插入：
>
> + `push_back(elem);`
> + `pop_back();`
> + `insert(const_iterator pos,ele); //迭代器指向位置pos插入元素ele`
> + `insert(const_iterator pos,int count, ele); //迭代器指向位置pos插入count个ele元素`
>
> 删除：
>
> + `erase(const_iterator pos); //删除迭代器指向的元素`
> + `erase(const_iterator start,const_iterator end); //删除迭代器从start到end之间的元素`
> + `clear(); //删除容器中所有元素`

> ```cpp
> void test01()
> {
> 	vector<int>v1;
> 	for (int i = 0; i < 10; i++)
> 	{
> 		v1.push_back(i);
> 	}
> 	showVector(v1);
> 
> 	v1.push_back(19);
> 	v1.push_back('z');
> 	showVector(v1);
> 
> 	v1.pop_back();
> 	showVector(v1);
> 
> 	v1.insert(v1.begin(), 25);
> 	showVector(v1);
> 
> 	v1.insert(v1.end(), 5, 9);
> 	showVector(v1);
> 
> 	v1.erase(v1.begin());
> 	showVector(v1);
> 
> //	v1.erase(v1.begin(), v1.end());
> 	v1.clear();
> 	showVector(v1);
> }
> ```
>
> 

#### 6.数据存取

> 函数原型：
>
> + `operator[]();`
> + `at(int idx);`
> + `front(); //返回容器中第一个数据元素`
> + `back(); //返回容器中最后一个元素`

> ```cpp
> void test01()
> {
> 	vector<int> v1;
> 	for (int i = 0; i < 10; i++)
> 	{
> 		v1.push_back(i);
> 	}
> 
> 	for (int i = 0; i < v1.size(); i++)
> 	{
> 		cout << v1[i] << " ";
> 	}
> 	cout << endl;
> 
> 	for (int i = v1.size()-1; i >= 0; i--)
> 	{
> 		cout << v1.at(i) << " ";
> 	}
> 	cout << endl;
> 
> 	cout << "v1.front() = " << v1.front() << endl;
> 	cout << "v1.back() = " << v1.back() << endl;
> }
> ```
>
> 

#### 7.互换容器

功能：实现两个容器内元素进行互换

> 函数原型：
>
> + `swap(vec); //将vec与本身的元素互换`

> ```cpp
> void test01()
> {
> 	vector<int> v1;
> 	for (int i = 0; i < 10; i++)
> 	{
> 		v1.push_back(i);
> 	}
> 	vector<int> v2;
> 	for (int i = 0; i < 20; i++)
> 	{
> 		if (i % 2 == 0)
> 		{
> 			v2.push_back(i);
> 		}
> 	}
> 	showVector(v1);
> 	showVector(v2);
> 
> 	v1.swap(v2);
> 	cout << "v1:";
> 	showVector(v1);
> 	cout << "v2:";
> 	showVector(v2);
> }
> ```

> **互换**最重要的应用是在==收缩vector容器的容量==
>
> ```cpp
> void test02()
> {
> 	vector<int> v1;
> 	for (int i = 0; i < 100000; i++)
> 	{
> 		v1.push_back(i);
> 	}
> 	cout << "v1.capacity = " << v1.capacity() << endl
> 		<< "v1.size = " << v1.size() << endl;
> }
> ```
>
> 当vector容器初始化的数据过多，编译器为了防止数据泄露，会分配过多的容量
>
> ![image-20251209155846916](C:/Users/张翔/AppData/Roaming/Typora/typora-user-images/image-20251209155846916.png)
>
> 此时如果重新resize容器，则会有非常多的内存被浪费
>
> ```cpp
> v1.resize(5);
> cout << "v1.capacity = " << v1.capacity() << endl
> 	<< "v1.size = " << v1.size() << endl;
> ```
>
> ![image-20251209160031225](C:/Users/张翔/AppData/Roaming/Typora/typora-user-images/image-20251209160031225.png)
>
> 如果有个vector容器，它的capacity与当前容器的size相等，两者swap之后，当前容器的capacity和size就相等了，不会浪费内存。而对于不需要的vector容器，则clear()就能清理了。
>
> ```cpp
> vector<int>(v1).swap(v1);
> ```
>
> 这是一个匿名对象的创建，可以先理解为`vector<int> x(v1).swap(v1);`使用拷贝构造，将v1的元素赋值到x中。此时x的size就是v1的数据个数，即5，所以x的capacity也是5。`.swap(v1)`则把x和v1进行交换，v1在内部数据不变的情况下，实现了capacity与size相等，而x尽管其capacity变的很大，但因为x是匿名对象，在该行代码执行完毕后，编译器清理x的代码，自动完成了内存清理。
>
> ![image-20251209161524680](C:/Users/张翔/AppData/Roaming/Typora/typora-user-images/image-20251209161524680.png)

#### 8.预留空间

功能：减少vector在动态扩展容量时的扩展次数

> 函数原型：
>
> + `reserve(int len);`
> 	+ 容器预留len个元素长度，预留位置不初始化，元素不可访问

> ```cpp
> void test02()
> {
> 	vector<int> v1;
> 	int num = 0;
> 	int* p = NULL;
> 
> 	v1.reserve(100000);//去掉reserve则num的值会达到30
> 	for (int i = 0; i < 100000; i++)
> 	{
> 		v1.push_back(i);
> 
> 		if (p != &v1[0])
> 		{
> 			p = &v1[0];
> 			num++;
> 		}
> 	}
> 	cout << "num = " << num << endl;
> }
> ```
>
> 

### 3.3 deque容器

#### 1.deque容器基本概念

**功能：**双端数组，可以对头端进行插入删除操作

deque的迭代器支持随机访问

deque和vector的区别

> + vector对于头部的插入/删除效率低，数据量越大，效率越低
> + deque相对而言，对头部的插入删除速度比vector快
> + vector访问元素时的速度会比deque快，这与两者内部实现有关



![image-20251219120333669](C:/Users/张翔/AppData/Roaming/Typora/typora-user-images/image-20251219120333669.png)

deque内部工作原理：

> deque内部有个中控器，维护每段缓冲区中的内容，缓冲区存放真实数据
>
> 中控器维护的是每个缓冲区的**地址**，使得使用deque时像一片连续的内存空间
>
> ![image-20251219120713027](C:/Users/张翔/AppData/Roaming/Typora/typora-user-images/image-20251219120713027.png)

#### 2.构造函数

函数原型：

> + `deque<T>;`
> + `deque(beg,end);`
> + `deque(n,elem);`
> + `deque(const deque &deq);`

#### 3.赋值操作

函数原型：

> + `deque& operator=(consst deque &deq);`
> + `assign(beg,end);`
> + `assign(n,elem);`

#### 4.大小操作

> + `deque.empty();`
> + `deque.size();`
> + `deque.resize(num);`
> + `deque.resize(num,elem);`

#### 5.插入和删除

函数原型：

> 两端插入操作：
>
> + `push_back(elem);` //尾部添加
> + `push_front(elem);` //头部插入
> + `pop_back();` //删除最后一个数据
> + `pop_front(); `//删除第一个数据

> 指定位置操作：
>
> + `insert(pos,elem);` //在pos位置插入一个elem元素的**拷贝**，==返回新数据的**位置**==
> + `insert(pos,n,elem);` //在pos位置插入n个elem数据，无返回值
> + `insert(pos,beg,end);` //在pos位置插入[beg,end]区间的数据，无返回值
> + `clear();` //清空所有数据
> + `erase(beg,end);` //删除[beg,end]区间的数据，==返回下一个数据的位置==
> + `erase(pos);`  删除pos位置的数据，==返回下一个数据的位置==

#### 6.数据存取

函数原型：

> + `at(int idx);`
> + `operator[];`
> + `front();`
> + `back();`

#### 7.排序

**算法：**`sort(iterator beg,iterator end);` //对beg和end区间内元素进行排序(升序)

对于支持<u>随机访问的迭代器的容器</u>，都可以使用sort进行排序



### 3.4 stack容器

**stack常用接口**

> 构造函数：
>
> + `stack<T> stk;`
> + `stack<T> s(const stack &stk);`

> 赋值操作：
>
> + `stack& operator=(const stack &stk);`

> 数据存取：
>
> + `push(elem);`
> + `pop();`
> + `top();`

> 大小操作：
>
> + `empty();`
> + `size();`

### 3.5 queue容器

> 构造函数：
>
> + `queue<T> que;`
> + `queue<T> q(const queue &que);`

> 赋值操作：
>
> + `queue& operator=(const queue &que);`

> 数据存取：
>
> + `push(elem);`
> + `pop();`
> + `back();`
> + `front();`

> 大小操作：
>
> + `empty();`
> + `size();`



### 3.6 list容器

#### 1.list基本概念

STL中的链表是一个**双向**链表



由于链表的储存方式不是连续的内存空间，因此链表中的迭代器只支持前移和后移，属于双向迭代器

#### 2.构造函数

函数原型：

> + `list<T> lst;`
> + `list(beg,end);`
> + `list(n,elem);`
> + `list(const list &lst);`















## 4.异常处理

> ```cpp
> #include <iostream>
> 
> using namespace std;
> 
> float divide(float a, float b)
> {
> 	if (b == 0)
> 	{
> 		throw runtime_error("Divide by zero");
> 	}
> 	return a / b;
> }
> 
> int main()
> {
> 	try
> 	{
> 		float c = divide(3.1, 2);
> 		cout << "c=" << c << endl;
> 	}
> 	catch(runtime_error& e)
> 	{
> 		cout << "exception:" << e.what() << endl;
> 	}
> 
> 	return 0;
> }
> ```
>
> 



> ```cpp
> 
> ```
>
> 