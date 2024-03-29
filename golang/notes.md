###  笔记记录

- golang中 `+ - ` 优先级比` >>`  `<<` 高

- golang中 for loop 的常见错误 [https://github.com/golang/go/wiki/CommonMistakes](https://github.com/golang/go/wiki/CommonMistakes)

- **nil**

    1. Go的文档中说到，nil是预定义的标识符，代表指针、通道、函数、接口、映射或切片的零值,并不是GO 的关键字之一

    2. nil只能赋值给指针、channel、func、interface、map或slice类型的变量 (非基础类型) 否则会引发 panic

        

- **make** & **new**

    ​	new 返回指针(*T)，make 返回引用(T)<br/>
    
    ​	make只用于map slice channel，new  map slice channel时，返回指针，并且是nil



- **空结构体  Empty structs**

    有什么用 ？

    1. 空结构体可以省内存，但微不足道

    2. 当一个对象只需要方法，不需要存值，不需要保存对象状态时，可以使用空结构体

        ```go
        type Lamp struct{}
        
        func (l Lamp) On() {
                println("On")
        
        }
        func (l Lamp) Off() {
                println("Off")
        }
        
        func main() {
               	// Case #1.
               	var lamp Lamp
               	lamp.On()
               	lamp.Off()
        	
               	// Case #2.
               	Lamp{}.On()
               	Lamp{}.Off()
        }
        ```

    3. 还可以配合channel使用，例子：

        ```
        func worker(ch chan struct{}) {
        	<-ch
        	println("roger")
        	close(ch)
        }
        
        func main() {
        	ch := make(chan struct{})
        	go worker(ch)
        	
        	ch <- struct{}{}
        	
        	<-ch	//读取已经关闭的channel，会取道对应type的零值，这里是空的struct{}
        	
        	println(“roger")
        	// Output:
        	// roger
        	// roger
        }
        ```

        

- **怎么比较两个struct、interface**

    可以用 `==` 比较 `struct`，就象处理其他简单类型一样，**但是要保证它们不含有`slices, maps, functions`，不然编译不通过 &emsp;！ ！ ！**

    ```go
    type Foo struct {
    	A int
    	B string
    	C interface{}
    }
    a := Foo{A: 1, B: "one", C: "two"}
    b := Foo{A: 1, B: "one", C: "two"}
    
    println(a == b)
    // Output: true
    
    type Bar struct {
    	A []int
    }
    a := Bar{A: []int{1}}
    b := Bar{A: []int{1}}
    
    println(a == b)
    // Output: invalid operation: a == b (struct containing []int cannot be compared)
    ```

    只要基础类型是“简单” 和 相同，可以用 `==`比较 `interface` ，否则，代码将在运行时 panic：

    ```go
    var a interface{}
    var b interface{}
    
    a = 10
    b = 10
    println(a == b)
    // Output: true
    
    a = []int{1}
    b = []int{2}
    println(a == b)
    // Output: panic: runtime error: comparing uncomparable type []int
    
    ```

    包含 slice，map（但不包含函数）的`struct`、`interface`都可以与`reflect.DeepEqual()`函数进行比较:

    ```go
    var a interface{}
    var b interface{}
    
    a = []int{1}
    b = []int{1}
    println(reflect.DeepEqual(a, b))
    // Output: true
    
    a = map[string]string{"A": "B"}
    b = map[string]string{"A": "B"}
    println(reflect.DeepEqual(a, b))
    // Output: true
    
    temp := func() {}
    a = temp
    b = temp
    println(reflect.DeepEqual(a, b))
    // Output: false
    ```

    此外为了比较`[]byte类型的 slice`，bytes包中有很好的帮助程序函数：`bytes.Equal()`, `bytes.Compare()`, and `bytes.EqualFold()`。 后者用于比较不区分大小写的文本字符串，这比`reflect.DeepEqual()`快得多。

    

- **Slice Sorting**

    用到 sort.Slice()， 底层快排

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



- **Slice Copy 与 = 的区别**

    Copy(dst , src ) 拷贝速度比 = 慢 

    Copy(dst  , src )  是值拷贝，= 赋值是引用

    

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

    要求将[]byte 转化成string ，不会导致内存复制

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
    MarkWorker goroutine recursively scan all the objects and colors them into white(inaccessible), gray(pending), black(accessible). But finally they will only be black and white objcts. 
    In compile time, the compiler has already injected a snippet called write barrier to monitor all the modifications from any goroutines to heap memory. 
    When "Stop the world" is performed, scheduler hibernates all threads and preempt all goroutines. Garbage collector will recycle all the inaccessible objects so heap or central can reuse. If the whole span of memory are unused, it can be freed to OS.
    Perform "Start the world" to wake cpu cores and threads, and resume the execution of goroutines.
    翻译:
    MarkWorker goroutine递归扫描所有对象，并将它们着色为白色（不可访问），灰色（待处理），黑色（可访问）。 但最终它们只会是黑白对象。 
    在编译时，编译器已经注入了一个称为写屏障的代码片段，以监视从任何goroutine到堆内存的所有修改。
    当执行“ Stop the world”时，调度程序将休眠所有线程并抢占所有goroutine。 垃圾收集器将回收所有无法访问的对象，以便堆或中央对象可以重用。 如果未使用全部内存，则可以将其释放给OS。 
    执行“Start the world”以唤醒CPU内核和线程，并继续执行goroutine。
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
    对于较小的对象（<= 32KB），runtime首先从缓存开始，然后是中央，最后是堆。 
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



- **用golang实现 stack 、queue**，几种方式

    1. slice可以实现

    2. container/list

    3. buffered channel

    对于slice 与 list，gogroup回应 “Always use a slice.”, [Dave Cheney](https://groups.google.com/forum/#!msg/golang-nuts/mPKCoYNwsoU/tLefhE7tQjMJ) 

    buffered channel 永远不是一个好主意

    

- **怎么按固定的顺序显示map**

    借助额外的空间，sort keys，借助排序好的keys遍历输出map ,

- **slice 的排序**

    自定义结构体，实现 len,less,swap三个方法

    <p style="color:red;margin-left:40px" >---这个方法太他妈过时了   ---这个方法太他妈过时了  ---这个方法太他妈过时了</p>
    **详见 ：[ stackoverflow  [sort a map by key or value] ](https://stackoverflow.com/questions/18695346/how-to-sort-a-mapstringint-by-its-values)**
    
    使用go 1.8 sort包 Slice()方法
    
    ```
        func Slice(slice interface{}, less func(i, j int) bool)
    ```
    
    例: 
    
    ```go
        package main
        
        import (
        	"fmt"
        	"sort"
        )
        
        func main() {
        	people := []struct {
        		Name string
        		Age  int
        	}{
        		{"Gopher", 7},
        		{"Alice", 55},
        		{"Vera", 24},
        		{"Bob", 75},
        	}
        	sort.Slice(people, func(i, j int) bool { return people[i].Name < people[j].Name })
        	fmt.Println("Sort By name:", people)
        
        	sort.Slice(people, func(i, j int) bool { return people[i].Age < people[j].Age })
        	fmt.Println("Sort By age:", people)
        }
    ```
    
       
    
- **slice的声明，哪个更可取**

    var a []int

    a := []int{}

    如果不使用slice，则第一个声明不分配内存，因此首选第一种声明方法。

    

- **Map原地修改value 错误**

    在Go语言中，Map中的值是不可以原地修改的，如：

    ```go
    package main
    
    type S struct{
    	name string
    }
    
    func main(){
    	m := map[string]S{
    		"x" : S{"one"},
    	}
    	m["x"].name = "two"
    }
    ```

    上面的代码会编译失败，因为在go中 map中的赋值属于值copy，就是在赋值的时候是把S的完全复制了一份，复制给了map。而在go语言中，是不允许将其修改的。
     **但是如果map的value为int，是可以修改的，因为修改map中的int属于赋值的操作。**

    ```go
    package main
    
    type S struct {
        Id int
    }
    
    func main() {
        s1 := map[string]S{
            "x" : S{22},
        }
        s1["x"] = 2
        s1["x"] = 3
    }
    ```

    那么，如何在go语言中原地修改map中的value呢？ 答案是：**传指针！**

    ```go
    package main
    
    type S struct{
    	name string
    }
    
    func main(){
    	m := map[string]*S{
    		"x" : &S{"one"},
    	}
    	m["x"].name = "two"
    }
    ```

    在结构体比较大的时候，用指针效率会更好，因为不需要值copy
     当然，如果map中的value为 *int指针类型，那么**在赋值时不可以用&123，因为int为常量，不占内存，没有内存地址**

    ```go
    package main
    
    type Student struct {
        Id int
    }
    
    func main() {
        s2 := make(map[string]*int)
        n := 1
        s2["chenchao"] = &n 
    }
    ```

- **slice 扩容机制**

  `runtime\slice.go` 下  `func growslice()`

  ```go
  // cap : 所需的最小容量(原来slice的容量 + append元素的容量)
  if cap > old.cap *2 {
  	newCap = cap
  }else{
      if old.len < 1024 {
          newCap = old.cap * 2
      }else{
          newCap = old.cap * 1.25
      }
  }
  
  // newCap 到现在为止只是预估容量
  // 最后 newCap = 分配的内存大小 / 元素类型对应的size
  // 分配的内存大小固定：
  // var class_to_size = [_NumSizeClasses]uint16{0, 8, 16, 32, 48, 64, 80, 96, 112, 128, 144, 160, 176, 192, 208, 224, 240, 256, 288, 320, 352, 384, 416, 448, 480, 512, 576, 640, 704, 768, 896, 1024, 1152, 1280, 1408, 1536, 1792, 2048, 2304, 2688, 3072, 3200, 3456, 4096, 4864, 5376, 6144, 6528, 6784, 6912, 8192, 9472, 9728, 10240, 10880, 12288, 13568, 14336, 16384, 18432, 19072, 20480, 21760, 24576, 27264, 28672, 32768}
  
  ```


- **优雅的关闭服务，参考Gin**

    ```go
      	go func() {
            // 监听请求
            if err := srv.ListenAndServe(); err != nil && err != http.ErrServerClosed {
                log.Fatalf("listen: %s\n", err)
            }
        }()
        // 优雅Shutdown（或重启）服务
        quit := make(chan os.Signal)
        signal.Notify(quit, os.Interrupt) // syscall.SIGKILL
        
        <-quit
        
        log.Println("Shutdown Server ...")
        ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
        defer cancel()
        if err := srv.Shutdown(ctx); err != nil {
            log.Fatal("Server Shutdown:", err)
        }
        select {
        	case <-ctx.Done():
        }
        log.Println("Server exiting")
    ```

    

- **context.WithTimeout**

    
