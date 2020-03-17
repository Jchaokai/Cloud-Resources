## Golang GC



#### Golang GC发展

- v1.1 STW

- v1.3 Mark STW, Sweep 并行

- v1.5 三色标记法

-  v1.8 hybrid write barrier 



#### Golang的GC

**简述：**

golang的GC是一种`标记-清除`的GC算法，

MarkWorker goroutine递归扫描所有对象，并使用三色标记（`白色-不可访问`、`灰色-待处理`、`黑色-可访问`）,但最终他们只会是黑、白对象。

在编译时，编译器已经注入一个称为`写屏障`的代码片段，来监视任何`goroutine`到堆内存的所有修改。

当执行`stop the world`时，调度器休眠所有线程并抢占所有`goroutine`。

GC回收`白色`对象，以便堆或中央对象可以重用。

执行`start the world`唤醒cpu内核和线程，继续执行`goroutine`







------



#### References

- [深入Golang Runtime之Golang GC的过去,当前与未来](https://www.jianshu.com/p/bfc3c65c05d1)



