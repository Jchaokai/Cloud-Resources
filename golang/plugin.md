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

<font  id="typeplugin" size="5px" color="#337ab7" >type Plugin</font>

type Plugin struct {

​	// contains filtered or unexported fields

}

Plugin is a loaded Go plugin.

<h4 id = "1" style="color:#337ab7">func Open(path string) (*Plugin, error)</h4>

Open opens a Go plugin. If a path has already been opened, then the existing *Plugin is returned. It is safe for concurrent use by multiple goroutines.

<h4 id = "1" style="color:#337ab7">func (p *Plugin) Lookup(symName string) (Symbol, error)</h4>

Lookup searches for a symbol named symName in plugin p. A symbol is any exported variable or function. It reports an error if the symbol is not found. It is safe for concurrent use by multiple goroutines.

<h4 id= "3" style="color:#337ab7">type Symbol</h4>

type Symbol interface{}
A Symbol is a pointer to a variable or function.

For example, a plugin defined as

```go
package main

// // No C code needed.
import "C"

import "fmt"

var V int

func F() { fmt.Printf("Hello, number %d\n", V) }

```

may be loaded with the Open function and then the exported package symbols V and F can be accessed

```go
p, err := plugin.Open("plugin_name.so")
if err != nil {
	panic(err)
}
v, err := p.Lookup("V")
if err != nil {
	panic(err)
}
f, err := p.Lookup("F")
if err != nil {
	panic(err)
}
*v.(*int) = 7
f.(func())() // prints "Hello, number 7"
```



