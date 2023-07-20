### Concurrency
-------------------
* Concurrency is running processes in alternate intervals. Lets say we have two processes are running concurrently. Go executes the first 
process and then pauses the process and start executing the second process. Again it pauses second process and executes the first process 
from where it has left before.
* The above process is called context switching. Go switches the execution context between processes.
* A parallel execution is running process in multiple cores of a processor without pausing any process.
* Go support concurrency by using goroutines and channels.

#### Goroutine
-----------------
* Goroutines are functions or methods that runs concurrently with other functions.
* Goroutines are light and doesn't take much memory and has very less stack size in memory. That's the reason it is preferred over threads
* Goroutines are created by using the keyword `go` in front of a function or method 
* The important thing to remember is `main()` has it's own routine. Let's call it main routine. And other routines run inside this main routine
* To get results from other goroutines, we have to make sure that the main routine should run until all the other goroutines finished execution. 
* Goroutines returns immediately when called. Then the control goes to the next line for execution. In the bellow code we have only one goroutine and one print statement.
* When control starts the single goroutine, it returns immediately and goes to next line(the print statement).Then the print statement executes and as there is no lines after that, the main goroutine exits and the single goroutine doesn't get any chance to produce result.
```go
package main

import "fmt"

func routine() {
	fmt.Println("inside go routine")
}

func main() {
	go routine()
	fmt.Println("main goroutine ends")
}
```
**output:**`main goroutine ends`  
* We need to find a way to make the main goroutine wait until the other gorutines finished executing. We can use `time` package to make the main goroutine sleep for specified time or `sync` package to make the main goroutine wait until all the launched goroutines are finished executing.
```go
package main

import (
	"fmt"
	"time"
)

func routine() {
	fmt.Println("inside go routine")
}

func main() {
	go routine() // returns immediately
	time.Sleep(1 * time.Second) // control comes here but sleeps for one second so routine() gets chance to run
	fmt.Println("main goroutine ends") // executes after one second 
}
```
**output:**
```
inside go routine
main goroutine ends
```
#### sync Package
----------------------
* Using `time.Sleep()` to pause the main goroutine so that other goroutines could run is not efficient. `sync` package in the standard library is useful to synchronize different goroutines. `sync` package provides many APIs to synchronize goroutines.
* We can use `waitGroup` from `sync` package to facilitate the execution of `goroutines`.
```go
package main

import (
	"fmt"
	"sync"
)

var wg sync.WaitGroup

func routine(index string) {
	defer wg.Done() // calls when function finishes execution
	fmt.Printf("inside go routine %s\n", index)
}

func main() {
	wg.Add(2) // count of number of goroutines
	go routine("one")
	go routine("two")
	wg.Wait() // waits for the program to finish execution of all goroutines

	fmt.Println("main goroutine ends")
}
```
**output:**
```
inside go routine two
inside go routine one
main goroutine ends
```
* In the above example `Add()` creates a counter from the number passed as argument, which represents number of goroutines.
* `Done()` is called at the end of each goroutine and it decrements the counter started by `Add()`
* `Wait()` blocks the execution of the program until the counter is zero i.e all the goroutines finished executing.
```go
func routine(msg string) {
	for i := 0; i <= 2; i++ {
		fmt.Printf("%s\n", msg)
	}
}

func main() {
	var wg sync.WaitGroup
	wg.Add(1)
	go func() {
		routine("hello world")
		wg.Done()
	}()
	wg.Wait()
}
```
**output:**
```
hello world
hello world
hello world
```
* In the above code anonymous function calls `routine()` but if we can pass a pointer to the `routine()` and use `Done()` inside `routine()`. But we shouldn't do that because `routine()` should only contain the logic not the routine related stuffs. So we can do it with an anonymous function. 
#### Data races
-------------------
* Data race is when two or more goroutines operation on the same memory location. Let's examine the below example
```go
package main

import (
	"fmt"
	"sync"
)

var counter int

func main() {
	const grs = 2

	var wg sync.WaitGroup
	wg.Add(grs)

	for i := 0; i < grs; i++ {
		go func() {
			for count := 0; count < 2; count++ {
				value := counter
				value++
				fmt.Println(counter)
				counter = value
			}
			wg.Done()
		}()
	}

	wg.Wait()
	fmt.Println("counter", counter)
}
```
**output**
```
0
1
0
1
counter 2
0
1
1
2
counter 3
```
* We expect the above program to increment the global variable `counter` to `4` but as we can see the result for two different tests are different and the `counter` did not get incremented to 4 in any of the tests.
* When we call `Println()` in the first goroutine, it internally does a system call which results an context switch so we pause the first goroutine in the middle before updating the `counter` variable and start the second goroutine.
* When second goroutine access the `counter` variable, it gets the old value `i.e 0` and increments to `1` then second goroutine calls `Println()` and again context switch happens and first goroutine updates the `counter` variable to `1` but as we can see in the next update by second goroutine counter would be set to `1`.
* This can be prevented telling the scheduler to execute a particular block of code synchronously without any context switches. We can use `sync/atomic` package or `sync.Mutex` to avoid data races.
#### sync.Mutex
----------------
* With the help of `sync.Mutex` we can lock the block of code we want to be execute atomically (without context switch) and prevent another goroutine do perform any kind of operation on the shared value
```go
import (
	"fmt"
	"sync"
)

var counter int

func main() {
	const grs = 2

	var mutex sync.Mutex
	var wg sync.WaitGroup
	wg.Add(grs)

	for i := 0; i < grs; i++ {
		go func() {
			for count := 0; count < 2; count++ {
				mutex.Lock() // lock the code block to execute atomically
				value := counter
				value++
				fmt.Println(counter)
				counter = value
				mutex.Unlock()// unlock the code block
			}
			wg.Done()
		}()
	}

	wg.Wait()
	fmt.Println("counter", counter)
}
```
**output**
```
0
1
2
3
counter 4
```
#### sync.Cond
-------------
* Let's say we have a routine which squares the elements of an integer array and we wants to send signal to the main routine once all the elements of the array processed. `sync.Cond` is useful in this situation.
```go
var arr []int

func process(c *sync.Cond) {
	c.L.Lock() // process locks the array for processing after c.Wait() releases the lock
	defer c.L.Unlock()
	fmt.Println("process goroutine starts processing elements")
	for i, ele := range arr {
		arr[i] = ele * ele
	}
	fmt.Println("process goroutine finished processing and broadcasting the signal to main goroutine")
	c.Broadcast() // Broadcast() wakes all goroutines waiting on c.
}

func main() {
	fmt.Println("main goroutine starts")
	l := sync.Mutex{} // main is the owner of the lock
	l.Lock()          // locks the array for initialization of elements
	arr = []int{1, 2, 3}
	c := sync.NewCond(&l)
	fmt.Println("main goroutine starts the process goroutine and waits for it to complete")
	go process(c)
	c.Wait() // temporarily releases the lock so that process can access the array
	fmt.Println("process is finished and main goroutine finishing")
	fmt.Println("Processed elements: ", arr)
}
```
**output:**
```
main goroutine starts
main goroutine starts the process goroutine and waits for it to complete
process goroutine starts processing elements
process goroutine finished processing and broadcasting the signal to main goroutine
process is finished and main goroutine finishing
Processed elements:  [1 4 9]
```
* The above can also be accomplished by using `sync.WaitGroup` as we have only 2 goroutines.
* `Wait()` atomically unlocks `c.L` and suspends execution of the calling goroutine. After later resuming execution, Wait locks `c.L` before returning. Unlike in other systems, `Wait()` cannot return unless awoken by `Broadcast()` or `Signal()`.
* `Broadcast()` wakes all goroutines waiting on `c`, `Signal()` wakes one goroutine waiting on `c`, if there is any.
* Let's say we have a shared resource and multiple goroutines are trying to read from it. `sync.Cond` can be used to synchronize the operations i.e send signal when the resource is available and pause the reading when data is written to the resource.
```go
var done = false

func read(name string, c *sync.Cond) {
	c.L.Lock()
	for !done {
		c.Wait()
	}
	fmt.Println(name, "starts reading")
	c.L.Unlock()
}

func write(name string, c *sync.Cond) {
	fmt.Println(name, "starts writing")
	time.Sleep(time.Second)
	c.L.Lock()
	done = true
	c.L.Unlock()
	fmt.Println(name, "wakes all")
	c.Broadcast()
}

func main() {
	cond := sync.NewCond(&sync.Mutex{})

	go read("reader1", cond)
	go read("reader2", cond)
	go read("reader3", cond)
	write("writer", cond)

	time.Sleep(time.Second * 3)
}
```
**output:**
```
writer starts writing
writer wakes all
reader3 starts reading
reader1 starts reading
reader2 starts reading
```