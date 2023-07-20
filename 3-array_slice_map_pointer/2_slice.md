### Slice
-------------
Slice is a data structure like array but with flexibility.We can increase the size of a slice by adding elements in it unlike arrays which has fixed length.Slices can be made from an existing array, by declaration statement or by `make()` function.Under the hood slices are just references to arrays.
```go
package main

import (
	"fmt"
)

func main() {
    var intSlice []int // nil slice
    strSlice := []string{"a", "b", "c"} // by declaration statement
    
    // make function takes type, length and capacity(optional) as parameter
    floatSlice := make([]float64, 3, 4) // if capacity is not provided then it get set to the same value as length
}
``` 
#### Some Points To Note
* When we create slice, we create 3 fields in the memory i.e a pointer points to the backing array, length and capacity of the slice. Length is the total number of element we can access and capacity is total number of element the backing array can hold. 
* Capacity can be larger than length. Capacity is generally for future growth as slices can grow. It's important to remember that can only access the elements between the index position  `0` to `length - 1`. If we try to access beyond the index `length-1`, we will get an error.
```
 ______
|      |                                            
| ptr  |---------> points to array              
|______|                                  
|      |                            
|len(5)|                                      
|______|
|      |                            
|cap(5)|                                      
|______|
```
* We can imagine slice as a struct(refer to struct) as below. So when we assign a slice to another variable of same type or pass to a function parameter we actually make a copy the struct representation of slice(which contains a pointer to the backing array) and pass it to the function parameter or assign it to another variable.
* So in the same manner everything in go is pass by value.
```go
    type slice struct {
        array     *internalArray
        length    int
        capacity  int
    }
```
### Appending
-----------
* While appending to a slice we add element at the end of the slice and the length of the array increases with every append operation.
* If we have enough capacity to add new element to the slice, then new backing array isn't required to add new element. If the capacity and length are equal i.e there is no room(capacity) to append new element to the existing backing array then a new array would be created and with increased capacity and the slice would point to the new backing array.
* In the below example we are passing `nameList` slice as parameter to the `addName` function. What happens is we make a copy of the struct representation of the slice `nameList` and pass it to the function i.e assign to `names` which is a function parameter of type `[]string`. 
* It's important to understand that `nameList` and `names` are different structs having a value which is a pointer that points to the same backing array.
* Inside the function when we try to append new names to the function, we create a new array and add names to it as the backing array doesn't have enough capacity to hold new element(capacity of the backing array is 3).
* Now the `names` variable is pointing to the new array with increased capacity but the `nameList` variable still points to the old array.
```go
func main() {
	nameList := []string{"John", "Jack", "Bill"} // length and capacity both are 3
	addName(nameList)
	fmt.Println(nameList)
}

func addName(names []string) {
	names = append(names, "Sam")
	fmt.Println(names)
}
```
**output**
```
[John Jack Bill Sam]
[John Jack Bill]
```
* If we want to change the content of the slice inside the function then it's better to pass pointer to the slice as function parameter.
```go
func main() {
	names := []string{"John", "Jack", "Bill"}
	addName(&names)
	fmt.Println(names)
}

func addName(names *[]string) {
	*names = append(*names, "Sam")
	fmt.Println(*names)
}
```
**output**
```
[John Jack Bill Sam]
[John Jack Bill Sam]
```
#### Slice Of Slice
* We can take slice of a slice. What does this mean is we car create a new slice from an existing slice or an existing array. The new slice created would internally point to the same backing array, which the parent slice points to or to the same parent array if the slice is created from an array.
* So any change in the new slice would reflect in the parent slice or array.
```go
func main() {
	intArray := [5]int{1, 2, 3, 4, 5}
	var intSlice []int = intArray[1:4] // from intArray
    // intSlice := intArray[1:4]  (short hand syntax for creating slice)
    fmt.Println(intSlice)

}
```
* If a slice gets created from an existing array by `array[start:end]` syntax, the slice created would point(reference) to the element at start index and will hold the elements including at start index up to but excluding the element at end index.The length of the slice would be the elements present in the slice and the capacity would be the total number of elements the parent array have starting from the `start` index.
```go
intArray := [5]int{1, 2, 3, 4, 5}
var intSlice []int = intArray[1:4] // intSlice points to element at index 1 i.e 2
// capacity of intSlice would be 4 because total number of elements of intArray starting from 
// start index i.e 1 is 4 [2, 3, 4, 5]
fmt.Printf("Slice: %v, Length: %d, Capacity: %d\n", intSlice, len(intSlice), cap(intSlice))
```
**output:**`Slice: [2 3 4], Length: 3, Capacity: 4`  
* As slice holds reference to the underlying array any changes made in the slice would reflect in the parent array.
```
[1, 2, 3, 4, 5]
   [2, 3, 4]     (slice points to the start index in this case index 1)
   <------->     length = 3
   <---------->  capacity = 4
```
* as slice points to the element at the index 1 of intArray so element at index 1 of slice would be equivalent to element at index 2 of the array
```go
intArray := [5]int{1, 2, 3, 4, 5}
var intSlice []int = intArray[1:4] 
intSlice[1] = 11
fmt.Printf("Slice: %v, Array: %v\n", intSlice, intArray)
```
**output:**`Slice: [2 11 4], Array: [1 2 11 4 5]`  
* As slices are expandable we can add elements at the end of a slice using `append()` function.In the below example `intSlice` has capacity 4 with length 3.If we append a single value to the intSlice, the original array will also receive the change.
```go
intArray := [5]int{1, 2, 3, 4, 5}
var intSlice []int = intArray[1:4]
intSlice = append(intSlice, 6)
fmt.Printf("Slice: %v, Array: %v\n", intSlice, intArray)
```
**output:**`Slice: [2 3 4 6], Array: [1 2 3 4 6]`  
* We saw that the capacity is of a slice is determined from the underlying array. When we try to change the slice by assigning elements or appending elements within the initial limit, the changes get reflected in the parent array.
* If we try to append more elements beyond the capacity of the slice, now the slice needs a bigger array to point at so that the capacity can be increased.What happens is a whole new array gets created with double the length, all the elements get copied to the new array and the slice now points to the new array with double capacity. 
* So any change in the new slice with the increased capacity won't affect the parent array as slice is not pointing to the parent array anymore.
```go
intArray := [5]int{1, 2, 3, 4, 5}
fmt.Printf("intArray before appending: %v\n", intArray)
var intSlice []int = intArray[1:4]
fmt.Printf("intSlice length before: %d intSlice capacity before: %d\n", len(intSlice), cap(intSlice))
intSlice = append(intSlice, 6)
fmt.Printf("intSlice: %v, intArray: %v\n", intSlice, intArray)
fmt.Printf("intSlice length after: %d intSlice capacity after: %d\n", len(intSlice), cap(intSlice))
fmt.Println("----------------------------------------------------------------")
strArray := []string{"a", "b", "c", "d"}
strSlice := strArray[1:3]
fmt.Printf("strArray before: %v\n", strArray)
fmt.Printf("strSlice: %v, Length before: %d, Capacity before: %d\n", strSlice, len(strSlice), cap(strSlice))
strSlice = append(strSlice, "e", "f")
fmt.Printf("strArray after appending: %v\n", strArray)
fmt.Printf("Slice after: %v, Length after: %d, Capacity after: %d\n", strSlice, len(strSlice), cap(strSlice))
```
**output:**
```
intArray before appending: [1 2 3 4 5]
intSlice length before: 3 intSlice capacity before: 4
intSlice: [2 3 4 6], intArray: [1 2 3 4 6]
intSlice length after: 4 intSlice capacity after: 4
----------------------------------------------------------------
strArray before: [a b c d]
strSlice: [b c], Length before: 2, Capacity before: 3
strArray after appending: [a b c d]
Slice after: [b c e f], Length after: 4, Capacity after: 6
```
* In the first case number of elements that gets appended is in the range of the slice so the slice points to the original array and changes made in slice can be seen in the array.In the second case number of elements that gets appended is out of the range of the slice so a new array gets crated and the slice points to the new array so the parent array remains unchanged after appending to slice.
* If we try to create a slice with declaration statement instead of creating from an array, under the hood a array gets created and a slice is returned referencing to the array.When we try to append elements to the slice the similar event happens as discussed before.
* We can create slice with `make(type, len, cap)`.Total number of elements would be equal to length and would be initialized to their zero values.By default the capacity would be equal to length if capacity is not given in the `make()` function.
* We can have multidimensional slices like we saw in array.Syntax is the same without specifying the length.
* If there is a large array and we need only a part of it in our program, we use slice but it still points to the original array under the hood so the array can not be garbage collected.We can make a copy of slice and dereference the bigger array so it can be garbage collected.
```go
intArray := [8]int{1, 2, 3, 4, 5, 6, 7, 8}
intSlice := intArray[1:5]
sliceCopy := make([]int, len(intSlice))
fmt.Printf("intSlice: %v sliceCopy: %v\n", intSlice, sliceCopy)
copy(sliceCopy, intSlice)
fmt.Println("sliceCopy after copying:", sliceCopy)
```
**output**
```
intSlice: [2 3 4 5] sliceCopy: [0 0 0 0]
sliceCopy after copying: [2 3 4 5]
```