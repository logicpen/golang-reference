### Strings
------
* Strings in golang are used to hold textual data and is represented by data structure that is consists of two parts. 
* One part is a pointer that points to the backing array that contains the actual string and another part contains the size of the string in bytes. 
* Each part has memory allocated depending upon the architecture of the computer.
```
 ______
|      |                                            
| ptr  |---->|h|e|l|l|o| (5 bytes sting array)                
|______|                                  
|      |                            
|len(5)|                                      
|______|
```

### String Formatting
String formatting can be done be `Printf()` provided by `fmt` package.Go provides many verbs for string formatting.Let's see some examples
```go
package main

import "fmt"

func main() {
    fmt.Printf("%d\n", 65) // prints decimal value(65)
    fmt.Printf("%b\n", 65) // prints binary value(1000001)
    fmt.Printf("%x\n", 65) // prints hexadecimal value(41)
    fmt.Printf("%c\n", 65) // prints character value(A)

    fmt.Printf("%f\n", 123.56) // 123.560000

    fmt.Printf("%e\n", 12345678.0) // 1.234568e+07
    fmt.Printf("%E\n", 12345678.0) // 1.234568E+07

    fmt.Printf("%s\n", "golang") // golang
    fmt.Printf("%x\n", "A") // prints hexadecimal value of "A"(41)

    /* The width and precision of printed values can be specified.By default printed values are right justified and space padded 
    */
    fmt.Printf("|%6d|%6d|\n", 12, 345) // prints (|    12|   345|)
    fmt.Printf("%6.2f\n", 1.2) // prints (|  1.20|)
    fmt.Printf("|%-6.2f|\n", 1.2) // prints value with right padded(|1.20  |)
    fmt.Printf("My name is %s and age is %d kgs\n", "John", 65) // My name is John and age is 65 kgs

    // printing type of varables
    fmt.Printf("%T %T %T %T\n", "John", 65, 65.48, true) // string int float64 bool
}
```
### Type Conversion(Type Casting)
Sometimes it's required to convert the type of a variable to another type.When doing arithmetic operations sometimes we need to convert int to float64 to get exact results.
```go
package main

import (
	"fmt"
)

func main() {
	var i int = 42
	var f float64 = float64(i)
	var u uint = uint(f)
	fmt.Println(i,f,u) // 42 42 42
    fmt.Printf("%T %T %T\n", i, f, u) // int float64 uint
    
	var a float64
	a = float64(i)/float64(5)
	fmt.Printf("value of a is %f", a) // value of a is 8.400000
}
```
**output:**
```
42 42 42
int float64 uint
value of a is 8.400000
```  
* To convert from `int to string` or `string to int`, `strconv` package is used.
 ```go
 package main

import (
	"fmt"
	"strconv"
	"reflect"
)

func main() {
    // string to int
	var s1 string = "1234"
	i1,_ := strconv.Atoi(s1)
	fmt.Println(i1)                 //1234
	fmt.Println(reflect.TypeOf(i1)) //int
	// int to string
	var i2 int = 1234
	s2 := strconv.Itoa(i2)
	fmt.Println(s2)  //1234
	fmt.Println(reflect.TypeOf(s2)) //string
}
```
**output:**
```
1234
int
1234
string
```  
* ParseFloat() and ParseInt() functions are used to convert strings to floats(float64) and integers(int64) respectively.
```go
package main

import (
	"fmt"
	"strconv"
)

func main() {
	f, _ := strconv.ParseFloat("1.234", 64) // string to float
	fmt.Printf("%f %T\n",f, f)
	i, _ := strconv.ParseInt("123", 0, 64) // string to int
	fmt.Printf("%d %T\n",i, i)
	d, _ := strconv.ParseInt("0x1c8", 0, 64)
	fmt.Printf("%d %T\n",d, d)
	u, _ := strconv.ParseUint("789", 0, 64) // string to uint
    fmt.Println(u)
    floatStr := strconv.FormatFloat(12.3456, 'f', 6, 64) // float to string
	fmt.Printf("%s %T\n",floatStr, floatStr)
}
```
**output**
```
1.234000 float64
123 int64
456 int64
789
12.345600 string
```  