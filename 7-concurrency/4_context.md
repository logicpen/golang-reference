### Context
-----
* Context package is used to pass request scope values, deadlines and cancellation signals across the program boundaries.
* Let's say `function1` starts a goroutine `function2` and `function2` starts another goroutine `function3`. If there's an error in `function1`, then we have to cancel other two goroutines (`function2` and `function3`). In this case we can use `context` package to signal the two goroutines stop executing and free the resources they are using as the parent function that created them has terminated.
* Let's say when handling an http request, we want to pass some request related data to the downstream function in the call stack. We can use `context` package to achieve so.
* In the core of the context package, lies  `Context` interface. This contains four functions.
    1. `Done() <-chan struct{}`:- It returns a channel when a context is cancelled or timeout (either when deadline is reached or timeout time has finished)
    2. `Err() error` :- Err will tell why this context was cancelled. A context is cancelled in three scenarios.
        1. With explicit cancellation signal
        2. Timeout is reached
        3. Deadline is reached
    3. `Deadline() (deadline time.Time, ok bool)`:- Used for handling deadlines and timeouts.
    4. `Value(key interface{}) interface{}`:- Used for retrieve request scope values.
* We can create new context as well as we can derive new context from an existing context. If a parent context is cancelled all the other context those are derived form the parent context are also cancelled
### Creating empty context:-
* To create an new empty context, `Background()` function from context package is used. The context created from `Background()` has no value, can never be cancelled and has no deadline.
* This function is used to crate root context(parent context) from which other context will be derived.
* `ToDo()` can also be used to create empty context but the context created from this function is used as a place holder so that in future an actual context can be passed.
### Deriving from an existing context:-
* `WithValue()`, `WithCancel()`, `WithDeadline()`, `WithTimeout()` functions are used to derive a child context from an existing context.
* `WithValue()` takes a parent context, a key and a value and returns a context with the valued attached to it.
* The derive a context which has value attach to it, is used to pass request scoped values through api boundaries. 
```go
package main

import (
	"context"
	"fmt"
)

type contextKey string

func main() {
	var key contextKey = "id"
	ctx := context.Background()                     // creating empty context
	childContext := context.WithValue(ctx, key, 10) // deriving context with value attacked
	routine(childContext)
}

func routine(ctx context.Context) {
	multipleValCtx := context.WithValue(ctx, contextKey("name"), "Jack") // add another value to the passed context
	id := multipleValCtx.Value(contextKey("id"))                         // extract the value using key
	name := multipleValCtx.Value(contextKey("name"))
	if id != nil && name != nil {
		fmt.Printf("Id is %d and name is %s\n", id, name)
		return
	}
	fmt.Println("Couldn't find context value")
}
```
**output**`Id is 10 and name is Jack`  
* With the help of context we can add value to the http request body pass to the downstream function to use it
* `WithCancel()` takes a parent context and returns a child context and a cancel function.
* The child context returned by the `WithCancel()` function comes with a new Done() channel.
* The returned context's `Done` channel is closed when either the returned cancel function is called or its parent's context's `Done` channel is closed depending on which of these happens first.
```go
package main

import (
	"context"
	"fmt"
	"sync"
	"time"
)

var wg sync.WaitGroup

func main() {

	wg.Add(2)

	ctx := context.Background()
	ctx1, cancel1 := context.WithCancel(ctx)
	go routine1(ctx1)

	time.Sleep(400 * time.Millisecond)
	cancel1()

	wg.Wait()
}

func routine1(ctx context.Context) {
	ctx2, cancel2 := context.WithCancel(ctx)
	defer cancel2() // avoid context leak
	defer wg.Done()
	go routine2(ctx2)

	select {
	case <-time.After(300 * time.Millisecond): // 300 ms is required to succeed
		fmt.Println("routine1 success")
	case <-ctx.Done():
		err := ctx.Err()
		fmt.Println("routine1", err)
	}
}

func routine2(ctx context.Context) {
	defer wg.Done()

	select {
	case <-time.After(200 * time.Millisecond): // 200 ms is required to succeed
		fmt.Println("routine2 success")
	case <-ctx.Done():
		err := ctx.Err()
		fmt.Println("routine2", err)
	}
}
```
**output**
```
routine2 success
routine1 success
```
* In the above example `ctx1` and `ctx2` are derived from `ctx` by using `WithCancel()`. We can see `ctx1` and `ctx2` comes with a cancel function i.e `cancel1` and `cancel2`. 
* Calling `cancel1` would cancel both `ctx1` and `ctx2` while calling `cancel2` would cancel only `ctx2`.
* In the above example main function sleeps for 400ms before calling the `cancel` function, which gives two go routines enough time to execute so we can see success in the output. 
* Let's say we only sleep for 100ms and called `cancel` in the main function. In this case both the go routines won't get enough time to execute and `Done` channel will be closed and the output would be as follows
**output**
```
routine1 context canceled
routine2 context canceled
```
* `WithTimeout()` takes an existing context and time and returns a new context. The context derived would get cancelled automatically after the specified that has passed to `WithTimeout()` while creating the context.
```go
package main

import (
	"context"
	"fmt"
	"sync"
	"time"
)

var wg sync.WaitGroup

func main() {
	wg.Add(2)

	ctx := context.Background()
	ctx1, cancel1 := context.WithTimeout(ctx, 100*time.Millisecond) // ctx1 will be cancelled after 100ms
	defer cancel1() // avoid context leak
	go routine1(ctx1)

	wg.Wait()
}

func routine1(ctx context.Context) {
	ctx2, cancel2 := context.WithTimeout(ctx, 250*time.Millisecond) // ctx2 will be cancelled after 250ms
	defer cancel2()                                                 // avoid context leak
	defer wg.Done()
	go routine2(ctx2)

	select {
	case <-time.After(300 * time.Millisecond): // 300 ms is required to succeed
		fmt.Println("routine1 success")
	case <-ctx.Done():
		err := ctx.Err()
		fmt.Println("routine1", err)
	}
}

func routine2(ctx context.Context) {
	defer wg.Done()

	select {
	case <-time.After(200 * time.Millisecond): // 200 ms is required to succeed
		fmt.Println("routine2 success")
	case <-ctx.Done():
		err := ctx.Err()
		fmt.Println("routine2", err)
	}
}
```
**output**
```
routine2 context deadline exceeded
routine1 context deadline exceeded
```
* In the above example we derived `ctx1` with `context.WithTimeout()` with time limit of 100ms i.e `ctx1` and all it's child context, in this case `ctx2`, would get cancelled after 100ms which we can see in the output.
* If we derive `ctx1` with let's say 400ms, both the routines would run successfully because `routine1()` and `routine2()` requires 300ms and 200ms respectively i.e with `ctx1, cancel1 := context.WithTimeout(ctx, 400*time.Millisecond)` the output would be as bellow
**output**
```
routine2 success
routine1 success
```
* Another fact to notice here is `ctx2` will get cancelled by itself after 250ms or it can be cancelled by it's parent context in this case `ctx1`. Whichever of these condition that happens first would cancel `ctx2`.
```go
func main() {
	wg.Add(2)

	ctx := context.Background()
	ctx1, cancel1 := context.WithTimeout(ctx, 400*time.Millisecond)
	defer cancel1() // avoid context leak
	go routine1(ctx1)

	wg.Wait()
}

func routine1(ctx context.Context) {
	ctx2, cancel2 := context.WithTimeout(ctx, 100*time.Millisecond) // ctx2 will be cancelled after 100ms
	defer cancel2()                                                 // avoid context leak
	defer wg.Done()
	go routine2(ctx2)

	select {
	case <-time.After(300 * time.Millisecond): // 300 ms is required to succeed
		fmt.Println("routine1 success")
	case <-ctx.Done():
		err := ctx.Err()
		fmt.Println("routine1", err)
	}
}

func routine2(ctx context.Context) {
	defer wg.Done()

	select {
	case <-time.After(200 * time.Millisecond): // 200 ms is required to succeed
		fmt.Println("routine2 success")
	case <-ctx.Done():
		err := ctx.Err()
		fmt.Println("routine2", err)
	}
}
```
**output**
```
routine2 context deadline exceeded
routine1 success
```
* In the above example `ctx2` got cancelled in 100ms before the parent context `ctx1`(which has deadline 400ms) and before successful execution of `routine2()`(which required 200ms).
* `routine1()` needed 300ms for successful execution which is less than the deadline of `ctx1`(which has deadline 400ms). So it successfully executed.