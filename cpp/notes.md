# notes

## 内存模型

代码区: 存放二进制代码,由OS管理

全局区: 存放全局变量、静态变量、常量

栈区: 由编译器自动分配释放、存放函数的参数值、局部变量

堆区: 由程序员分配和释放，若程序员不释放,程序结束有OS回收

### 程序运行前

程序编译后,生成exe可执行程序, **未执行该程序前**分为两个区域

**1. 代码区：**

 存放 CPU 执行的机器指令

 代码区是**共享**的，共享的目的是对于频繁被执行的程序(比如可以双开的模拟器)，只需要在内存中有一份代码即可

 代码区是**只读**的，使其只读的原因是防止程序意外地修改了它的指令

**2. 全局区：**

 **全局变量**(1)和**静态变量**(2)存放在此.

 全局区还包含了**常量区**(3), **字符串常量**和**其他常量**(const修饰的全局常量)也存放在此.

|        |            |                                 |
| :----- | :--------- | :------------------------------ |
|        | 1.全局变量 |                                 |
| 全局区 | 2.静态变量 |                                 |
|        | 3.常量区   | 字符串常量、const修饰的全局常量 |
|        |            |                                 |

 **该区域的数据在程序结束后由操作系统释放.**

### 程序运行后

 **栈区：**

 由编译器自动分配释放, 存放函数的参数值,局部变量等

 注意事项：不要返回局部变量的地址，栈区开辟的数据由编译器自动释放

示例：

```c++
int * func()
{
	int a = 10;
	return &a;
}

int main() {

	int *p = func();

	cout << *p << endl;
	cout << *p << endl;

	system("pause");

	return 0;
}
```

 **堆区：**

 由程序员分配释放,若程序员不释放,程序结束时由操作系统回收

 在C++中主要利用new在堆区开辟内存

## 运算符重载

- 有四个操作符不能被重载:  `::` `.`  `.*`   `?:` 
- 新的运算符 `**`  `<>`  `or`  `&|`也不能被创建
- 

## 为什么 List 不能使用 ::sort() 排序？

::sort()排序时 使用的是 RandomAcessItertor 随机访问迭代器，迭代时可以随机的+?  -? 运算，但是list 是不连续的空间，只能+1 -1来进行迭代，所以只能使用它自带的sort()算法



## new 关键字

```
int* b = new int[50]  // 200bytes,只是分配空间
Entity* e = new Entity(); // new 分配空间，()调用构造函数
// Entity* e = new(b) Entity(); //new(b),决定内存的位置,在b的分配空间里初始化

delete e;
delete[] b;


```



## 隐式转换

```
class Entity{
public:
	std::string m_name;
	int m_age;
	
	Entity(const std::string& name) : m_name(name),m_age(-1){}
	Entity(int age) : m_name("unkown"),m_age(age){}
}

void printEntity(const Entity& e){
	//todo
}

int main(){
	printEntity(22); // 22隐式调用构造函数
	//printEntity("aaa") //错误，构造函数的参数类型为std::string,而这里"aaa"是char[4] 
	printEntity(std::string("aaa"));
	printEntity(Entity("aaa"));
	
    Entity a("aaa");
    Entity b(22);
    //Entity a = Entity("aaa");
    //Entity b = Entity(22);

    Entity a = "aaa";	//"aaa"隐式调用构造函数
    Entity b = 22		// 22隐式调用构造函数


}


```

`explicit  ...构造函数`，表明构造函数只能显示调用，不能被隐式转换了。



## smart pointer

unique_ptr

范围指针，不可以复制，只指向一个内存空间，超出使用范围制动析构

使用 make_unique（c++14）

shared_ptr

范围指针，可以复制，每复制一个，计数器+1，其中一个复制对象超出使用范围-1，当shared_ptr的计数器=0，所有这些对象被析构



## 类型转换

推荐使用`static_cast、const_cast、reinterpret_cast、dynamic_cast`等方法的类型转换。

| 关键词           |                             说明                             |
| :--------------- | :----------------------------------------------------------: |
| static_cast      |        用于良性转换，一般不会导致意外发生，风险很低。        |
| const_cast       |  用于 const 与非 const、volatile 与非 volatile 之间的转换。  |
| reinterpret_cast | 高度危险的转换，这种转换仅仅是对二进制位的重新解释，不会借助已有的转换规则对数据进行调整，但是可以实现最灵活的 C++ 类型转换。 |
| dynamic_cast     |      借助 RTTI，用于类型安全的向下转型（Downcasting）。      |



## 多线程

 当不需要新建的线程立即执行是，使用std::async

- std::thread 的析构函数需要注意：如果该线程未结束(joinable() == true)而且也不是detach()的，会报异常

- std::thead::hardware_concurrency()  静态函数可以获得当前系统的cpu核心数
- 

额外知识 ：yield()  这个底层函数，先看一个有问题的情景：一个进程里的线程A 需要等线程B完成，busy waiting状态（耗费cpu资源），但cpu单核，就会出现线程A等线程B完成，但线程B永远不会被调度。这个时候yield()就可以解决问题，线程A告诉cpu，先别管我看看有没有其他的线程需要执行，过会再来执行我

yield()使用情景：你使用了忙等（busy waiting），等待的时间有比较长，又想降低cpu资源，就可以使用yield()。sleep()也可以，但sleep需要告诉它一个确定的时间参数，而我们自己也不知道确切需要多长时间，yield就是cpu你先切换出去，过会再回来。

线程里使用mutex，会挂起，不会忙等busy waiting，所以在 `“无锁”`或 `“busy waiting”` 两种情况下需要减少等待时间 可以使用yield()

