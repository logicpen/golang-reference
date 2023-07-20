### Pointer
* Everything in go is pass by value (i.e what you see is what you get). So when we pass a pointer, we are passing another value but the value just happens
to be a pointer.
* Pointers are also a kind of variable which stores memory address of another variable.When we create a variable and assign a value to it, compiler stores the value in a memory location and label it with the variable name.Using pointers, we can get the memory address of the variable and store it in another variable.
```go
package main

import (
	"fmt"
)

func main() {
	num := 10
	var numPtr *int = &num
	fmt.Printf("Type of numPtr is %T\n", numPtr)
    fmt.Println("Address of num is", numPtr)
    
    // new() function is also used to create pointers
	str := "a"
	strPtr := new(string) // points to an empty string
	fmt.Printf("Type of strPtr: %T, address in strPtr: %v value of strPtr: %s\n", strPtr, strPtr, *strPtr)
	strPtr = &str
	fmt.Println("After assigning strPtr")
	fmt.Printf("Type of strPtr: %T, address in strPtr: %v value of strPtr: %s\n", strPtr, strPtr, *strPtr)

}
```
**output:**
```
Type of numPtr is *int
Address of num is 0xc00001a0e0
Type of strPtr: *string, address in strPtr: 0xc000010210 value of strPtr: 
After assigning strPtr
Type of strPtr: *string, address in strPtr: 0xc000010200 value of strPtr: a
```
* In go `&` operator is used to get the memory location of a variable(`&varName`).`*` with type is used to declare a pointer variable(`*int` -> pointer of a variable of type int).In the above example `numPtr` is a pointer of type `*int` means `numPtr` stores memory address of a variable of `int` type, in this case `num`,
* To get the value stored in the memory location we also use `*` operator.This process is called dereferencing of pointer.(`fmt.Println(*numPtr)` prints the value which is stored in the memory location the pointer points to)
* We can declare a pointer without assigning a value to it.In such case the pointer gets it's zero value i.e `nil` 
* We can also change the value stored in the memory location through pointers(`*numPtr=11` will change the value of the variable `num`)
```go
num := 10
var numPtr *int
fmt.Println("Initial value of pointer numPtr is", numPtr)
numPtr = &num
fmt.Println("Assigned value of pointer numPtr is", numPtr)
fmt.Printf("Value stored in location %v is %d\n", numPtr, *numPtr) // retrieves value from memory location
*numPtr = 11 // changing the value in the memory location will change the value of variable num
fmt.Println("New value of variable num is,", num)
```
**output:**
```
Initial value of pointer numPtr is <nil>
Assigned value of pointer numPtr is 0xc00010c000
Value stored in location 0xc00010c000 is 10
New value of variable num is, 11
```
* Pointer arithmetic is not possible in go.Pointers can be passed as argument to a function which we will see in future.
* As pointers refer to the memory location of a variable, any change using pointer would change the value of the original variable.

### Escape Analysis
* Basic understanding of function and struct is required.
```go
type user struct {
	name string
	age  int
}

func main() {
	u1 := f1()
	u2 := f2()
}

func f1() user {
	u1 := user{
		name: "John",
		age:  25,
	}
	return u
}

func f2() *user {
	u2 := user{
		name: "John",
		age:  25,
	}
	return &u
}
```

```
  Call Stack      Heap             Call Stack       Heap
   |      |     |      |            |       |     |      | 
   |f1(u1)|     |      |            |f2(*u2)|     |      |        
   |------|     |      |            |-------|     |  u2  |
   | main |     |      |            | main  |     |      |   
   | u1   |     |      |            |u1 *u2 |     |      |         
   |______|     |______|            |_______|     |______|
main go routine                  main go routine
```
* Escape analysis(Static code analysis) determines whether a variable should initialized on stack or heap. It's done by compiler. 
* Stacks are self cleaning i.e when `main` calls `f1` or any other function it will take a slice of stack on top of main slice and clean it and it will
initialize variables for that function inside the slice. All the variables stored in the slice above the main slice that has been created by previous function calls will be cleaned in the cleaning process.
* In the above example when `f1` returns and `f2` is called, the memory used by `f1` will be cleaned and will allocated to `f2` for use. 
* As we know everything in go is pass by value. In case of `f1`, `main` will get a copy of `u1`. So now we have two copies one in `main` and another in `f1`.
* In the second function call `f2`, all the variable are cleaned up above the main slice so `u1` will cleaned from slice above the `main` slice but we still have a copy of `u1` in `main`.
* In the second function call we are initializing `u2` and returning a pointer to the value `u2`( in the function `f2`). But we know that `u2` going to be cleaned up in the next function call while main will still have a pointer(`*u2`) that points towards a value(`u2` in `f2`) that doesn't exist.
* To avoid this situation the compiler during escape analysis determines that `u2` should stay on heap instead of stack.
* There is a cost to it. When something gets initialized in heap garbage collector has to clean it up as heap not self cleaning which in turn cause performance issue.
* Even though `u2` is in the heap, while accessing `u2` in `f2` we use value syntax not the pointer syntax.  
