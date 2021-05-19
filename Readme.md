# goroutine manager 

The package allows you to monitor of running goroutines
It was useful when you need wait when all your goroutines will finish
Look Test_Usage* in mgr_test.go file for usage

### Usage:

```go
package main

import (
	"github.com/rurick/goroutinemanager"
	"fmt"
	"time"
	"context"
)

func main() {
	f1 := func() { time.Sleep(10 * time.Millisecond) }
	f2 := func() { time.Sleep(5 * time.Millisecond) }
	f3 := func() { time.Sleep(30 * time.Millisecond) }
	f4 := func() { time.Sleep(15 * time.Millisecond) }

	mgr := goroutinemanager.New()
	mgr.Add("f1")
	go func() { f1(); mgr.Remove("f1") }()

	mgr.Add("f2")
	go func() { f2(); mgr.Remove("f2") }()

	mgr.Add("f3")
	go func() { f3(); mgr.Remove("f3") }()

	mgr.Add("f4")
	go func() { f4(); mgr.Remove("f4") }()

	// emulation of end of programm
	terminateTimeout := 100 * time.Millisecond
	ctx, cancel := context.WithTimeout(context.Background(), terminateTimeout)
	defer cancel()

	select {
	case <-ctx.Done():
		fmt.Println("terminate timeout")
		return
	case <-mgr.Done():
		return
	}
}
```
