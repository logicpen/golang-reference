### Reading Input From Console
* Go provides different functions in it's std lib to read from standard input (console,terminal).
* `fmt.Scan()` scans text read from standard input, storing successive space-separated values into successive arguments. Newlines count as space. It returns the number of items successfully scanned.
* `fmt.Scanln()` is similar to Scan, but stops scanning at a newline and after the final item there must be a newline or EOF.
```go
func main() {
	fmt.Println("Enter 2 numbers:")
	var num1 int
	var num2 int
	fmt.Scan(&num1, &num2)
	fmt.Println("Num1: ", num1, " Num2: ", num2)

	fmt.Println("Enter first and last name:")
	var firstName string
	var lastName string
	fmt.Scan(&firstName, &lastName)
	fmt.Println(firstName, lastName)
}
```
**output:**
```
Enter 2 numbers:
1 2 // enter space separated input value in std input(terminal, console) (read by Scan)
Num1:  1  Num2:  2
Enter first and last name:
john doe  // enter space separated input value in std input(terminal, console) (read by Scanln)
john doe
```
* You can use `for` loop to read infinitely from the console or for the specific number of times. Below program calculates the sum of variable number of integers read from std input(terminal, console)
```go
func main() {
	fmt.Println("Enter how many numbers:")
	var total int
	var num int
	var sum = 0
	fmt.Scan(&total)
	fmt.Printf("Enter %v integers each in a new line\n", total)
	for i := 1; i <= total; i++ {
		fmt.Scan(&num)
		sum += num
	}
	fmt.Println("Sum:", sum)
}
```
* If you want to read the whole line with spaces, you can use `bufio` and `os` packages. In case if you want an integer or float as input you have to explicitly convert it using `ParseInt()` or `ParseFloat()` functions.
```go
package main

import (
	"bufio"
	"fmt"
    "os"
    "strconv"
)

func main() {
	reader := bufio.NewReader(os.Stdin)
	fmt.Print("Enter name: ")
	text, _ := reader.ReadString('\n') // stops reading when new line is encountered
	text = text[:len(text)-1] // removing the new line character from the end
    fmt.Println(text)
    
    fmt.Print("Enter num: ")
	text, _ = reader.ReadString('\n') // stops reading when new line is encountered
	text = text[:len(text)-1]          // removing the new line character from the end
	float, _ := strconv.ParseFloat(text, 64)
	fmt.Println(float + 1.11)
}
```
**output:**
```
Enter name: John Smith
John Smith
Enter num: 2.22
3.33
```