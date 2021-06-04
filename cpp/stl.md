# STL

六大部件: 

- Containers  容器	
- Allocators  分配器	
- Algorithms  算法
- Iterators  迭代器
- Adapters  适配器
- Functors  仿函数

**场景描述:**

如果我们需要对100k的数据进行处理，我们需要一个存放这么多数据的地方(containters),容器也不是凭空产生的，需要分配器(allocators)分配出这么多资源，之后我们要对容器的数据进行操作，就要用到一些已经帮我们写好的算法(Algorithms)如插入、删除、排序等，使用算法进行操作时，也要借助迭代器(Iterators)来访问，存取容器的元素。

仿函数(Functors)，对一些我们自定义的class，比如我们要实现两个类相加的功能，就需要写一些仿函数。

适配器(Adapters)，相当于"变压器","转换器"，对其他五个部件进行转换。

**示例如图所示：**

<img src="assets\stl_6components.png" style="zoom:67%;" />

​	

## 容器分类

序列容器(sequence containters)

- Array
- Vector 
- Deque 双向队列，前、后都可以扩充
  - stack
  - queue
- List 双向环型链表,*最后有个节点不属于List , 这样才符合前闭后开原则*
- Forward-List 单项链表

关联式容器(associative containters)

- rb_tree

  - Set/ Multiset  (key不能重复/ Mutilset可重复)
  - Map/ Multimap  (key不能重复/ Mutilmap可重复)

- hashtable

  - unordered_set/Mutilset

  - unordered_map/Mutilmap

  

小练习:

开辟一个50w大小的Array，插入50w条数据，并在其中找出一个(随机)的数

```c++
#include <array>
#include <iostream>
#include <ctime>
#include <cstdlib> //qsort,bsearch,NULL

const int ASIZE = 500000;


namespace arr01
{
    using namespace std;

    int compareLongs(const void* a,const void* b){
        return (*(long*)a - *(long*)b);
    }

    void test_array()
    {
        cout << "************ test_array  **********\n";

        array<long, ASIZE> c;
        clock_t starttime = clock();
        for (long i = 0; i < ASIZE; ++i)
        {
            c[i] = rand();
        }
        cout << "fill array need m-seconds " << (clock() - starttime) << endl;
        cout << "array size " << c.size() << endl;
        cout << "array.front() " << c.front() << endl;
        cout << "array.back() " << c.back() << endl;
        cout << "array.data() " << c.data() << endl;

        long target = []() -> long
        {
            long target = 0;
            cout << "target int [0," << RAND_MAX << "], enter the target you want:   " ;
            cin >> target;
            return rand();
        }();

        starttime = clock();
        ::qsort(c.data(), ASIZE, sizeof(long), compareLongs);

        long* pItem = (long *)::bsearch(&target,c.data(), ASIZE, sizeof(long), compareLongs);
        cout << "qsort() + bsearch() , m-seconds " << (clock() - starttime) << endl;
        if (pItem != NULL)
            cout << " target"<< *pItem << endl;
        else
            cout << "not found" << endl;
    }
} // namespace arr01


int main()
{

    arr01::test_array();
    system("pause");
    return 0;
}


```

## 分配器

### 前言: template相关

-  类模板

  - 全特化

    全特化指 T不管是什么，都按模板定义的那样使用

  - 偏特化

    对特殊的type进行特殊的处理

    ```c++
    template <class T>
    struct ccc{
        //todo
    };
    
    template<> struct ccc<int>{
        //如果ccc是int型,todo something
    };
    template<> struct ccc<void>{
      	//如果ccc是void，todo something
    };
    ```

    - 个数上的偏

      ```c++
      template <class T,class Alloc = alloc>
      class vector{
         //todo
      };
      
      template<class Alloc>
      class vector<bool,Alloc>{
          //第一个绑定为bool,
         	//vector本来可以接受任意类型的元素，如果第一个
          //接受的元素是bool型，那我们可以进行特殊的设计,比如我们是不是可以精简vector的设计?，如果它只存放0，1的话
          //todo
      }
      ```

      

    - 范围上的偏

      ```c++
      template <class T>
      class vector{
         //todo
      };
      
      template<class T>
      class vector<T*>{
          //接受指针，指针指向的类型还是任意
          //todo
      }
      
      template <class T>
      class vector<const T*>{
          //todo
      }
      ```

      

