---
title: C++中的各种可调用对象
tags:
  - C++
url: 735.html
id: 735
categories:
  - C/C++
  - 程序开发语言
date: 2018-04-24 19:11:42
---

## 概述
一组执行任务的语句都可以视为一个函数，一个可调用对象。在程序设计的过程中，我们习惯于把那些具有复用性的一组语句抽象为函数，把变化的部分抽象为函数的参数。

函数的使用能够极大的极少代码重复率，提高代码的灵活性。

C++中具有函数这种行为的方式有很多。就函数调用方式而言
```cpp
func(param1, param2);
```
这儿使用`func`作为函数调用名，`param1`和`param2`为函数参数。在C++中就`func`的类型，可能为：

- 普通函数
- 类成员函数
- 类静态函数
- 仿函数
- 函数指针
- lambda表达式 C++11加入标准
- std::function C++11加入标准

下面就这几种函数展开介绍

## 简单函数形式
### 普通函数
这种函数定义比较简单，一般声明在一个文件开头。如下：
```cpp
#include <iostream>

// 普通函数声明和定义
int func_add(int a, int b) { return a + b; }

int main()
{
    int a = 10;
    int b = 20;
    int sum = func_add(a, b);
    std::cout << a << "+" << b << "is : " << sum << std::endl;
    getchar();
    return 0;
}
```

### 类成员函数
在一个类class中定义的函数一般称为类的方法，分为成员方法和静态方法，区别是成员方法的参数列表中隐含着类this指针。
```cpp
#include <iostream>

class Calcu
{
public:
	int base = 20;
	// 类的成员方法，参数包含this指针
	int class_func_add(const int a, const int b) const { return this->base + a + b; };
	// 类的静态成员方法，不包含this指针
	static int class_static_func_add(const int a, const int b) { return a + b; };
};

int main(void) 
{
	Calcu obj;
	int a = 10;
	int b = 20;

	// 类普通成员方法调用如下
	obj.class_func_add(a, b);

	// 类静态成员方法调用如下
	obj.class_static_func_add(a, b);
	Calcu::class_static_func_add(a, b);

	getchar();
	return 0;
}
```

### 仿函数
仿函数是使用类来模拟函数调用行为，我们只要重载一个类的operator()方法，即可像调用一个函数一样调用类。这种方式用得比较少。
```cpp
class ImitateAdd
{
public:
	int operator()(const int a, const int b) const { return a + b; };
};

int main()
{
    // 首先创建一个仿函数对象，然后调用()运算符模拟函数调用
    ImitateAdd imitate;
    imitate(5, 10);

	getchar();
	return 0;
}
```

## 函数指针
顾名思义，函数指针可以理解为指向函数的指针。可以将函数名赋值给相同类型的函数指针，通过调用函数指针实现调用函数。

函数指针是标准的C/C++的回调函数的使用解决方案，本身提供了很大的灵活性。
```cpp
#include <iostream>

// 声明一个compute函数指针，函数参数为两个int型，返回值为int型
int (*compute)(int, int);

int max(int x, int y) { return x >= y ? x : y; }
int min(int x, int y) { return x <= y ? x : y; }
int add(int x, int y) { return x + y; }
int multiply(int x, int y) { return x * y; }

// 一个包含函数指针作为回调的函数
int compute_x_y(int x, int y, int(*compute)(int, int))
{
	// 调用回调函数
	return compute(x, y);
}

int main(void) 
{
	int x = 2; 
	int y = 5;
	std::cout << "max: " << compute_x_y(x, y, max) << std::endl; // max: 5
	std::cout << "min: " << compute_x_y(x, y, min) << std::endl; // min: 2
	std::cout << "add: " << compute_x_y(x, y, add) << std::endl; // add: 7
	std::cout << "multiply: " << compute_x_y(x, y, multiply) << std::endl; // multiply: 10
	
	// 无捕获的lambda可以转换为同类型的函数指针
	auto sum = [](int x, int y)->int{ return x + y; };
	std::cout << "sum: " << compute_x_y(x, y, sum) << std::endl; // sum: 7
	
    getchar();
    return 0;
}
```

## Lambda函数
### Lambda函数定义
Lambda函数，又可以称为Lambda表达式或者匿名函数，在C++11中加入标准。定义形式如下：
```
[captures] (params) -> return_type { statments;} 
```
其中：

- `[captures]`为捕获列表，用于捕获外层变量
- `(params)`为匿名函数参数列表
- `->return_type`指定匿名函数返回值类型
- `{ statments; }`部分为函数体，包括一系列语句

需要注意：

