### GO - goroutine
协程


#### goroutine 调度
**G** **M** **P**
- G: goroutine对象，存放goroutine信息，与P的绑定信息
- M: 代表真正的内核OS线程,每创建一个M，都对应一个底层线程，G是在M上执行
- P: 处理器，每一个运行的M都绑定一个p，



#### goroutine & channel

