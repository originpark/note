# 内存分区

exe程序运行前存在代码区和全局区

- 代码区:只读的二进制代码
- 全局区:存放全局变量和静态变量,全局区同时包含了常量区

exe程序运行后出现了栈区和堆区

- 栈区:栈区存放局部变量和函数形参,函数执行完之后自动释放
- 堆区:存放动态开辟出来的数据,堆区的数据除非进程结束,否则不会自动释放





# new&delete

- new是在堆区创建数据
- 其返回值是创建出来的数据的地址
- 使用delete来释放堆区的数据



new基本数据类型

```c++
int* n = new int(10);//在堆区创建一个值为10的int类型数据
```



new数组

```c++
int* array = new int[10];//在堆区创建一个长度为10的int类型数组
```



delete基本数据类型

```cpp
delete n;
```



delete数组

```cpp
delete[] array;
```





# 引用

引用就是给变量起别名,其本质是一个指针常量(指向不能改变的指针),把指针理解成cpp帮我们封装好的一个功能就好了

```cpp
int a = 10;
int &b = a;//相当于int* const b = &a;
b = 20;//相当于*b = 20;
```



可以用const修饰一个引用表示引用值不可修改





# 函数



## 参数默认值

形参可以设置默认值

- 如果某个位置参数有默认值，那么从这个位置往后，从左向右，必须都要有默认值
- 如果函数声明有默认值，函数实现的时候就不能有默认参数





## 占位参数

如下

```cpp
void test(int a, int);
```

- 在传参的时候占位参数必须填补
- 占位参数可以有默认值







# 对象初始化与销毁



## 析构函数

当对象被销毁时自动调用析构函数,因此

```cpp
int main() 
{
    Person p;//主线程结束时,栈区空间自动释放,调用~Person()
    Person* p = new Person();//主线程结束时,堆区空间不会自动释放,不调用~Person()
    return 0;
}
```





## 拷贝构造函数



语法

```cpp
Person(const Person &p) {}
```





### 调用时机

当对象作为参数传递时,会调用拷贝函数进行值传递

```cpp
void test01(Person p) {
}

int main() {
	Person p;
	test01(p);//先拷贝一个对象副本给形参,然后调用Person的拷贝函数
	return 0;
}
```



当创建对象是传入一个同类型的对象实例时,会调用拷贝函数

```cpp
Person p1(10);
Person p2(p1);//调用拷贝函数
```



以值的方式返回局部对象

```cpp
Person test02() {
	Person p(300);
	return p;//这里的p是个局部变量,函数结束后就会被销毁,因此这里返回的是p的一个拷贝,会调用拷贝函数
}
```









### 注意事项

不要用拷贝构造函数初始化匿名对象,例如

```cpp
Person(p);//错误,等价于Person p;
```



如果我们不写拷贝函数,cpp会默认给我们提供一个拷贝函数

如果我们写了拷贝函数,那么就不再提供默认的无参构造函数







### 浅拷贝&深拷贝

编译器为我们提供的默认拷贝构造函数属于浅拷贝



例如有以下类

```cpp
class Person {
private:
    int age;
    int* height;
public:
    Person(int age, int height) {
        this->age = age;
        this->height = new int(height);
    }
};
```



那么编译器为我们提供的默认拷贝构造函数就会像是这样子

```cpp
Person(const Person& p) {
    this->age = p.age;
    this->height = p.height;//直接拷贝指针本身
}
```

不难发现对于指针height,默认的拷贝构造函数只是单纯地拷贝的指针本身的值

这会带来一个问题,这里的height属于堆区空间,需要手动调用delete height来释放,由于是浅拷贝,因此所有拷贝出来的对象的height属性都指向同一个位置,必然会造成重复delete的问题



使用深拷贝来解决重复delete的问题,即对于指针属性,不是拷贝指针本身,而是拷贝指针所指向的数据,因此我们手动实现拷贝构造函数

```cpp
Person(const Person& p) {
    this->age = p.age;
    this->height = new int(*p.height);//,重新在堆区开辟一块空间,拷贝指针所向的值
}
```









## 构造函数

1. 当调用无参构造的时候不用加括号,例如Person p; 否则会被看成函数声明

2. 可以显示调用构造函数来创建对象: Person p = Person();这里的Person()如果不被赋值看成是一个匿名对象,匿名对象在当前语句执行完之后就会被立即销毁,例如

   ```cpp
   Person();//这一行执行完之后,这个匿名对象就被释放了
   ```





### 初始化列表

```cpp
Person(int age, string name):age(age), name(name) {
}
```





## static

类的static属性需要在类外进行初始化

```cpp
int Person::staticVairable = 1000;
```



通过类名访问static成员

```cpp
cout << Person::staticVairable << endl;
```





# 类的对象实例的大小



空类,占一个字节,用以标识这是一个类的对象实例

```cpp
//占一个字节
class Person {
}
```



非静态成员变量,属于这个类的对象实例

```cpp
//占四个字节
class Person {
public:
    int a;
}
```



static成员,不属于这个类的对象实例