- 当匿名函数没有参数时，可以省略`(params)`部分
- 当匿名函数体的返回值只有一个类型或者返回值为void时，可以省略`->return_type`部分
- 定义匿名函数时，一般使用`auto`作为匿名函数类型

下面都是有效的匿名函数定义
```cpp
auto func1 = [](int x, int y) -> int { return x + y; }; 
auto func2 = [](int x, int y) { return x > y; }; // 省略返回值类型
auto func3 = [] { global_ip = 0; }; // 省略参数部分
```

### Lambda函数捕获列表
为了能够在Lambda函数中使用外部作用域中的变量，需要在`[]`中指定使用哪些变量。

下面是各种捕获选项：

- `[]` 不捕获任何变量
- `[&]` 捕获外部作用域中所有变量，并作为引用在匿名函数体中使用
- `[=]` 捕获外部作用域中所有变量，并拷贝一份在匿名函数体中使用
- `[x, &y]` x按值捕获, y按引用捕获
- `[&, x]` x按值捕获. 其它变量按引用捕获
- `[=, &y]` y按引用捕获. 其它变量按值捕获
- `[this]` 捕获当前类中的this指针，如果已经使用了&或者=就默认添加此选项

**只有lambda函数没有指定任何捕获时，才可以显式转换成一个具有相同声明形式函数指针**
```cpp
auto lambda_func_sum = [](int x, int y) { return x + y; }; // 定义lambda函数
void (*func_ptr)(int, int) = lambda_func_sum; // 将lambda函数赋值给函数指针
func_ptr(10, 20);  // 调用函数指针
```

## std::function函数包装
### std::function定义
`std::function`在C++11后加入标准，可以用它来描述C++中所有可调用实体，它是是可调用对象的包装器，声明如下：
```cpp
#include <functional>

// 声明一个返回值为int，参数为两个int的可调用对象类型
std::function<int(int, int)> Func;
```
使用之前需要导入`<functional>`库，并且通过std命名空间使用。

### 其他函数实体转化为std::function
`std::function`强大的地方在于，它能够兼容所有具有相同参数类型的函数实体。

相比较于函数指针，`std::function`能兼容带捕获的lambda函数，而且对类成员函数提供支持。
```cpp
#include <iostream>
#include <functional>

std::function<int(int, int)> SumFunction;

// 普通函数
int func_sum(int a, int b)
{
	return a + b;
}

class Calcu
{
public:
	int base = 20;
	// 类的成员方法，参数包含this指针
	int class_func_sum(const int a, const int b) const { return this->base + a + b; };
	// 类的静态成员方法，不包含this指针
	static int class_static_func_sum(const int a, const int b) { return a + b; };
};

// 仿函数
class ImitateAdd
{
public:
	int operator()(const int a, const int b) const { return a + b; };
};

// lambda函数
auto lambda_func_sum = [](int a, int b) -> int { return a + b; };

// 函数指针
int (*func_pointer)(int, int);

int main(void) 
{
	int x = 2; 
	int y = 5;

	// 普通函数
	SumFunction = func_sum;
	int sum = SumFunction(x, y);
	std::cout << "func_sum：" << sum << std::endl;

	// 类成员函数
	Calcu obj;
	SumFunction = std::bind(&Calcu::class_func_sum, obj, 
		std::placeholders::_1, std::placeholders::_2); // 绑定this对象
	sum = SumFunction(x, y);
	std::cout << "Calcu::class_func_sum：" << sum << std::endl;

	// 类静态函数
	SumFunction = Calcu::class_static_func_sum;
	sum = SumFunction(x, y);
	std::cout << "Calcu::class_static_func_sum：" << sum << std::endl;

	// lambda函数
	SumFunction = lambda_func_sum;
	sum = SumFunction(x, y);
	std::cout << "lambda_func_sum：" << sum << std::endl;

	// 带捕获的lambda函数
	int base = 10;
	auto lambda_func_with_capture_sum = [&base](int x, int y)->int { return x + y + base; };
	SumFunction = lambda_func_with_capture_sum;
	sum = SumFunction(x, y);
	std::cout << "lambda_func_with_capture_sum：" << sum << std::endl;

	// 仿函数
	ImitateAdd imitate;
	SumFunction = imitate;
	sum = SumFunction(x, y);
	std::cout << "imitate func：" << sum << std::endl;

	// 函数指针
	func_pointer = func_sum;
	SumFunction = func_pointer;
	sum = SumFunction(x, y);
	std::cout << "function pointer：" << sum << std::endl;

	getchar();
	return 0;
}
```
最后的输出如下：
```
func_sum：7
Calcu::class_func_sum：27
Calcu::class_static_func_sum：7
lambda_func_sum：7
lambda_func_with_capture_sum：17
imitate func：7
function pointer：7
```
其中需要注意对于类成员函数，因为类成员函数包含this指针参数，所以单独使用`std::function`是不够的，还需要结合使用`std::bind`函数绑定this指针以及参数列表。

