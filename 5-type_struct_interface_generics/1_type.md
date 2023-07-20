### Type
* The `type` keyword is used to define new data types. The new datatype can be created for existing data types, with `struct`(later in the tutorial)
or function type.
```go
package main

import "fmt"

type name []string // new type of slice of string
type age int

func main() {
	names := name{"John", "Sam", "Jenny"}
	age := age(25)
	fmt.Println(age)
	for i, n := range names {
		fmt.Println(i, n)
	}
	names = append(names, "Carol")
	fmt.Println(names)
}
```
**output**
```
25
0 John
1 Sam
2 Jenny
[John Sam Jenny Carol]
```
* We can think that in the above program we created a new data type `name` which extends the functionality of slice of string type. It can be think of 
inheritance in object oriented programming.
#### Receiver Function
* Receiver functions are functions which are associated to a particular type. To access the receiver function associated with a particular type, first 
we have to define a variable of same type and use `.` operator to access the function.
```go
type name []string // new type of slice of string

func (n name) printNames() {
	for i, n := range n {
		fmt.Println(i, n)
	}
}

func main() {
	names := name{"John", "Sam", "Jenny"}
	names.printNames()
}
```
**output**
```
0 John
1 Sam
2 Jenny
```
* The receiver function `printNames` is preceded with `(n name)` where `name` denotes the type we are attaching to (in this case `name`) and `n` denotes
hold the reference to actual variable created. Here `names` gets coped to `n` at the type of execution so `n` holds reference to the variable `names`.  
* We can see that even though go doesn't support object oriented design pattern( using class definition ), we can still organize our code with custom
types.
