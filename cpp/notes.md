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

### unique_ptr

