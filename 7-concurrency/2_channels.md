### Channels
-------------
* Channels are the medium through which goroutines communicate with each other. Channels are responsible for sharing data between goroutines.
* Data can be of any type including pointers. Each channel has a type associated with it which represents the type of data that can be shared using the channel
```go
package main

import "fmt"

func main() {
	var ch chan int // ch is variable of type channel with zero value (nil)
	fmt.Println(ch)
	ch = make(chan int)
	fmt.Printf("Type of ch: %T\n", ch)

	strChannel := make(chan string) // short declaration
	fmt.Printf("Type of strChannel: %T\n", strChannel)    
}
```
**output:**
```
<nil>
Type of ch: chan int
Type of strChannel: chan string
```
#### Sending and receiving data through channels
---
* To write data in to a channel by a goroutine, we have to make sure that there is another goroutine which reads data from the channel.
* In the same manner to read data from a channel by a goroutine, we also have to make sure that there is another goroutine that writes to the channel
* The read from channel expression blocks the execution of the program until some goroutine doesn't write to the channel and vice versa. So we can say that read and write operations are blocking by default.
```go
package main

import "fmt"

func routine(finish chan string) {
	fmt.Println("inside go routine")
	finish <- "goroutine finished" // writes to a channel
}

func main() {
	finish := make(chan string)
	go routine(finish)
	readChannel := <-finish // reads from a channel
	fmt.Printf("Data received from channel: %s\n", readChannel)
	fmt.Println("main routine finished")
}
```
**output:**
```
inside go routine
Data received from channel: goroutine finished
main routine finished
```
* In the above code `goroutine()` takes a channel as parameter and writes to the channel after the end of the function.
* When `routine()` is called it will return automatically and control will return to main routine but as we haven't written to the finish channel the reading from the channel(`readChannel := <-finish`) will block the execution of the main routine.
* Again control will go inside `routine()` and data will be written to the channel which will enable the `readChannel := <-finish` line in the main routine to read from the channel.
* After reading from the channel rest of the main routine will finish executing. We can see that we don't need to use `sync` or `time` packages to stop the execution of main routine. 
```go
func routine(msg string, ch chan string) {
	for i := 0; i <= 2; i++ {
		ch <- msg
	}
	close(ch)
}

func main() {
	ch := make(chan string)
	go routine("hello world", ch)

	for {
		msg, open := <-ch
		if !open {
			break
		}
		fmt.Println(msg)
	}
}
```
**output:**
```
hello world
hello world
hello world
```
* In the above example in the main routine we are trying to read from channel in an infinite loop. In the `routine()` we are sending values through routine and after we finish sending we close the channel. `open` become false once we closed the channel and we break out of the loop.
* If we don't close the channel and try to read from the channel after `routine()` is finished executing, we can go into deadlock. If we try to read from a closed channel we will get zero value for the channel's type(empty string) without blocking. So we check if a channel is closed or not and break out of the loop.
```go
func routine(msg string, ch chan string) {
	for i := 0; i <= 2; i++ {
		ch <- msg
	}
	close(ch)
}

func main() {
	ch := make(chan string)
	go routine("hello world", ch)

	for msg := range ch {
		fmt.Println(msg)
	}
}
```
* In the above example we iterate over a channel and printing the values as long as channel is open. This is the better way than the previous example.
```go
func main() {
	ch := make(chan string)
	ch <- "hello world"

	msg := <-ch
	fmt.Println(msg)
}
```
**output:**`fatal error: all goroutines are asleep - deadlock!`  
* In the above example we try to send a value but the line which try to read from the channel doesn't get to execute so the program goes in a deadlock. We can solve the issue by sending from another goroutine or by creating a buffered channel.

