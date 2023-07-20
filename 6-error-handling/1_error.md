## Error Handling
* Like other programming languages go also has ways to handle error in the code.
* We can see that there are many standard library function return two values. First value is some kind of result generated because of successful execution of the function and second is an error which generated if there is an error while executing the function.
* If we get some result form the function, then error becomes `nil` otherwise we get an error which gives us information about why the error has occurred.
```go
// todo file opening example
```
* If we checkout `error` in the standard library, we will find that it is an interface with the signature as below.
```go
type error interface {
    Error() string
}
```
* The above code tells us that any type that implements `Error()` function which returns an `string`(error string that gives valuable information about error) satisfies `error` interface. It's a builtin type so we don't need to import it form any inbuilt package.
* As we know interfaces are not concrete. They reference to concrete data underneath. So the actual implementation of `error` interface is done by another package called `errors`.
```go
// holds the actual error string
type errorString struct {
    s string
}

func (e *errorString) Error() string { // satisfies the error interface
    return e.s
}

func New(test string) error { // factory function to generate and return error 
    return &errorString{text}
}
```
* We can create our own errors with the help of `errors` package when there is necessity of defining our own error. Lets say an user entered an string where an integer was expected. so avoid such scenarios we can crate out own errors and return it.
```go
package main

import (
	"errors"
	"fmt"
)

func main() {
	if err := errFunc(); err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println("No error found")
}

func errFunc() error {
	return errors.New("something went wrong")
}
```
**output:** `something went wrong`  
