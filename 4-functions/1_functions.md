### Functions
While making softwares it's common to group code into a single unit which performs a specific task.Grouping code lines in a single unit is also good for maintainability and debugging(finding error).It's common in every programming language.There are various ways code can be grouped(functions,classes,modules,packages etc).Functions are the one of the very basic ways to group code.  

Function can be defined as a independent block of code grouped together and intended to perform a specific task.The code inside a function is independent of the code outside of the function.  

Function can take zero or more parameters as input and can output zero or more processed values.`func main(){...}` is also a function which gets called by go compiler when we run a go program.`func` keyword is used while defining a function.We can define our own functions to perform specific tasks.  

After defining a function we have to call the function to run the code inside of that function.
```go 
package main

import (
	"fmt"
)

// function with zero input parameter and zero output value
func printHello() {
	fmt.Println("hello world")
}

// function takes slice of int type and returns a value of float64 type
func average(arr []int) float64 {
	sum := 0
	for _, v := range arr {
		sum += v
	}
	average := float64(sum) / float64(len(arr))
	return average
}

func main() {
	intArr := []int{3, 4, 5, 6, 7}
	printHello()               // function call
	average := average(intArr) // function call
	fmt.Println("The average is ", average)
}
```
**output:**
```
hello world
The average is  5
```
* In the above example we defined two functions and called those functions in side the `main()` function to execute the code inside the functions.`printHello()` doesn't take any value as parameter and doesn't output(return) any value.It just prints `hello world` to the console.
* The `average()` function does a lot of stuffs.First it takes one argument which is an array of int type.Second it returns a value of type `float64`.`return` statement is used to return a value from a function.We can stored the returned value in a variable of same type.
* Function `average()` takes `arr` as a parameter which is of type `[]int`.When we pass any variable of type `[]int` to `average()` as argument, the value from the variable gets copied to the `arr`.In this case `intArr` copied to `arr` and we got the whole date in `arr`.Then we calculated average using `arr` variable.
* When functions get called by another function, they can't access the variables defined in the calling function unless it's passed explicitly.`average()` function can not access `intArr` unless we pass it to the function.
* We can name the variable to be returned.
```go
func f() (val int) {
	val = 1 // we don't need to define val 
	return
}
```
* We can return multiple values from a function
```go
func f() (int string) {
	return 1, "a"
}
func main() {
	x, y := f()
}
```
#### Variadic Function
Variadic functions can take variable number of arguments.The arguments form a slice with length equal to number of arguments passed.The syntax is `func f(a int, b ...int) {...}`.
```go
func main() {
	varFunc(1, 2, 3)
}

func varFunc(vals ...int) {
	fmt.Println(vals, len(vals))
	fmt.Printf("Type of val: %T\n", vals)
}
```
**output:**
```
[1 2 3] 3
Type of val: []int
```
* In the above parameter `vals` becomes a slice of type int with all the arguments passed to the function stored with in the slice(it's similar to `vals := []int{1, 2, 3}`).
* If we have a variadic function, we can unpack a slice of same type while passing as argument.
* Variadic argument always the last parameter on the variadic function. We can not make the first argument as variadic argument.
```go
func main() {
	marks := []int{34, 56, 78}
	name := "John"
	printMark(name, marks...) // unpacking a slice
}

func printMark(name string, marks ...int) {
	fmt.Println(name, " has marks: ", marks) // John  has marks:  [34 56 78]
}
```

#### Closure
Instead of calling a function we can create function inside of a function.The local function can access all the local variables of the parent function.A function which reference variables outside of it's scope is called closures.
```go
package main

import (
	"fmt"
)

func main() {
	x := 1
	add := func() int {
		x++
		return x
	}
	fmt.Println(add()) // prints 2
	fmt.Println(add()) // prints 3
}
```
* In the above example `add` is a local variable of type `func() int`, so we can call the function associated with `add` variable like `add()`.The local function(`add()`) references to `x` which is outside of it's scope.So add is called a closure.
* The function which is assigned to `add` variable is called anonymous function because it doesn't have identifier(name).
```go
func getNextNum() func() uint {
	i := uint(0)
	return func() uint {
		num := i
		i++
		return num
	}
}

func main() {
	getNext := getNextNum()
	fmt.Println(getNext())
	fmt.Println(getNext())
	fmt.Println(getNext())
	fmt.Printf("%T\n", getNext)
}
```
**output:**
```
0
1
2
func() uint
```
* Go supports first class functions i.e functions can be passed as arguments, assigned to variables and can be returned from another functions.In the above example we returned a function form `getNextNum()` and assigned to `getNext` variable. 

* We can pass pointer to a function and if there is any change happens inside the function, the same change would manifest outside of the function.
```go
func changePtr(ptr *int) {
	*ptr = *ptr + 1
}

func main() {
	a := 20
	changePtr(&a)
	fmt.Printf("value of a after passing to the function %d\n", a)
}
```
**output:**`value of a after passing to the function 21`  
