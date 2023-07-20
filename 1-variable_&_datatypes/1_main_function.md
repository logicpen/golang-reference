### The Main Function
---
Below is a simple go program saved in a file named `main.go` but the file can be named anything.
```go
package main

import "fmt"

func main() {
    // single line comment

    /*
    This is
    multiline comment
    */
    
    fmt.Println("hello world")
}
```
**output** `hello world`  

* The first line is `package declaration`.Every go program must belongs to a particular package. Packages are like programs bundled together to perform specific types of tasks i.e math package for performing mathematical operations.
* Packages are built for code reusability.We can use any package by importing it by `import` keyword. We have imported `fmt` package to print `hello world` to console or terminal. 
* All the blank lines and whitespace are ignored by go compiler. While importing a package, the package name should be surrounded by double quotes.
* `func` keyword is used to declare functions in golang, in this case `main` function is declared. Functions are used to group statements to perform certain tasks and these statements are executed only when the function is called in the program.
* Function names should be followed by parenthesis `()`. Like `main()`, `Println()` is also a function defined in `fmt` package. To use a function defined in a package, we have to first import the package and access the function with `.` operator. The function `main` is a special function because it gets called by go compiler when we try to run the program. We don't need to explicitly call `main` function.
* Comments are used give useful information about code or to document the code etc. Like whitespace, comments are also ignored by go compiler. Comments can be added by `//`(for single line comment) or `/**/`(multiline comment). It is advised to add comment to your code so that it can be easily perceived what the particular piece of code does.