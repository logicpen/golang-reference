### VARIABLE
-------------
* Variables in go are just names given to the memory locations where the actual values are stored.
Variables are also of specific types i.e string, int, float64. `var` keyword is used to define variables such as var varName type(`var intVar int`)
* While assigning names to variables there should be some rules to be kept in mind
1. Name must start with a letter and can contain letters, numbers and underscore(_).Names start with small letter are unexported i.e can only be used in the same package and the names start with capital letter are exported i.e can be used in other packages which imports the current package.
2. Names should be concise i.e it should be short and should describe, what the variable does
3. It's suggested to used mixed case(camel case) names i.e`var interestRate float64`,`var UserName string`.Let's get into an example
```go
package main

import "fmt"

var globalVarInt int = 1

func main() {

    var localVarInt int = 2

    globalVarInt = 2
    fmt.Println(globalVarInt, localVarInt)
}
```
Variables declared outside of any scope are global variables and variables declared inside any scope is called local variables and are accessible only in the same scopes.Global variables are accessible through the program.`globalVarInt` is global variable and it's accessible inside the `main` function but `localVarInt` is local variable and only be accessible inside the `main` function.

### Declaring and assigning values to a variable
---------------------------------------------
* There are many ways we can declare a variable in golang
* Zero values are assigned to variables by go compiler if the variables are not assigned any value at the time of declaration.`interfaces`, `slices`, `channels`, `maps`, `pointers` and `functions` has zero value as `nil`.
```go
package main

import "fmt"

var globalVarInt int
var globalVarBool bool

func main() {

    var localVarString string
    var localVarFloat float64
    var localVarInt int
    localVarInt = 10
    fmt.Println(globalVarInt, globalVarBool, localVarString, localVarFloat, localVarInt)
}
```
**output:** 
`0 false  0 10`  
* Here `localVarInt` has assigned value 10 after it's declared with zero value. Empty string `""` is zero value of string.
* Initial values can also be assigned to variable as follows.
```go
package main

import "fmt"

var globalVarInt int = 5
var globalVarBool = true

func main() {

    var localVarString string = "hello"
    var localVarFloat = 789.47 // type is float64
    localVarInt := 10 // short declaration
    fmt.Println(globalVarInt, globalVarBool, localVarString, localVarFloat, localVarInt)
}
```
**output:**
`5 true hello 789.47 10`  
* If you are assigning value at the time of declaring the variable you can omit the type and go compiler would automatically assign the type to the variable.`localVarInt := 10` *is called short declaration and this syntax is not acceptable declaring global variables.*
* We can declare variables in a single block as follows.
```go
package main

import "fmt"

var (
    name = "John"
    age = 25
    weight = 63.49
    isMarried = false
)

func main() {

    fmt.Println(name, age, weight, isMarried)
}
```
### CONSTANTS
* As the name suggests, the value of constants can not be changed once declared.In case of variables we can assign new value of same type to a variable.Constants are declared using `const` keyword.
* Constants exists at compile time.
```go
package main

import "fmt"

const (
    name = "John"
    age = 25
    weight = 63.49
    isMarried = false
)
const address = "USA"

func main() {

    fmt.Println(name, age, weight, isMarried, address)
}
```
**output:**
`John 25 63.49 false USA`  
* There is a concept of constants of kind(untyped) and constants of type(typed). If there is no type associated to a constant, we can say that it a constant of kind and if there is a type associated then we can say that it a constant of type.
* Constant of kind can be implicitly converted to other compatible type. 
```go
package main

import "fmt"

type salary float64 // salary type is base on int64

// all constants defied will be end up being type salary
const (
	baseSalary   salary = 10000            // 10000 gets promoted to type salary as it's compatible
	allowance           = baseSalary * 0.1 // allowance gets converted to type salary too
	defaultBonus        = baseSalary * 0.05
)

func addBonus(b salary) {
	fmt.Println("Total salary after adding bonus: ", baseSalary+b)
}

func main() {
	// 5 is constant of kind int and 0.36 is constant of kine float64
	// 5 is implicitly promoted to float64 and var is initialized to type float64
	var var1 = 5 * 0.36

	// const1 will of kind float64 (untyped constant)
	const const1 = 1 / 0.23

	const first int8 = 10
	const const2 = 3 * first // const2 will be of type int8

	addBonus(500) // even though addBonus receives argument of type salary, by implicit conversion(type promotion)
				  // 500 gets converted to type salary
	/*
	bonus := float64(500)
	addBonus(bonus)

	Above code will give error because bonus is of type float64 and we are trying to pass it as type salary.
	*/	
}
```
* `iota` is used to initialized const from zero and automatically incremented for the next constants
```go
package main

import "fmt"

const (
	a = iota
	b
	c
)
const (
	d = iota + 1
	e
	f
)

func main() {
	fmt.Printf("a: %d, b: %d, c: %d\n", a, b, c)
	fmt.Printf("a: %d, b: %d, c: %d\n", d, e, f)
}
```
**output**
```
a: 0, b: 1, c: 2
d: 1, e: 2, f: 3
```
#### The Underscore(_)
* Sometimes values are assigned to `_`(underscore) instead of regular variables.Go compiler complains if we declare a variable but don't use it.Assigning values to `_` is a way to tell the compiler that the variable is not needed and can be ignored without any error.When we define our custom function which may return multiple values and we might want to discard few values returned from the function.
```go
a, _ = func getIntValue() // functions returning 2 integer values
fmt.Println(a)
```
* In the above example the second returned value is discarded and the first values is used further in the program.We will learn more about functions in later tutorials.`_` is also called blank identifier.