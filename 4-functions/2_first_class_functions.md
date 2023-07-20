### First Class Functions
Go treats functions like any other variable.We can pass a function as argument, assigned to a variable and return a function from another function.
#### Anonymous Functions
```go
package main

import (
	"fmt"
)

func main() {
	a := func() {
		fmt.Println("hello world")
	}
	a()
	func(str string) {
		fmt.Println(str)
	}("hello world again")
}
```
**output:**
```
hello world
hello world again
```
* In the above example the function assigned to the variable a doesn't have name.The second function also doesn't have any name and invoked immediately by placing `()` after the closing curly braces.The functions which doesn't have a name called `anonymous function`.  
#### Higher-Order Functions
* If a function takes another function as argument or returns a function, then it is called an higher order function.
```go
package main

import (
	"fmt"
)

func highOrdFunc(returnStr func(a string) string) { // passing function to another function
	fmt.Println(returnStr("hello world"))
}

func main() {
	f := func(str string) string {
		return str
	}
	highOrdFunc(f)
}
```
**output:**`hello world`  
```go
package main

import (
	"fmt"
)

func returnFunc() func(a, b int) int { // returns a function
	f := func(num1, num2 int) int {
		return num1 + num2
	}
	return f
}

func main() {
	add := returnFunc()
	fmt.Println(add(1, 2))
}
```
**output:**`3`  
