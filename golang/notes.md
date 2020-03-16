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

-  **utf8 length**

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

    说出下面的区别

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

