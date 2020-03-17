###  笔记记录

- **nil**

    1. Go的文档中说到，nil是预定义的标识符，代表指针、通道、函数、接口、映射或切片的零值,并不是GO 的关键字之一

    2. nil只能赋值给指针、channel、func、interface、map或slice类型的变量 (非基础类型) 否则会引发 panic

        

- **make** & **new**

    ​	new 返回指针(*T)，make 返回引用(T)<br/>
    
    ​	make只用于map slice channel，new  map slice channel时，返回指针，并且是nil
    
    
    
- **Slice Sorting**

    用到 sort.Sllice()， 底层快排

    ```go
    type S struct {
    	v int
    }
    
    func main() {
    	s := []S{{1}, {3}, {5}, {2}}
    	sort.Slice(s, func(i, j int) bool {
    		return s[i].v < s[j].v
    	})
    	fmt.Printf("%v", s)
    }
    
    ```

- **utf8 length**

    ```go
    import (
    	"fmt"
    	"unicode/utf8"
    )
    
    func main() {
    	fmt.Println(utf8.RuneCountInString("你好"))
    }
    ```

    

- **readline**

    ```go
    fmt.Fscanf()
    bufio.Reader.ReadLine()
    bufio.ReadString('\n')
    bufio.Scanner.Scan()
    /*
    bufio.Scanner.Scan(): 		
    	=== Best suited ===
    	func (s *Scanner) Scan() bool
    	Scan方法获取当前位置的token（该token可以通过Bytes或Text方法获得），并让Scanner的扫描位置移动到下一个token。当扫描因为抵达输入流结尾或者遇到错误而停止时，本方法会返回false。在Scan方法返回false后，Err方法将返回扫描时遇到的任何错误；除非是io.EOF，此时Err会返回nil。
    	例子:
    	scanner := bufio.NewScanner(file)
    	for {
    		if ok := scanner.Scan(); !ok {
    			if scanner.Err() == nil {
    				fmt.Println("EOF")
    				break
    			}
    			fmt.Println(scanner.Err())
    		}
    		fmt.Println("每一行数据",scanner.Text())
    	}
    	
    	
    fmt.Fscanf(): 				
    	=== Only applicable to formated lines ===
    	func Fscanf(r io.Reader, format string, a ...interface{}) (n int, err error)
    	Fscanf从r扫描文本，根据format 参数指定的格式将成功读取的空白分隔的值保存进成功传递给本函数的参数。返回成功扫描的条目个数和遇到的任何错误。
    	例子：
    	var line []int
    	for {
    		//数据样式:  54 - 110shismshims
    		//获取每一行的前两个数字
    		var f ,s int
    		if _, e = fmt.Fscanf(file, "%d - %d", &f, &s); e != nil || e == io.EOF {
    			break
    		}
    		line = append(line,f,s)
    	}
    	fmt.Println(line)
    	
    bufio.Reader.ReadLine(): 
    	=== Very low level. May require more invocations when buffer limit is exceeded ===
    	
    bufio.ReadString('\n'): 
    	=== Cannot handle EOF ===
    	
    */
    ```

    

- **flag**

    flag 包用来解析命令行参数

    ```go
    //使用flag.String(), Bool(), Int()等函数注册flag
    //下例声明了一个整数flag，解析结果保存在*int指针ip里：
    var ip = flag.Int("flagname", 1234, "help message for flagname")
    
    //也可以将flag绑定到一个变量，使用Var系列函数：
    var flagvar int
    flag.IntVar(&flagvar, "flagname", 1234, "help message for flagname")
    
    //你可以自定义一个用于flag的类型（满足Value接口）并将该类型用于flag解析，如下：
    flag.Var(&flagVal, "name", "help message for flagname")
    
    //在所有flag都注册之后，调用：
    flag.Parse()
    
    //flag的值可以直接使用。如果你使用的是flag自身，它们是指针；如果你绑定到了某个变量，它们是值。
    fmt.Println("ip has value ", *ip)
    fmt.Println("flagvar has value ", flagvar)
    ```

    例子：

    ```go
    package main
    
    import (
        "flag"
        "fmt"
    )
    
    var ip string
    var port int
    
    func init() {
    	flag.StringVar(&ip,"ip","0.0.0.0","ip address")
            flag.IntVar(&port,"port",8000,"port number")
    }
    
    func main() {
    	flag.Parse()
    	fmt.Printf("%s:%d", ip, port)
    }
    
    ```

    

-  **shared object** (共享对象)

    Compile str.go to shared object(str.so). 

    ```go
    // str.go
    package main
    
    import "strings"
    
    func UpperCase(s string) string {
    	return strings.ToUpper(s)
    }
    
    //命令行 go build -buildmode=plugin -o str.so str.go
    
    // main.go
    package main
    
    import (
    	"fmt"
    	"log"
    	"plugin"
    )
    
    func main() {
    	p, err := plugin.Open("str.so")
    	if err != nil {
    		log.Panicf("plugin.Open: %s\n", err)
    	}
    	f, err := p.Lookup("UpperCase")
    	if err != nil {
    		log.Panicf("Lookup UpperCase: %s\n", err)
    	}
    	UpperCase, ok := f.(func(string) string)
    	if !ok {
    		log.Panicf("UpperCase assertion: %s\n", err)
    	}
    	s := UpperCase("hello")
    	fmt.Println(s)
}
    
    ```
    
    

