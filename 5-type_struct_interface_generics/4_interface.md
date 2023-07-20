### Interface
-----
* Interface is also an user defined type which contains set of methods with signature.
* When a custom type implements all those methods defined by an interface, we can safely say that the type implements the interface.
```go
type Person interface {
	getName() string
	getAge() int
	getAddress() string
}

type person struct { // person implements all the methods of Person interface
	name    string
	age     int
	address string
}

func (p person) getName() string {
	return p.name
}

func (p *person) getAge() int {
	return p.age
}

func (p person) getAddress() string {
	return p.address
}

func main() {
	var i Person
	person1 := person{
		name:    "John",
		age:     25,
		address: "London",
	}
	i = &person1 // as person struct implements all methods of interface we can assign person struct to interface

	fmt.Println("Name of the person one is ", i.getName())
	fmt.Println("Age of the person one is ", i.getAge())
	fmt.Println("Address of the person one is ", i.getAddress())
}
```
**output:**
```
Name of the person one is  John
Age of the person one is  25
Address of the person one is  London
```
* In the above example, we have assigned pointer of struct to the interface variable rather than value of the struct. This is because one of the methods defined in interface is implemented by the struct with a pointer receiver.
* So if any of the methods of an interface implemented by a type receives a pointer, the interface variable must accept a pointer.
#### Structure Of Interface
* Interface represented as type, value pair in go.
```go
type Person interface {
	getName() string
	getAge() int
	getAddress() string
}

type person struct {
	name    string
	age     int
	address string
}

func (p person) getName() string {
	return p.name
}

func (p person) getAge() int {
	return p.age
}

func (p person) getAddress() string {
	return p.address
}

func main() {
	var p Person
	person1 := person{
		name:    "John",
		age:     25,
		address: "London",
	}
	p = person1
	fmt.Printf("Type: %T\n", p)
	fmt.Printf("Value: %v\n", p)
}
```
**output:**
```
Type: main.person
Value: {John 25 London}
```
#### Empty Interface
* If an interface has zero methods, called an empty interface. It represented by `interface{}`. Because empty interface doesn't have any methods
any type can implement the empty interface.
```go
func emptyInterface(i interface{}) {
	fmt.Printf("Type: %T\n", i)
	fmt.Printf("Value: %v\n", i)
}

func main() {
	str := "hello world"
	emptyInterface(str)

	num := 20
	emptyInterface(num)

	customStruct := struct {
		name string
	}{
		name: "John",
	}
	emptyInterface(customStruct)
}
```
**output:**
```
Type: string
Value: hello world
Type: int
Value: 20
Type: struct { name string }
Value: {John}
```
#### Method Sets
* When we assign a type to an interface, the concrete data gets a method set depending upon the nature of concrete data.
* If we assign a value to an interface then all the methods that are implemented by using value receiver would be included in method set.
* If we assign a pointer to an interface then all the method that are implemented by using value receiver as well as pointer receiver would be included in method set.
```go
type printer interface {
	getName() string
	getAge() int
}

type person struct {
	name string
	age  int
}

func (p person) getName() string {
	return p.name
}

func (p *person) getAge() int {
	return p.age
}

func printInfo(p printer) {
	fmt.Println(p.getName())
	fmt.Println(p.getAge())
}

func main() {
	per := person{
		name: "John",
		age:  25,
	}
	// printInfo(per) this will give error as getAge() uses pointer receiver
	printInfo(&per)
}
```
**output**
```
John
25
```
* As `getAge()` method is implemented by pointer receiver, we have to pass pointer to the variable i.e `&per` as argument so that both the method would get included in the method set and it will satisfy the `printer` interface.
* If we pass value of the variable i.e `per`, it will only include the method `getName()` in the method set excluding `getAge()` which won't satisfy the interface `printer` and we would get an error.
* If any of the method uses pointer receiver, to satisfy an interface, we have to use pointer to a variable else we can use value of the variable.

#### Composing Interface with interfaces
* We can create new interface with using other interfaces. In the below example `printer` interface is created from `printName` and `printAge` interface.
* Any that wants to satisfy `printer` interface has to implement `getName()` and `getAge()` functions defined in `printName` and `printAge` interface respectively.
```go
type printName interface {
	getName() string
}

type printAge interface {
	getAge() int
}

type printer interface { // created using other interfaces
	printName
	printAge
}

type person struct {
	name string
	age  int
}

func (p person) getName() string {
	return p.name
}

func (p *person) getAge() int {
	return p.age
}

func printInfo(p printer) {
	fmt.Println(p.getName())
	fmt.Println(p.getAge())
}

func main() {
	per := person{
		name: "John",
		age:  25,
	}
	printInfo(&per)
}
```
**output:**
```
John
25
```
#### Composing struct with interfaces & Embedding
* We can create struct with using other interfaces.
```go
type runner interface {
	run()
}

type eater interface {
	eat()
}

type runEater interface { 
	runner // same as having run() method instead
	eater  // same as having run() method instead
}

func print(r runEater) { // takes any type that has implemented runner and eater interface
	r.run()
	r.eat()
}

type animal struct { // not embedding 
	runner runner // any type that implements runner interface
	eater  eater  // any type that implements eater interface
}

type human struct { // embedding
	runner   // any type that implements runner interface
	eater    // any type that implements eater interface
}

type run struct {
	canRun bool
}

func (r run) run() {
	fmt.Println("Running")
}

type eat struct {
	canEat bool
}

func (e eat) eat() {
	fmt.Println("Eating")
}

func main() {
	a := animal{
		runner: run{true},
		eater:  eat{true},
	}
	a.runner.run()
	a.eater.eat()
	// print(a) // will give error as type animal doesn't have run() or eat() method

	h := human{
		runner: run{true},
		eater:  eat{true},
	}
	h.run() // inner type promotion causes calling the function directly without any intermediate field
	h.runner.run() // we can also use intermediate field to 
	print(h) // won't give any error
}
```
* Even though animal has inner types `runner` and `eater`, the methods those are implemented by inner types can not be associated with the parent types i.e `animal`. As `animal` type doesn't have methods `run()` or `eat()`, trying to pass as argument to `print()` function would give an error.
* Type `human` has embedded field which results in inner type promotion so the parent struct i.e `human` gets access to all the methods directly which makes it compatible with `runEater` interface so we can pass `animal` type as argument to the function `print`.
* If we use interface embedding, we can only access the methods. If we try to access the properties of actual struct that is used to create variable, we will get an error. We can not access `catEat` and `canRun` properties in the variable `a` and `h`.
* We can embed struct inside another struct as we did with interface and through type promotion all the methods implemented by inner struct would be directly accessible to parent struct which can be used to make parent struct compatible with interfaces even though parent struct doesn't have any method directly associated.
* We can assess properties of embedded struct as well as methods directly as if they belongs to the parent struct directly.