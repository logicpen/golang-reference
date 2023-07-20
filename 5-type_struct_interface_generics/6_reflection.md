### Reflection
---------------------
* Reflection allows a program to check a variable's values as well as it's type at runtime. To perform the above 
operations we use `reflect` package which is inbuilt to the standard library.  
* In some cases we might want to perform different operations depending upon data types as runtime, we may get data from 
reading a file or from a network request and we want to determine types in that instant or there is a function which works 
with different types. So in those cases we can leverage reflection successfully.

#### reflect.TypeOf()
* `TypeOf()` takes any type of variable (i.e: `interface{}`) and returns a variable which is of type `reflect.Type` which is an interface. `reflect.Type` interface contains different methods which can be used to get information about **the type of original variable**, that has been passed to the `TypeOf()` method.
```go
type Person struct {
	Name string
	Age  int
}
p := Person{
	Name: "John",
	Age:  25,
}

pt := reflect.TypeOf(p)
fmt.Println(pt.Name())   // name of the type(Person) that the variable(p) is defined from
field, ok := pt.FieldByName("Age")  // check if a field name exists
fmt.Println(field.Index, ok)
```
**output**
```
Person        // prints empty string if variable passed is a slice, pointer or a map etc 
[1] true      // index of Age field is 1 and true because it exists
```
* If the variable is a pointer, map, slice, channel, or array, then the contained type by using `ptrType := reflect.TypeOf(&person).Elem()`.
Then we can use `prtType.Name()`, `prtType.FieldByName()` etc.
* There are other functions such as `NumField()` to find number of fields of a `struct` only. (read documentation)
* `Kind()` method can be used to find the kind of the type. In the above example `p` is of type `Person` but the kind is `struct`.
```go
type Person struct {
	Name string
	Age  int
}
p := Person{
	Name: "John",
	Age:  25,
}

pt := reflect.TypeOf(p)
fmt.Println(pt.Kind()) // struct (Kind() method returns reflect.Kind)
fmt.Println(pt.Kind() == == reflect.Struct) // true
```
* We have `reflect.String`, `reflect.Int` etc to compare with `reflect.Kind` returned by `Kind()` method as shown above.

####  reflect.ValueOf()
* This function takes any variable and returns `reflect.Value`(`struct`) which can be used to get the **underlying value** of the passed variable.
```go
type Person struct {
	Name string
	Age  int
}
person := Person{
	Name: "John",
	Age:  25,
}

pv := reflect.ValueOf(person) // pt is of type reflect.Value
fmt.Println(pv.NumField())    // number of fields i.e 2 (same as defined in reflect.Type)
fmt.Println(pv.Field(0).String(), pv.Field(1).Int()) // John 25 (convert reflect.Value returned by Field() to appropriate types)
```

#### Changing underlying values
* We can change underlying value of any type.
```go
type Person struct {
	Name string
	Age  int
}
person := Person{
	Name: "John",
	Age:  25,
}

pv := reflect.ValueOf(&person).Elem().FieldByName("Age") // pointer to person needs to be passed
pv.SetInt(30)
fmt.Println(person.Age) // 30
```