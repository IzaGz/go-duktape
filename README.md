# Duktape bindings for Go(Golang) [![wercker status](https://app.wercker.com/status/1ce7671d7223880e967bf8a81b96341d/s "wercker status")](https://app.wercker.com/project/bykey/1ce7671d7223880e967bf8a81b96341d)
[Duktape](http://duktape.org/index.html) is a thin, embeddable javascript engine.
Most of the [api](http://duktape.org/api.html) is implemented.
The exceptions are listed [here](https://github.com/olebedev/go-duktape/blob/master/api.go#L1566).

### Usage

The package is fully go-getable, no need to install any external C libraries.  
So, just type `go get gopkg.in/olebedev/go-duktape.v2` to install.


```go
package main

import "fmt"
import "gopkg.in/olebedev/go-duktape.v2"

func main() {
  ctx := duktape.New()
  ctx.EvalString(`2 + 3`)
  result := ctx.GetNumber(-1)
  ctx.Pop()
  fmt.Println("result is:", result)
}
```

### Go specific notes

Bindings between Go and Javascript contexts are not fully functional.
However, binding a Go function to the Javascript context is available:
```go
package main

import "fmt"
import "gopkg.in/olebedev/go-duktape.v2"

func main() {
  ctx := duktape.New()
  ctx.PushGlobalGoFunction("log", func(c *duktape.Context) int {
    fmt.Println(c.SafeToString(-1))
    return 0
  })
  ctx.EvalString(`log('Go lang Go!')`)
}
```
then run it.
```bash
$ go run *.go
Go lang Go!
$
```

### Timers

There is a method to inject timers to the global scope:
```go
package main

import "fmt"
import "gopkg.in/olebedev/go-duktape.v2"

func main() {
  ctx := duktape.New()

  // Let's inject `setTimeout`, `setInterval`, `clearTimeout`,
  // `clearInterval` into global scope.
  ctx.PushTimers()

  ch := make(chan string)
  ctx.PushGlobalGoFunction("second", func(_ *Context) int {
    ch <- "second step"
    return 0
  })
  ctx.PevalString(`
    setTimeout(second, 0);
    print('first step');
  `)
  fmt.Println(<-ch)
}
```
then run it
```bash
$ go run *.go
first step
second step
$
```

Also you can `FlushTimers()`.

### Command line tool

Install `go get gopkg.in/olebedev/go-duktape.v2/...`.  
Execute file.js: `$GOPATH/bin/go-duk file.js`.

### Benchmarks
| prog        | time  |
| ------------|-------|
|[otto](https://github.com/robertkrimen/otto)|200.13s|
|[anko](https://github.com/mattn/anko)|231.19s|
|[agora](https://github.com/PuerkitoBio/agora/)|149.33s|
|[GopherLua](https://github.com/yuin/gopher-lua/)|8.39s|
|**go-duktape**|**9.80s**|

More details are [here](https://github.com/olebedev/go-duktape/wiki/Benchmarks).

### Status

The package is not fully tested, so be careful.


### Contribution

Pull requests are welcome!  
__Convention:__ fork the repository and make changes on your fork in a feature branch.

### Chat [![Gitter](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/olebedev/go-duktape?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)
