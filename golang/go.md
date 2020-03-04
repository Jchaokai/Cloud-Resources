## GO - goroutine

《


### goroutine 调度
go的调度器内部有三个重要的结构：**G** **M** **P**
- G: goroutine对象，存放goroutine信息，与P的绑定信息
- M: 代表真正的内核OS线程，G是在M上执行
- P: 代表调度的上下文，可以看做一个局部的调度器，使go代码在一个线程上跑，它是实现从N:1到N:M映射的关键。每一个运行的M都绑定一个p，
![](https://img-blog.csdn.net/20180108174007331?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcGhhbnRvbV8xMTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

如上图所示，有2个物理线程M，每个M都拥有一个context（P），每个也都有一个正在运行的goroutine。图中灰色的那些goroutine并没有运行，而是处于ready的就绪态，等待被调度。P维护着这个队列（称为runqueue)。go语言里，启动一个goroutine很容易：go function就行，所以每有一个go语句被执行，runqueue队列就在其末位加入一个goroutine，在下一个调度点，就从runqueue取出一个goroutine执行。

**注意：** P的数量可以通过GOMAXPROCS()来设置，它其实代表真正的并发度，即有多少个goroutine可以同时运行。

**----- 为什么要维护多个P ?**

因为当一个os线程被阻塞时，p可以转而投奔另一个os线程。示意图如下:
![](https://img-blog.csdn.net/20180108173927945?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcGhhbnRvbV8xMTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

1. **os线程M0陷入阻塞时**，P转而在os线程M1上运行。调度器保证有足够的线程来运行所有的contenxt P（M1可能是创建或者从线程缓存总取出）。
2. **当M0恢复时**，它必须尝试取得一个context P来运行goroutine，一般情况下，它会从其他的os线程那里偷一个context p过来。如果没有偷到的话，它就把goroutine放在一个global runqueue里，然后放入线程缓存里。contexts们也会周期性的检查global runqueue，否则global runqueue上的goroutine永远无法执行。
3. **另一种情况:** P所分配的任务G很快就执行完了（分配不均），这就导致一个上下文P闲着没事干而系统却仍然忙碌。但是如果global runqueue没有任务G了，那么P就不得不从其他的上下文P那里拿一些G来执行。一般来说，如果上下文P从其他的上下文P那里要偷一个任务的话，一般就是’偷’run queue的一半，这就确保了每个os线程都能充分使用。






#### 参考资料
1. [golang之goroutine调度的理解](https://blog.csdn.net/phantom_111/article/details/79005490)
2. [Scalable Go Scheduler Design Doc](https://docs.google.com/document/d/1TTj4T2JO42uD5ID9e89oa0sLKhJYD0Y_kqxDv3I3XMw/edit)