### Type Assertion
* Type assertion is a way to check if a particular variable is of specific type. Let's say we defined a variable using `interface{}`(empty interface) and we want to know the underlying concrete type. In the below code how we can do it. 
```go
func main() {
	var unknown interface{} = "this a string" // without type information
	known, ok := unknown.(string) // asserting the type and assigning to another variable i.e if variable unknown is of type string
	fmt.Println(known, ok) // variable known would contain the actual value if assertion is successful ok would be true if assertion will be successful
}
```
**output**`this a string true`  
* If we don't know the type of any variable we can use `type` and `switch` to inspect the underlying concrete type and perform some operation accordingly. This is referred as type switch
```go
func main() {
	var unknown interface{} = true
	switch known := unknown.(type) {
	case string:
		fmt.Println("Type is string and the value is", known)
	case int:
		fmt.Println("Type is int and the value is", known)
	default:
		fmt.Printf("Type is %T and the value is %v\n", known, known)
	}
}
```
**output**`Type is bool and the value is true`  
* Let's say we have an interface(`type shape interface`) and different struct types(`type circle struct`, `type rectangle struct`, `type triangle struct`) have implemented the same interface. Now we have an slice of interface values(`[]shape`).
* In such case there is no way know the type of any element that present in the given slice. So here we can also use type switch to identify the underlying type.
```go
// to do example
``` 