非静态成员函数,不属于这个类的对象实例,因此可以用类的空指针来调用没有使用属性的成员函数

静态成员函数,不属于这个类的对象实例上







# const



## 常函数

在成员函数的括号后面加const,表示是一个常函数

```cpp
void fun() const {
    
}
```



常函数中不可修改成员变量的值

常函数的本质:this指针本质是一个 Type* const p,即指针常量,使普通函数变成常函数就是使this变为

const Type* const p,即不能改变指向,也不能改变指向的值的指针



如果我们想在常函数中修改某个成员变量的值,可以用mutable关键字修饰该成员变量

```cpp
mutable int a;
```





## 常对象

用const修饰创建出来的对象,就是一个常对象

```cpp
const Person p;
```



常对象的成员不能被修改(mutable修饰的成员除外)

常对象只能调用常函数





# 友元



## 全局函数做友元

在类中的最上方使用friend关键字和函数声明定义友元全局函数,友元全局函数可以访问类的private成员

```cpp
class Person {
	friend void friendMethod();
private:
	void m1() {
		cout << "m1();" << endl;
	}
};


void friendMethod() {
	Person p;
	p.m1();
}
```





## 类做友元

在类中最上方使用friend关键字声明友元类,友元类可以访问本类的私有成员

```cpp
class Person {
    friend class GoodGay;
}
```





## 成员函数做友元

是其他类的某个方法中可以访问本类的私有成员

```cpp
friend void FriendClass::fun();
```





# 运算符重载



## 原理

定义operator开头的函数,有两种方式

一是在类中,即通过对象调用:obj1.operatorx(obj2),这种写法最终能重载obj1 x obj2这种效果

另一种写成全局函数:operatorx(obj1, obj2),最终实现obj1 x obj2







## 加法

```cpp
#include<iostream>
using namespace std;

class Person {
public:
	int a;
	int b;
	Person(int a, int b) {
		this->a = a;
		this->b = b;
	}
	Person operator+(const Person& p) {//成员函数重载+
		int a = this->a + p.a;
		int b = this->b + p.b;
		Person person(a, b);
		return person;
	}
    
    Person operator(const Person& p, int n) {//运算符函数也可以重载
    }
};

//全局函数实现 + 号运算符重载
//Person operator+(const Person& p1, const Person& p2) {
//	
//	int a = p1.a + p2.a;
//	int b = p1.b + p2.b;
//	Person person(a, b);
//  return person;
//}


void test01() {
	Person p1(10, 20);
	Person p2(20, 10);
	Person p3 = p1 + p2;//实质是Person p3 = p1.operator+(p2);
	cout << p3.a << ", " << p3.b << endl;
}
int main() {
	test01();
	return 0;
}
```





## 左移运算符

<<运算符一般就是用来重载输出功能的,因此与cout这个输出流一起使用

<<运算符不适合在类中定义operator函数,因为这样的话调用的时候只能是person.operateor<<(cout),这样的话就会变成person << cout,而我们希望cout在左边,因此考虑使用全局函数

```cpp
//全局函数就能实现cout在左,person在右,即cout << person
ostream& operator<<(ostream& cout, Person& person) {
	cout << "a = " << person.a << ", b = " << person.b;
	return cout;//返回cout是为了链式编程(后面接多个<<)
}
```



重载左移运算符就类似于实现java中的toString方法





## 递增运算符



实现对自定义整形类的递增

```cpp
#include<iostream>
using namespace std;

class Integer {
	friend ostream& operator<<(ostream& cout, Integer integer);
private:
	int n;
public:
	Integer() {
		n = 0;
	}

	Integer& operator++() {//无参表示前置++
		n++;
		return *this;
	}

	Integer operator++(int) {//有一个占位int表示后置++
		Integer integer = *this;//先将对象保存
		n++;//++
		return integer;//返回保存的数据
	}

	
};

ostream& operator<<(ostream& cout, Integer integer) {
	cout << integer.n;
	return cout;
}

int main() {
	Integer integer;
	cout << integer++ << endl;
	cout << integer << endl;
	return 0;
}
```





## 赋值运算符

编译器为我们创建的类提供了四个默认方法:无参构造器,析构函数,拷贝函数,operator=(),这个operator拷贝函数一样也是浅拷贝,也会发生重复delete问题

因此我们需要承载=

```cpp
#include<iostream>
using namespace std;

class Test {
public:
	int* height;
	Test(int height) {
		this->height = new int(height);
	}

	Test& operator=(Test& t) {//重载=
        //将t拷贝给当前对象,因此要先将当前对象在堆区开辟的空间释放掉
		if (height != NULL) {
			delete height;
			height = NULL;
		}
        //深拷贝
		height = new int(*t.height);
        //返回复制操作后的左值,即当前对象
		return *this;
	}

	~Test() {
		if (height != NULL) {
			delete height;
			height = NULL;
		}
	}
};

ostream& operator<<(ostream& out, Test& test) {
	out << *test.height;
	return out;
}


int main()
{
	Test test1(10);
	Test test2(20);
	Test test3(30);
	test1 = test2 = test3;
	cout << test1 << endl;
	cout << test2 << endl;
	cout << test3 << endl;
	return 0;
}
```





