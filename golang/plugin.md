### plugin package

------

实现Go插件的加载和符号解析。

目前，插件仅在Linux上有效。 

插件是Go主程序包，具有导出的函数和变量，该主程序包已使用以下命令构建：

```shell
go build -buildmode = plugin
```

首次打开插件时，将调用尚未包含在程序中的所有程序包的init函数。 主要功能未运行。 插件仅初始化一次，无法关闭。



#### Index

<a href = "#typeplugin">type Plugin</a>

- <a href ="#1">func Open(path string) (*Plugin, error)</a>
- <a href ="#2">func (p *Plugin) Lookup(symName string) (Symbol, error)</a>

<a href="#3">type Symbol</a>

#### Package Files

- plugin.go 

- plugin_dlopen.go

------

<h3  id="typeplugin" style="color:#337ab7" >type Plugin</h3>

type Plugin struct {<br/>

 // contains filtered or unexported fields

}<br>

Plugin is a loaded Go plugin.

<h3 id = "1" style="color:#337ab7">func Open(path string) (*Plugin, error)</h3>

Open打开一个Go插件。如果已经打开路径，则返回现有的* Plugin。对于多个goroutine并发使用是安全的。

<h3 id = "2" style="color:#337ab7">func (p *Plugin) Lookup(symName string) (Symbol, error)</h3>

查找在插件p中搜索名为symName的符号。符号是任何导出的变量或函数。如果找不到该符号，它将报告错误。对于多个goroutine并发使用是安全的。

<h3 id= "3" style="color:#337ab7">type Symbol</h3>

type Symbol interface{}
Symbol 是指向变量或函数的指针。

<br/>

------

 例子:

package main下有两个 .go文件 str.go、main.go

```go
// str.go
package main

import "strings"

func UpperCase(s string) string {
	return strings.ToUpper(s)
}

```

<br/>

```shell

go build -buildmode=plugin -o str.so str.go
```

may be loaded with the Open function and then the exported package symbols V and F can be accessed

<br/>

```go
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
	UpperCase, ok := f.(func(string) string)	//类型断言
	if !ok {
		log.Panicf("UpperCase assertion: %s\n", err)
	}
	s := UpperCase("hello")
	fmt.Println(s)
}

```



