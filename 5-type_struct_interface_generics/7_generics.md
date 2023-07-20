### Generics(Go 1.18 or later )
* Many programming languages have the concept of generics — code that can elegantly accept one of a range of types without needing to be specialized for each one, as long as those types all implement certain behaviors. Let's see an example below.
```go
func sumInt(ints []int) {
	sum := 0
	for _, val := range ints {
		sum += val
	}
	fmt.Println(sum)
}

func sumFloat32(floats []float32) {
	sum := float32(0)
	for _, val := range floats {
		sum += val
	}
	fmt.Println(sum)
}

func main() {
	ints := []int{1, 2, 3}
	floats := []float32{1.0, 2.0, 3.0}
	sumInt(ints)
	sumFloat32(floats)
}
```
```
6
6.6000004
```
* In the above example we have to repeat the same logic of finding the sum of elements of a slice two times for `int` and `float32`. Imagine if we have to write the same logic for other types i.e `int32`, `int64`, `float64` etc then it would be a lot of repetition.
* Generics is the way we can write a single function which will operate on the different types.
```go
func sum[T int | float32](nums []T) {
	sum := T(0)
	for _, val := range nums {
		sum += val
	}
	fmt.Println(sum)
}

func main() {
	ints := []int{1, 2, 3}
	floats := []float32{1.1, 2.2, 3.3}
	sum(ints) // calling without type parameter
	sum[float32](floats) // calling with type parameter
}
```
```
6
6.6000004
```
* `sum()` function has one ordinary function parameter called `nums` and one type parameter called `T` inside the square bracket(`[T int | float32]`) . The type parameter `T` has a constraint which is union of `int` and `float32` i.e `T` could be either `int` or `float32`.
* While calling with actual values we can specify the type parameter or you can ignore it(both are acceptable but later is preferred). Go compiler can infer the type from the function parameter so ignoring type parameter in the calling code is fine. 
* If there isn't any function parameter then we have to specify type parameter.
#### Type Constraint
-------------------
* Type constraints can be declared in `interface`. The types which implements the interface are allowed by the type constraint.
```go
type number interface {
	int | int8 | int16 | int32 | int64 | float32 | float64
}

func sum[T number](nums []T) {
	sum := T(0)
	for _, val := range nums {
		sum += val
	}
	fmt.Println(sum)
}

func main() {
	ints := []int{1, 2, 3}
	floats := []float32{1.1, 2.2, 3.3}
	sum(ints)
	sum(floats)
}
```
```
6
6.6000004
```