## 关系运算符

有==, <, >, !=

返回一个bool

```cpp
bool operator==(obj obj) {
}
```





## 函数调用运算符

函数调用运算符就是`()`,也能被重载



```cpp
#include<iostream>
#include<string>
using namespace std;

class Test {
public:
	void operator()(string str) {//函数调用运算符重载
		cout << str << endl;
	}
};

int main()
{
	Test test;
	test("hello, world.");//调用
	return 0;
}
```









# 继承



## 语法

```cpp
class Son: 继承方式 Father {
}
```





## 继承方式

有三种继承方式

- 公有继承: 除了父类的私有属性,其他属性都能访问
- 保护继承: 除了父类的私有属性,其他属性都能访问,只不过所有属性的访问修饰符都被修改为了protected
- 私有继承: 除了父类的私有属性,其他属性都能访问,只不过所有属性的访问修饰符都被修改为了private







## 构造和析构执行顺序

对于构造函数:先执行父类,再执行本类

对于析构函数:先执行本类,再执行父类





## 同名成员

子类继承时会将父类所有的非静态属性都拷贝过来,因此,如果父类和子类有一个同名的属性的话,那么在子类中父类的这个同名属性不会被覆盖,而是被隐藏



父类和子类中定义了一个同名的成员a,则通过子类访问父类中的a

```cpp
son.Father::a
```





## 虚继承

b和c继承a,d继承b和c,a中有一个x属性,那么在d中,就有分别来自b和来自c的两份x,没有必要

解决方式是使用关键字virtual修饰b和c,此时b和c在继承a的x时不是拷贝一份,而是维护一个指向a的x属性的指针,这样,d在继承b和c的时候,继承的就是b和c维护的指针所指向的值,就是单独的一个x





# 多态

cpp中的多态分为静态和动态绑定



默认情况下是静态的,例如b继承a,重写了a的x方法,当用a的引用接收b的对象,调用x方法时,实际调用的仍然是a的x方法,即在编译阶段就确定了这个函数的地址



使用virtual来修饰a的x函数后,这个x函数就变成了一个虚函数,这样就能实现动态绑定的效果



注意cpp使用多态时必须用父类的指针或引用去接收子类,不能直接用父类本身来接收子类



virtual修饰函数实现动态绑定的原理是:当用virtual修饰父类函数后,父类就会维护一个vftable,该table中有一个该虚函数的指针

当子类继承父类时,同时也会继承该table和table中的父类虚函数的指针,子类重写该虚函数后,就会将子类table中的虚函数地址改为重写后的函数地址,这样在调用的时候就是调用的重写函数





# 抽象类

纯虚函数

```cpp
virtual void fun() = 0;
```



有纯虚函数的类就默认成为抽象类





# 虚析构和纯虚析构

普通情况下,delete p ,就会调用该对象的析构函数

但是当该p是父类的指针或引用来维护一个子类对象的时候,delete p就无法调用子类的析构函数

解决方法是将析构函数变为虚析构





# 模板

就是泛型



## 函数模板

定义函数模板

```cpp
template<typename T>
void mySwap(T& a, T& b) {
	T temp = a;
	a = b;
	b = temp;
}
```



调用模板函数,两种方式都可以

```cpp
mySwap(a, b);
mySwap<int>(a, b);
```



注意:

如果普通函数和模板函数重载了,优先调用普通函数,可以通过同模板的方式强制调用模板函数

即`fun<>();`



给模板定义特殊情况

```cpp
template<typename T>
bool size(T& o1, T& o2) {
	if (o1 >= o2) {
		return true;
	}
	else {
		return false;
	}
}

template<> bool size(Person &p1, Person &p2) {//上述模板当传入两个Person对象时会调用这个函数
	return p1.name > p2.name;
}
```





## 类模板



```cpp
#include<iostream>
#include<string>

using namespace std;

template<class type1, class type2>
class Temp {
	type1 n1;
	type2 n2;
public:
	Temp(type1 n1, type2 n2) {
		this->n1 = n1;
		this->n2 = n2;
	}
};


int main()
{
	Temp<int, string> t1(10, "100");
	return 0;
}
```



模板参数列表中可以有默认类型,例如

```cpp
template<class type1, class type2 = string>
```



这样在创建对象的时候只需要声明type1的类型就好了









类模板中的成员函数是在被调用时才会创建





### 模板对象作参数

有三种方式,推荐第一种

```cpp
void test02(Temp<int, string> t1) {
	t1.show();
}

template<typename T1, typename T2>
void test02(Temp<T1, T2> t) {
	t.show();
}

template<typename T>
void test03(T& t) {
	t.show();
}
```



### 继承模板类

如果一个类继承了模板类,则必须要指定其父类的模板类型

```cpp
class Son: public Father<int> {
}
```



如果不想在子类中指定父类的模板类型,则可以将子类也声明为模板类

```cpp
template<typename K>
class Son: public Father<K> {
}
```





## 模板类的成员函数的类外实现

```cpp
template<typename T>
void person<T>::fun() {
}
```