#### Deadlock
-------------
```go
func main() {
	forever := make(chan bool)
	go func() {
		for i := 0; i < 3; i++ {
			fmt.Println(i, " inside go routine")
			time.Sleep(1 * time.Second)
		}
	}()
	fmt.Println("outside go routine")
	<-forever
}
```
**output**
```
outside go routine
0  inside go routine
1  inside go routine
2  inside go routine
fatal error: all goroutines are asleep - deadlock!
```
* **A deadlock is when no goroutines have a chance of making progress**. 
* So in the above program the anonymous go routine had a chance of progressing up to 3 seconds. So once 3 seconds are up, anonymous goroutine finished executing and `main()` is blocked, we entered into deadlock.

### Buffered channels
----------------------
* The channels discussed before is called unbuffered channels i.e doesn't have capacity.
* Buffered channel has capacity i.e we can specify how much data we can write to a channel. We can fill the buffered channel without the corresponding receiver and it won't block the code.
* If the channel holds maximum data, the writing will be blocked until a goroutine reads from the channel.
```go
func main() {
	ch := make(chan string, 2)
	ch <- "hello world"

	msg := <-ch
	fmt.Println(msg)
}
```
**output:**```hello world```  
```go
func main() {
	ch := make(chan string, 2)
	ch <- "hello world"
	ch <- "hello world again"

	msg := <-ch
	fmt.Println(msg)
	msg = <-ch
	fmt.Println(msg)
}
```
**output:**
```
hello world
hello world again
```
* In the above examples we can see, we can fill up to the capacity of the channel and it won't block the code. We have to read from channel by range if we want to read all the values. 
* If we try to write to channel beyond it's capacity without reading from it, the program will go to deadlock. If we want to send more values, we first have to read form the channel which will empty the channel to make space for more values to send to the channel. See from the examples below
```go
func main() {
	ch := make(chan string, 2)
	ch <- "hello world"
	ch <- "hello world again"
	ch <- "deadlock"

	msg := <-ch
	fmt.Println(msg)
	msg = <-ch
	fmt.Println(msg)
}
```
**output:**`fatal error: all goroutines are asleep - deadlock!`  
```go
func main() {
	ch := make(chan string, 2)
	ch <- "hello world"
	ch <- "hello world again"
	<-ch // reading from ch which makes space to send value
	ch <- "no deadlock"

	msg := <-ch
	fmt.Println(msg)
	msg = <-ch
	fmt.Println(msg)
}
```
**output:**
```
hello world again
no deadlock
```
```go
func main() {
	ch1 := make(chan string)
	ch2 := make(chan string)

	go func() {
		for i := 0; i <= 5; i++ {
			ch1 <- "500 millisecond"
			time.Sleep(time.Millisecond * 500)
		}
	}()
	go func() {
		for i := 0; i <= 5; i++ {
			ch2 <- "2 second"
			time.Sleep(time.Second * 2)
		}
	}()

	for i := 0; i <= 5; i++ {
		fmt.Println(<-ch1) // block the loop for 500 millisecond
		fmt.Println(<-ch2) // block the loop for two seconds
		// it will take 2500ms(2.5 seconds) for the loop to complete one iteration
	}
}
```
* If we run the above code we will see that reading from `ch1` will be delayed even though it is ready to send value much sooner because program has to wail for two seconds to read from `ch2`(it can be solved using `select` statement).
#### Select Statement
----------------------
* GoLang `select` statement is like the `switch` statement, which is used for multiple channels operation. This statement blocks until any of the cases provided are ready. It will select the channel that is ready for operation. If multiple channels are ready then it will randomly choose one of them.
```go
func main() {
	ch1 := make(chan string)
	ch2 := make(chan string)

	go func() {
		for i := 0; i <= 5; i++ {
			ch1 <- "500 millisecond"
			time.Sleep(time.Millisecond * 500)
		}
	}()
	go func() {
		for i := 0; i <= 5; i++ {
			ch2 <- "2 second"
			time.Sleep(time.Second * 2)
		}
	}()

	for {
		select {
		case msg1 := <-ch1:
			fmt.Println(msg1)
		case msg2 := <-ch2:
			fmt.Println(msg2)
		}
	}
}
```
* The above code would print message from ch1 more frequently as it is available early than ch2.
