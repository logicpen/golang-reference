### Methods 
* The concept of method has come form object oriented programming languages. If a function is associated with a particular class is called a method.
* In case of golang, as there is no concept of classes, we can say that the functions associated with a struct or type is called a method. 
* We can say if function has a receiver is called a method
```go
type person struct {
	name    string
	age     int
	address string
}

func (p person) getName() string { // value receiver
	return p.name
}

func (p *person) getAge() int { // pointer receiver
	return p.age
}

func (p person) getAddress() string {
	return p.address
}

func main() {
	person1 := person{
		name:    "John",
		age:     25,
		address: "London",
	}
    p := &person1

    fmt.Println("Name of the person one is ", person1.getName())
    fmt.Println("Age of the person one is ", person1.getAge())   // calling without a pointer even receives pointer
    fmt.Println("Address of the person one is ", p.getAddress()) // calling with a pointer even receives value
}
```
**output:**
```
Name of the person one is  John
Age of the person one is  25
Address of the person one is  London
```  
* The receiver of a function can be a value or a pointer.The above example we defined two methods from which `getName()` is value receiver and `getAge()`
is pointer receiver.
* Pointer receiver methods can mutate the value of the operating instance of a type but value receiver methods can't.
* We can see that even though method `getAddress()` is a value receiver, we can call the method with a pointer. In similar fashion, we can also
call a value receiver method with a pointer.