### std::bind参数绑定规则
在使用std::bind绑定**类成员函数**的时候需要注意绑定参数顺序：
```cpp
// 承接上面的例子
SumFunction = std::bind(&Calcu::class_func_sum, obj, 
		std::placeholders::_1, std::placeholders::_2);
SumFunction(x, y);
```

- 第一个参数为类成员函数名的引用（推荐使用引用）
- 第二个参数为this指针上下文，即特定的对象实例
- 之后的参数分别制定类成员函数的第1,2,3依次的参数值
- 使用`std::placeholders::_1`表示使用调用过程的第1个参数作为成员函数参数
- `std::placeholders::_n`表示调用时的第n个参数

看下面的例子：
```cpp
// 绑定成员函数第一个参数为4，第二个参数为6
SumFunction = std::bind(&Calcu::class_func_sum, obj, 4, 6);
SumFunction(); // 值为 10

// 绑定成员函数第一个参数为调用时的第一个参数，第二个参数为10
SumFunction = std::bind(&Calcu::class_func_sum, obj, std::placeholders::_1, 10);
SumFunction(5); // 值为 15

// 绑定成员函数第一个参数为调用时的第二个参数，第一个参数为调用时的第二个参数
SumFunction = std::bind(&Calcu::class_func_sum, obj, std::placeholders::_2, std::placeholders::_1);
SumFunction(5, 10); // 值为 15
```

对于非类成员对象，一般直接赋值即可，会自动进行转换并绑定参数，当然也可以使用`std::bind`指定参数绑定行为；
```cpp
#include <iostream>
#include <functional>

// 按照顺序输出x, y, x
void print_func(int x, int y, int z)
{
	std::cout << x << " " << y << " " << z << std::endl;
}
std::function<void(int, int, int)> Func;

int main()
{
	Func = std::bind(&print_func, 1, 2, 3);
	Func(4, 5, 6); // 输出： 1 2 3

	Func = std::bind(&print_func, std::placeholders::_2, std::placeholders::_1, 3);
	Func(1, 2, 7); // 输出： 2 1 3

	int n = 10;
	Func = std::bind(&print_func, std::placeholders::_1, std::placeholders::_1, n);
	Func(5, 6, 7); // 输出： 5 5 10
    
    getchar();
    return 0;
}
```

**需要注意：就算是绑定的时候指定了默认参数，在调用的时候也需要指定相同的参数个数（虽然不会起作用），否则编译不通过。**

## 关于回调函数
回调就是通过把函数等作为另外一个函数的参数的形式，在调用者层指定被调用者行为的方式。

通过上面的介绍，我们知道，可以使用函数指针，以及`std::function`作为函数参数类型，从而实现回调函数：
```cpp
#include <iostream>
#include <functional>

std::function<int(int, int)> ComputeFunction;
int (*compute_pointer)(int, int);

int compute1(int x, int y, ComputeFunction func) {
    // do something
	return func(x, y);
}

int compute2(int x, int y, compute_pointer func)
{
    // do something
	return func(x, y);
}
// 调用方式参考上面关于函数指针和std::function的例子
```
以上两种方式，对于一般函数，简单lambda函数而言是等效的。

但是如果对于带有捕获的lambda函数，类成员函数，有特殊参数绑定需要的场景，则只能使用`std::function`。

其实还有很多其他的实现回调函数的方式，如下面的标准面向对象的实现：
```cpp
#include <iostream>

// 定义标准的回调接口
class ComputeFunc
{
public:
	virtual int compute(int x, int y) const = 0;
};

// 实现回调接口
class ComputeAdd : public ComputeFunc
{
public:
	int compute(int x, int y) const override { return x + y; }
};

int compute3(int x, int y, const ComputeFunc& compute)
{
	// 调用接口方法
	return compute.compute(x, y);
}

// 调用方法如下
int main()
{
    ComputeAdd add_func; // 创建一个调用实例
    int sum = compute3(3, 4, add_func); // 传入调用实例
}
```
面向对象的方式更加灵活，因为这个回调的对象可以有很复杂的行为。

以上三种方法各有各的好处，根据你需要实现的功能的复杂性，扩展性和应用场景等决定使用。

另外，这些函数类型的参数可能为空，在调用之前，应该检查是否可以调用，如检查函数指针是否为空。