- 函数模板

- 成员模板

### allocators

`operator new()` 和 `malloc()`

operator new()最终还是调用malloc()



**分配器在在分配内存时，除了分配固定size的区间存放数据外，还有额外的开销，如果开辟的size区间小，那么意味着额外开销的比例就大，开辟的size空间小，额外开销的比例就小**

<img src="assets\image-20210524092743794.png" style="zoom:80%;" />

上图分析 :

- 最上，最下的位置，如图  `00000041`  是cookie 记录了 整个开销的大小，

  在malloc拿内存 时得到一个指针，最后free还内存 时只要还这个指针，不需要告诉它大小

  **TIPS：G2.9 分配器进行了优化，没有过多使用cookie**





vc6.0 的分配器 用 `::oparetor new` `::operator delete` 完成 `allocate() ` `deallocate()`，没有其他特殊设计

BC5 STL 对分配器 用 `::oparetor new` `::operator delete` 完成 `allocate() ` `deallocate()`，没有其他特殊设计

G2.9 的分配器 `::oparetor new` `::operator delete` 完成 `allocate() ` `deallocate()`，没有其他特殊设计



*TIPS: 正常使用下，我们分配的size区间不大，上述的三个标准额外开销都大*

```

GCC 在<defalloc.h>中有这样的注释：
//do not use this file ........
//SGI STL using a different alloctor .....
//this file is not included by any other STL alloctor
//虽然定义出了这个文档，但是容器在分配时,使用了不同的分配器 -> alloc

GCC2.9 在各容器分配时使用了 alloc 
template <class T,class Alloc = alloc>
class vector{
	...
}
其 alloc 实现在 <stl_alloc.h>
因为分配内存的最后动作一定是 c的 malloc ，所以尽量减少malloc的次数，不过度包装



```



## 序列容器

### List

G2.9 sizeof(...) 4

G4,9 sizeof(...) 8





iterator 必须提供的5种 特性,以供算法使用

- iterator_category
- difference_type
- value_type
- reference
- pointer



iterator traits

### vector

三个指针  `start`  `finish` `end_of_storage`

sizeof(vector) = 3*4 = 12;



### deque

看似是连续的空间，但其实是一段段buffer组成，由`map`(“中央控制器”，指向一个vector的指针，存储着各段buffer的位置信息 ) 管理串联起来。

deque的 `iterator` 是一个class,包含四个指针`cur`,`first`,`last`,`node`：

- cur：deque的某个元素在一段buffer中的位置，

- first：一段buffer的开始边界

- last：一段buffer的结束边界

- node：node指向了（`该段buffer位置信息`）存储在map中的位置。

<img src="assets\deque.png" style="zoom:80%;" />



```
class deque{
	....
	protected:
        iterator start			指向deque的第一段buffer
        iterator finish			指向deque的第一段buffer
        map_pointer  map		指向一个vector的指针,存储各段buffer的位置信息
        size_type map_size		map所代表的vector的空间大小
	...
};

```



sizeof(queue) = 2迭代器(1迭代器4指针=16) + sizeof(map_pointer) + sizeof(size_type)
			  = 2 * 4 * 4 + 4 + 4 
			  = 40字节



buffer的空间分配策略：

```
inline size_f  _deque_buf_size(size_t n,size_t sz){
	return n !=0 ? n :(sz <512 ? size_t(512/sz) : size_t(1))	
}
```

- n != 0	 buffer_size = n，即由使用者决定 

  **G2.9允许指定buffer size，G4.X 不允许自己决定,大小为512/size_**

- n == 0
  - sz( sizeof(value_type) ) < 512，buffer_size = 512 / sz
  - sz >= 512 ，buffer_size = 1



#### queue

一个deque,封锁一些功能就是一个queue，没有iterator，不允许遍历

<img src="assets\stack.png" style="zoom:80%;" />

#### stack

一个deque,封锁一些功能就是一个stack，没有iteartor，不允许遍历

<img src="assets\queue.png" style="zoom:80%;" />



## 关联式容器

底层由`RB_tree `  和 `hash_table`支撑

### RB_tree相关

sizeof(RB_tree)  **G2.9 = 12** ，如图下图所示 

<img src="assets\rb_tree.png" style="zoom:70%;" />

**G4.9 = 24**

<img src="assets\rb_tree2.png" style="zoom:70%;" />



