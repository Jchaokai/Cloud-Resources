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

仿函数(Functors)，对一些我们自定义的class，如果我们要实现相加的功能，就需要写一些仿函数。

适配器(Adapters)，相当于"变压器","转换器"，对其他五个部件进行转换。

**示例如图所示：**

<img src="assets\stl_6components.png" style="zoom:67%;" />

​	

## 容器分类

序列容器(sequence containters)

- Array
- Vector 
- Deque 双向队列，前、后都可以扩充
- List 双向(环型)链表
- Forward-List 单项链表

关联式容器(associative containters)

- Set/ Multiset  (key不能重复/ Mutilset可重复)

- Map/ Multimap  (key不能重复/ Mutilmap可重复)

- unordered_containers

  - unordered_set/Mutilset

  - unordered_map/Mutilmap

    

小练习:

开辟一个50w大小的Array，插入50w条数据，并在其中找出一个(随机)的数

```c++


```

