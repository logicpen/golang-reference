### Regular Expressions
* A regular expression is a sequence of characters that specifies a search pattern in text. Usually such patterns are used by  
  string-searching algorithms for "find" or "find and replace" operations on strings, or for input validation.(Wikipedia)
* The usage of regular expressions are as follow. 
    * To check if the password set by user satisfies the prescribed password patterns.
    * To check if email id entered by user is a valid email id pattern.
    * Find a particular character or set of characters and replace them with another character or set of characters.
### Regular Expressions with Golang
* Golang provides an in built package `regexp` which provides many functions to deal with regular expressions.
```go
package main

import (
	"fmt"
	"log"
	"regexp"
)

func main() {
	input := "this is a test string"  // input string
	regex := `test`                   // patter to be search in input string
	reg, err := regexp.Compile(regex) // Compile parses a regular expression and returns, if successful, a Regexp object 
	                                  // that can be used to match against text.
	if err != nil {              
		log.Fatal(err)
	}
	isMatch := reg.MatchString(input) // MatchString reports whether the string s contains any match of the regular expression.
	fmt.Println(isMatch)              // prints true as expression `test` is present in input string.
}
```
* In the above example the regular expression pattern is written in raw string with backtick ( ``regex := `test` ``), this is preferred 
  because both ordinary string literals and regular expressions use backslashes for special characters.
* A raw string, delimited by backticks, is interpreted literally and backslashes have no special meaning.

### Character Grouping in Regular Expression 
* Grouping of characters can be done by `[]`(square brackets). Characters present inside of `[]` is considered one at a time.
* We can form other regular expressions like, 
    * `[a-z]` :- checks if any one of the lower case alphabets is present in the input text or not
    * `[A-Z]` :- checks if any one of the upp er case alphabets is present in the input text or not
    * `[0-9]` :- checks if any one of the digits is present in the input text or not
```go
input1 := "atom"
regex1 := `[abc]` // expression checks if any of a, b or c is present in the input text
reg1, err := regexp.Compile(regex1)
if err != nil {
    log.Fatal(err)
}
isMatch1 := reg1.MatchString(input1)
fmt.Println(isMatch1) // prints true as a is present in the string "atom"

input2 := "A123"
regex2 := `[a-zA-Z]` // expression checks if any one of the lower or upper case alphabets is present in the input text
reg2, err := regexp.Compile(regex2)
if err != nil {
    log.Fatal(err)
}
isMatch2 := reg2.MatchString(input2)
fmt.Println(isMatch2) // prints true as A is present in the string "A123"
```
* `^` is used as negation of the pattern that follows it,
    * `[^a-b]` :- checks if any character except lower case alphabets is present in the input text at least once or not
    * `[^A-B]` :- checks if any character except upper case alphabets is present in the input text at least once or not
    * `[^0-9]` :- checks if any character except the numeric characters is present in the input text at least once or not
```go
input1 := "abc"
regex1 := `[^abc]` // expression checks if any character except a or b or c is present in the input text at least once
reg2, err := regexp.Compile(regex1)
if err != nil {
    log.Fatal(err)
}
isMatch1 := reg2.MatchString(input1)
fmt.Println(isMatch1) // prints false as all the characters present are either a or b or c
                      // it will print true if input text is "abcd"
```
### Repetition or Quantifiers in Regular Expression
* `a?` :- checks if `a` presents in the input string 0 or 1 times(as many times as possible) but prefers 1 time
* `a*` :- checks if `a` presents in the input string 0 or more times but prefers more
* `a+` :- checks if `a` presents in the input string 1 or more times but prefers more
* `a{n}` :- checks if `a` presents in the input string exactly n times
* `a{n,}` :- checks if `a` presents in the input string at least n or more times
* `a{n, z}` :- checks if `a` presents in the input string at least n times but less than z times

### Miscellaneous 
* `.` :- Dot is used to match with any character
* `\` :- Backslash is used as escape character to match characters like `.`, `\`, `-`, `+` etc
