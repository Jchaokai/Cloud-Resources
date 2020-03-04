## GO - goroutine
协程


### goroutine 调度
go的调度器内部有三个重要的结构：**G** **M** **P**
- G: goroutine对象，存放goroutine信息，与P的绑定信息
- M: 代表真正的内核OS线程，G是在M上执行
- P: 代表调度的上下文，可以看做一个局部的调度器，使go代码在一个线程上跑，它是实现从N:1到N:M映射的关键。每一个运行的M都绑定一个p，
![](https://img-blog.csdn.net/20180108174007331?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcGhhbnRvbV8xMTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


### goroutine & channel

