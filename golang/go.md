## GO - goroutine
协程


### goroutine 调度
go的调度器内部有三个重要的结构：**G** **M** **P**
- G: goroutine对象，存放goroutine信息，与P的绑定信息
- M: 代表真正的内核OS线程，G是在M上执行
- P: 代表调度的上下文，可以看做一个局部的调度器，使go代码在一个线程上跑，它是实现从N:1到N:M映射的关键。每一个运行的M都绑定一个p，
![](https://img-blog.csdn.net/20180108174007331?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcGhhbnRvbV8xMTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

如上图所示，有2个物理线程M，每个M都拥有一个context（P），每个也都有一个正在运行的goroutine。图中灰色的那些goroutine并没有运行，而是处于ready的就绪态，等待被调度。P维护着这个队列（称为runqueue)。go语言里，启动一个goroutine很容易：go function就行，所以每有一个go语句被执行，runqueue队列就在其末位加入一个goroutine，在下一个调度点，就从runqueue取出一个goroutine执行。

**注意：** P的数量可以通过GOMAXPROCS()来设置，它其实代表真正的并发度，即有多少个goroutine可以同时运行。

### goroutine & channel

