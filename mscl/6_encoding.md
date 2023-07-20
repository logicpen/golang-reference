### Encoding & Decoding
------------------------
* Encoding is the process of putting sequence of characters in to a specialize format for efficient transmission or storage. In the other hand decoding is the process of converting encoded data back to the original sequence of character.
* For example data in transmitted in JSON format in REST APIs. To send data in response to an request, we have to convert the data(encode) to JSON before sending or while receiving data from request, we decode the JSON data to suitable format and then we process the data.
* Go as well as other programming languages provides methods defined in their std lib to handling encoding and decoding process.
* `base64`, `binary`, `hex`, `unicode`, `ASCII` are some of the examples of encoding formats.
#### base64 Encoding
---------------------
* `base64` encoding is to encode non-HTTP-compatible characters that may be in the user name or password into those that are HTTP-compatible hence it can be transported using HTTP.
```go
func main() {
	str := "hello"
	fmt.Println("Original string:", str)
	encodedStr := base64.StdEncoding.EncodeToString([]byte(str)) // package name :- "encoding/base64"
	fmt.Println("After encoding:", encodedStr)

	bytes, _ := base64.StdEncoding.DecodeString(encodedStr)
	fmt.Printf("Original string after decoding: %s\n", bytes)
}
```
**output*:**
```
Original string: hello
After encoding: aGVsbG8=
Original string after decoding: hello
```
#### hexadecimal Encoding
-------------------------
```go
func main() {
	str := "hello"
	fmt.Println("Original string:", str)
	encodedStr := hex.EncodeToString([]byte(str)) // package name :- "encoding/hex"
	fmt.Println("After encoding:", encodedStr)

	bytes, _ := hex.DecodeString(encodedStr)
	fmt.Printf("Original string after decoding: %s\n", bytes)
}
```
**output:**
```
Original string: hello
After encoding: 68656c6c6f
Original string after decoding: hello
```