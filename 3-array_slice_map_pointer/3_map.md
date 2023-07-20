### Map
-----------
* Map is a built in data structure in go.We can store multiple elements inside a map in the form of key value pair.Value can be retrieved using the key associated with it.
* If we declare a map like `var legs map[string]int`.Here in legs map key would be of string type and value would be of int type.Now the map has value `nil`.
* If we want to add items to the map we have to use `make()` i.e `legs = make(map[string]int)` or we can directly declare a map using `make()` i.e `legs := make(map[string]int)`.
```go
package main

import (
	"fmt"
)

func main() {
	var legs map[string]int
    legs = make(map[string]int)
    // we can directly declare a map using short hand syntax i.e legs := make(map[string]int)
    /* 
    legs := map[string]int {
        "cow": 4,
        "crow": 2,
    }
    */
	legs["cow"] = 4
	legs["crow"] = 2
	fmt.Println(legs)
    fmt.Printf("The cow has %d legs\n", legs["cow"])
    fmt.Println(legs["cat"]) // returns zero value of the respective type if not present in map
}
```
**output**
```
map[cow:4 crow:2]
The cow has 4 legs
0
```
* `value, isPresent := legs[key]` syntax is used for determining if a key value pair exists in the map or not.`value` would be the value for the `key` or zero value if `key` is not present.`isPresent` would be `true` or `false` is pair exists or doesn't exits respectively.
```go
legs := map[string]int{
    "cow":  4,
    "crow": 2,
}
animal := "cat"
value, isPresent := legs[animal]
fmt.Println(value, isPresent)
if isPresent {
    fmt.Printf("%s is present in the map\n", animal)
} else {
    fmt.Printf("%s is not present in the map\n", animal)
}
```
**output**
```
value: 0 isPresent: false
cat is not present in the map
```
#### Iterating Over a Map
`range` form of `for` loop is used to iterate over a map.
```go
legs := map[string]int{
    "cow":  4,
    "crow": 2,
    "cat":  4,
}
for k, v := range legs {
    fmt.Printf("%s has %d legs\n", k, v)
}
```
**output:**
```
cat has 4 legs
cow has 4 legs
crow has 2 legs
```
* We can find the length and delete an element from a map also
```go
legs := map[string]int{
    "cow":  4,
    "crow": 2,
    "cat":  4,
}
fmt.Printf("length of legs map is %d\n", len(legs))
delete(legs, "cat")
fmt.Printf("after deleting length of legs map is %d\n", len(legs))
```
**output**
```
length of legs map is 3
after deleting length of legs map is 2
```
* maps are reference types similar to slices. While assigning a map to a different variable both points to the same data structure in the memory. So changing in either of the variable would be reflected in both the variable.
```go
map1 := map[string]int{
    "a": 1,
    "b": 2,
    "c": 3,
}
map2 := map1
fmt.Printf("Before changing -> map1: %v, map2: %v\n", map1, map2)
map2["b"] = 4
fmt.Printf("After changing -> map1: %v, map2: %v\n", map1, map2)
```
**output:**
```
Before changing -> map1: map[a:1 b:2 c:3], map2: map[a:1 b:2 c:3]
After changing -> map1: map[a:1 b:4 c:3], map2: map[a:1 b:4 c:3]
```
* maps can only be compared to `nil`.We can not compare between two maps even though both reference to the same internal data structure.
```go
map1 == nil // is correct(returns true or false)
map1 == map2 // gives error
```