- **String Internal**

    将[]byte 转化成string ，不会导致内存复制

    预期输出为“ 143”

    ```go
    
    package main
    
    import (
    	"fmt"
    	"unsafe"
    )
    
    func main() {
    	var b = []byte("123")
    	s := *(*string)(unsafe.Pointer(&b))
    
    	b[1] = '4'
    	fmt.Printf("%+v\n", s) //print 143
    }
    
    ```

    

- **Slice Internal**

    填写注释A处，以确保printOriginalSlice将打印子切片的原始切片

    ```go
    package main
    
    import (
    	"fmt"
    	"reflect"
    	"unsafe"
    )
    
    const M = 10
    const N = 5
    
    func printOriginalSlice(subslice *[]int) {
    	//A
        data:=(*[M]int)(unsafe.Pointer(((*reflect.SliceHeader)(unsafe.Pointer(subslice))).Data))
    
    	fmt.Printf("original\t%p:%+v\n", data, *data)
    }
    
    func main() {
    	slice := make([]int, M)
    	for i, _ := range slice {
    		slice[i] = i
    	}
    	subslice := slice[0:N]
    
    	fmt.Printf("slice\t%p:%+v\n", &slice, slice)
    	fmt.Printf("subslice\t%p:%+v\n", &subslice, subslice)
    	printOriginalSlice(&subslice)
    }
    
    ```

    

- **Map malloc Threshold** (map malloc阈值)

    What is the malloc threshold of Map object? How to modify it?

    ```
    
    The default limit is 128.
    It can be modified by changing the value of maxKeySize and maxValueSize in runtime.hashmap
    ```

    

- **runtime.newobject()**

    What does runtime.newobject() do? Does `make()` and `new` operation always invoke runtime.newobject()?

    ```
    
    runtime.newobject() is to allocate heap memory.
    make() and `new` operation will not invoke runtime.newobject() when it is inlined or optimized.
    ```

    

- **GC**

    简要描述golang的GC怎么工作

    ```
    MarkWorker goroutine recursively scan all the objects and colors them into white(inaccessible), gray(pending), black(accessible). But finally they will only be black and white objcts. In compile time, the compiler has already injected a snippet called write barrier to monitor all the modifications from any goroutines to heap memory. When "Stop the world" is performed, scheduler hibernates all threads and preempt all goroutines. Garbage collector will recycle all the inaccessible objects so heap or central can reuse. If the whole span of memory are unused, it can be freed to OS. Perform "Start the world" to wake cpu cores and threads, and resume the execution of goroutines.
    翻译:
    MarkWorker goroutine递归扫描所有对象，并将它们着色为白色（不可访问），灰色（待处理），黑色（可访问）。 但最终它们只会是黑白对象。 在编译时，编译器已经注入了一个称为写屏障的代码片段，以监视从任何goroutine到堆内存的所有修改。 当执行“ Stop the world”时，调度程序将休眠所有线程并抢占所有goroutine。 垃圾收集器将回收所有无法访问的对象，以便堆或中央对象可以重用。 如果未使用全部内存，则可以将其释放给OS。 执行“Start the world”以唤醒CPU内核和线程，并继续执行goroutine。
    ```

    

-  **Goroutine Sleep**

    What is the difference between C.sleep() and time.Sleep()?

    ```
    
    C.sleep() invokes syscall sleep, which causes idle threads
    time.Sleep() is optimized for goroutine so syscall sleep is not involved.
    - - -
    C.sleep（）调用系统睡眠，这会导致空闲线程 
    time.Sleep（）针对goroutine进行了优化，因此不涉及系统调用睡眠。
    ```



- **Memory Allocation**（内存分配）

     Briefly introduce the strategies that how go runtime allocates memory.

    (简要介绍运行时分配内存的策略。)

    ```
    
    For small objects(<=32KB), go runtime starts with cache firstly, then central, and finally heap.
    For big objects(>32KB), directly from heap.
    - - - 
    对于较小的对象（<= 32KB），运行时首先从缓存开始，然后是中央，最后是堆。 
    对于大对象（> 32KB），直接从堆中分配。
                                
    ```



-  **Stack vs Heap**

    When go runtime allocates memory from heap, and when from stack?

    ```
    
    For small objects whose life cycle is only within the stack frame, stack memory is allocated.
    For small objects that will be passed across stack frames, heap memory.
    For big objects(>32KB), heap memory.
    For small objects that could escape to heap but actually inlined, stack memory.
    - - - 
    对于生命周期仅在stack的小型对象，将分配stack内存。 
    对于将在stack之间传递的小对象，请使用heap内存。 
    对于大对象（> 32KB），堆内存。 
    对于可能逃逸到堆heap但实际上是内联的小对象，请使用stack内存。
    ```



-  **Goroutine Pause or Halt**（Goroutine暂停/停止）

    List the functions can stop or suspend the execution of current goroutine, and explain their differences.

    列出可以停止或暂停当前goroutine执行的函数，并说明它们的区别。

    ```
    
    runtime.Gosched: give up CPU core and join the queue, thus will be executed automatically.
    runtime.gopark: blocked until the callback function unlockf in argument list return false.
    runtime.notesleep: hibernate the thread.
    runtime.Goexit: stop the execution of goroutine immediately and call defer, but it will not cause panic.
    - - - 
    runtime.Gosched：使当前goroutine放弃处理器，以让其它goroutine运行。它不会挂起当前goroutine，因此当前goroutine未来会恢复执行。
    runtime.gopark：阻塞，直到参数列表中的回调函数unlockf返回false。
    runtime.notesleep：休眠线程。
    runtime.Goexit：立即停止执行goroutine并调用defer，但这不会引起panic。
                                
    ```