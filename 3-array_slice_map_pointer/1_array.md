### Array
--------
* Until now we could only store a single value in a variable but what if we want to store multiple values.This is when arrays come to play.Arrays are collection of elements of same type.Let's see some example of arrays
```go
package main

import "fmt"

func main() {
    var intArray1[2]int // declaring array of int type of length 2
    intArray1[0] = 11 // indexing starts from 0 i.e the first element is at zeroth position
    intArray1[1] = 12 
    fmt.Printf("The array is %v and first element is %d\n", intArray1, intArray1[0])

    strArray := [3]string{"a", "b", "c"} // declaration and initialization(short hand syntax)
    fmt.Printf("The array is %v and second element is %s\n", strArray, strArray[1])

    boolArray := [3]bool{true} 
    fmt.Println(boolArray) // the elements get initialized to their zero values if not initialized
    
    /* The length can be ignored while declaration by replacing it with three dots and the compiler would determine the length
    */
    floatArray := [...]float64{43.21, 64.45, 78.67}
    // len() returns length of an array and can be used with string
    fmt.Printf("Length of floatArray: %d and type: %T\n", len(floatArray), floatArray)
}
```
**output**
```
The array is [11 12] and first element is 11
The array is [a b c] and second element is b
[true false false]
Length of floatArray: 3 and type: [3]float64
```
Length is included while determining the type of array.We can not change of the size of the array once declared.

Arrays are value type means when we assign an array to another variable, the copy of the array gets assigned to the variable not the original array.So the changes made in the array referencing the second variable doesn't affect the original array.Pointers are used if changes are needed in original array
```go
originalArray := [3]int{1, 2, 3}
copiedArray := originalArray
copiedArray[0] = 11
fmt.Printf("The original array is %v and the copied array is %v\n", originalArray, copiedArray)
```
**output:**`The original array is [1 2 3] and the copied array is [11 2 3]`  
#### Iterating over the elements of an array
Sometimes it's necessary to cycle through all the elements of an array.We can do it in following ways
```go
sum := 0
intArray := [...]int{1, 2, 3, 4, 5}
// using for loop
for i := 0; i < len(intArray); i++ {
    sum += intArray[i] // same as sum = sum + intArray[i]
}
fmt.Printf("Sum is %d after for loop\n", sum)

sum = 0
// using range
for i, v := range intArray {
    sum += v
    fmt.Printf("%d is at index %d\n", v, i)
}
fmt.Printf("Sum is: %d\n", sum)
```
**output**
```
Sum is 15 after for loop
1 is at index 0
2 is at index 1
3 is at index 2
4 is at index 3
5 is at index 4
Sum is: 15
```
`range` returns both index and value. If you only need the values of the elements and want to discard the index then use blank identifier `_`. `for _, v := range a {...}`
#### Multidimensional Array
These are arrays that contain other arrays as their elements.We can call these array as *array of arrays* pretty much like an *array of integers*.
```go
var multArray [3][2]int
	multArray[0] = [2]int{1, 2}
	multArray[1] = [2]int{3, 4}
	// multArray[2] = [2]int{5, 6}
	multArray[2][0] = 5
	multArray[2][1] = 6
	fmt.Printf("multArray: %d\n", multArray)
	// iterating over multArray
	for i, innerArray := range multArray {
		fmt.Printf("%v at index %d\n", innerArray, i)
		sum := 0
		for _, elementOfInArray := range innerArray {
			sum += elementOfInArray
		}
		fmt.Printf("sum of the elements of array %v is %d\n", innerArray, sum)
	}
```
**output**
```
multArray: [[1 2] [3 4] [5 6]]
[1 2] at index 0
sum of the elements of array [1 2] is 3
[3 4] at index 1
sum of the elements of array [3 4] is 7
[5 6] at index 2
sum of the elements of array [5 6] is 11
```
We can also use short hand syntax to declare multidimensional array.
```go
multArray := [3][2]int{
    {1, 2},
    {3, 4},
    {5, 6}, // prevents the compiler from inserting semicolon
}
```
* `[]byte` is required to when we want to write something in to files. it represents slice of `uint8` which also represents code points of ascii character(0 - 255). We can convert strings to `[]byte`.
```go
func main() {
	num := []byte("hello")
	fmt.Println(num)
}
```
**output:**`[104 101 108 108 111]`