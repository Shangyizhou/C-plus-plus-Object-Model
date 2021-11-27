## this指针调整

在c++中多继承过程，根据访问不同的父类成员变量或者是成员函数，同一个实例对象会出现不同的基址（对象的地址，类似于你在不同的场合就会有身份的转换，在家的身份，在学校，在公司的等等），这种现象叫做this指针基址调整。

```C
#include <iostream>
using namespace std;

class A
{
public:
	A() {
		printf("A::A()的this指针是：%p\n", this);
	}
	void funcA() { 
		printf("A::funcA：this = %p\n", this);
	}
	int a;
};

class B
{
public:
	B() {
		printf("B::B()的this指针是：%p\n", this);
	}
	void funcB() {
		printf("B::funcB：this = %p\n", this);
	}
	int b;
};

class C : public A, public B
{
public:
	C() {
		printf("C::C()的this指针是：%p\n", this);
	}
	void funcC() {
		printf("C::funcC：this = %p\n", this);
	}
	int c;
};

int main()
{
	cout << sizeof(A) << endl;
	cout << sizeof(B) << endl;
	cout << sizeof(C) << endl;
	C myc;
	myc.funcA();
	myc.funcB();
	myc.funcC();
}
```

````C
4
4
12
A::A()的this指针是：0019FBD0
B::B()的this指针是：0019FBD4
C::C()的this指针是：0019FBD0
A::funcA：this = 0019FBD0
B::funcB：this = 0019FBD4
C::funcC：this = 0019FBD0
````

**观察得知**  

- myc的`sizeof`是12，因为C类继承了A类和B类，所以C类也有A类和B类的成员，算上C类自己则有三个整型成员变量，所以为12字节
- 接着行的结果是执行代码行 myc; 所致 这里要注意，执行的个类的构造函 数，父类(基类 )A 类构造函数的 this 指针与父类 类构造函数的 this 指针不同，差了4个字节，而类构造函数的 this 指针与类构造函数的 this 指针相同.

**在执行funcB的时候，this指针从原来的 0019FBD0 调整到 0019FBD4，这属于编译器内部自动调整这个this指针，而在执行funcC时，this指针又调回到 0019FBD0**

子类先继承了A类再继承了B类，所以子类C的this指针才跟父类A一样。为了获取B的成员变量，这时this指针需要偏移字节数为`sizeof(A)`，所以A,B的地址差了4个字节

![image-20211125222036582](https://syz-picture.oss-cn-shenzhen.aliyuncs.com/image-20211125222036582.png)

## 参考

- [C++幕后故事(一) --对象模型this指针调整 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/80794001)

