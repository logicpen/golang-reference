### Data Stream & Buffer
* Data streams and buffer are useful while handling large data set. Let's say we have large file that needs to be
  read, in that case instead of reading the whole file and load it to the memory we can read in smaller chunks. 
  We can also do the same while writing to any file or data store.
* Buffer which has some predefined size is an area in computer memory and used to stored data chunks. So in short a chunk
  data stream is temporarily stored in buffer to be processed and once processing is done new data from data stream is placed
  inside of buffer to be processed again.
### io.Reader & io.Writer
* `io.Reader` and `io.Writer` are two interfaces which are useful while working with buffers. The signature of of both the
  interfaces are as follows,
```go
  type Reader interface {
	Read(p []byte) (n int, err error) // Read reads up to len(p) bytes from underlying data stream into p.
}

type Writer interface {
	Write(p []byte) (n int, err error) // Write writes len(p) bytes from p to the underlying data stream.
}
```
* Many types in different packages implement `Read()` and `Write()` methods to comply with `io.Reader` and `io.Writer` interface.
### Reading From Data Source
* Many types and packages implements `Read()` method defined in `io.Reader` interface to read from the data source.
#### strings.NewReader()
* This function returns a pointer to `strings.Reader` struct that implements `Read()` method defined by `io.Reader` interface.
```go
package main

import (
	  "fmt"
	  "io"
	  "strings"
)

func main() {
	  strReader := strings.NewReader("Hello World") // create a reader from string
	  p := make([]byte, 2) // data packet 

	  for { // read until all the bytes from source to data packet until error occurs.
		    n, err := strReader.Read(p) // 
		    fmt.Printf("%d bytes are read, data: %s\n", n, p[:n])

		    if err == io.EOF {
			      fmt.Println("End of the data source")
			      break
		    } else if err != nil {
			      fmt.Println("Error occurred ", err)
			      break
		    }
	  }
}
```
**output:**
```
2 bytes are read, data: He
2 bytes are read, data: ll
2 bytes are read, data: o 
2 bytes are read, data: Wo
2 bytes are read, data: rl
1 bytes are read, data: d
0 bytes are read, data: 
End of the data source
```
#### ioutil.ReadAll()
* This function reads all from a type which implements `io.Reader` interface. This function reads all the data
  from source until reaches `EOF` or any other type of error.
```go
package main

import (
	"fmt"
	"io"
	"io/ioutil"
	"strings"
)

func main() {
	strReader := strings.NewReader("Hello World")
	data, err := ioutil.ReadAll(strReader)
  if err != nil {
		fmt.Println("Error occurred ", err)
	}
	fmt.Printf("%d bytes are read, data: %s\n", len(data), data)
}
```
**output:**`11 bytes are read, data: Hello World` 
#### io.ReadFull()
* It will try to read bytes equal to the length of buffer and will return number of bytes and error. If 0 bytes are present in the source then it will return `io.EOF`. If number of bytes present in the source is greater than 0 but less than the length of the buffer then it would return `io.ErrUnexpectedEOF`.
```go
strReader := strings.NewReader("Hello World")
p := make([]byte, 7)

bytesRead, err := io.ReadFull(strReader, p) // error is nil
fmt.Printf("%d bytes are read, data: %s\n", bytesRead, p[:bytesRead]) // prints:- 7 bytes are read, data: Hello W

bytesRead, err = io.ReadFull(strReader, p) // error is io.ErrUnexpectedEOF
fmt.Printf("%d bytes are read, data: %s\n", bytesRead, p[:bytesRead])  // prints:- 4 bytes are read, data: orld

bytesRead, err = io.ReadFull(strReader, p) // error is io.EOF
fmt.Printf("%d bytes are read, data: %s\n", bytesRead, p[:bytesRead])  // prints:- 0 bytes are read, data:
```
#### io.LimitReader()
* This function takes a reader and an integer(n) and returns a reader with the capacity of passed integer(n).
```go
strReader := strings.NewReader("Hello World again") // 17 bytes of data
newReader := io.LimitReader(strReader, 11) // new reader with 11 bytes of data

p := make([]byte, 6)
for {
	n, err := newReader.Read(p)
	fmt.Printf("%d bytes are read, data: %s\n", n, p[:n])

	if err == io.EOF {
		fmt.Println("End of the data source")
		break
	} else if err != nil {
		fmt.Println("Error occured ", err)
		break
	}
}
```
**output:**
```
6 bytes are read, data: Hello 
5 bytes are read, data: World
0 bytes are read, data: 
End of the data source
```
### Writing To Data Store
* We can store data in computer hardware in form of files or in memory(ram). The block of space(data store) where data is stored is handled by go and it has some methods which helps to write data to the block of space(data store).
* Data stores can implement `Write()` method defined in `io.Writer` interface to write to the data store.
* The `Write()` method accepts a data packets which is a slice of `byte` and write it to the data store. It returns number of bytes written and an error.
```go
func main() {
	s := Store{}
	n, err := s.Write([]byte("Hello"))
	fmt.Printf("Bytes written: %d, Error: %v, Data: %s\n", n, err, s.data)
	n, err = s.Write([]byte("World"))
	fmt.Printf("Bytes written: %d, Error: %v, Data: %s\n", n, err, s.data)
	n, err = s.Write([]byte("Again"))
	fmt.Printf("Bytes written: %d, Error: %v, Data: %s\n", n, err, s.data)
}

type Store struct {
	data []byte // used to store 10 bytes of data
}

func (s *Store) Write(p []byte) (n int, err error) {
	// checks if more than 10 bytes of data is written
	if len(s.data) == 10 {
		return 0, io.EOF
	}

	// check length of the incoming data
	dataLen := len(p)
	// remaining space out of 10 bytes
	remainingSpace := 10 - len(s.data)
	// if remaining space is less than incoming data
	if remainingSpace <= dataLen {
		dataLen = remainingSpace //
		err = io.EOF
	}

	s.data = append(s.data, p[:dataLen]...)
	n = dataLen
	return
}
```
**output:**
```
Bytes written: 5, Error: <nil>, Data: Hello
Bytes written: 5, Error: EOF, Data: HelloWorld
Bytes written: 0, Error: EOF, Data: HelloWorld
```
#### io.WriteString()
* It take a `Writer`, a string and writes a byte from a string to the Writer by calling `Write()` method internally on writer.
* If the source implements `io.StringWriter` interface then `WriteString()` function of the interface will be call instead of `Write()` function.
```go
func main() {
	s := &Store{}
	n, err := io.WriteString(s, "Hello")
	fmt.Printf("Bytes written: %d, Error: %v, Data: %s\n", n, err, s.data)
	n, err = io.WriteString(s, "World")
	fmt.Printf("Bytes written: %d, Error: %v, Data: %s\n", n, err, s.data)
	n, err = io.WriteString(s, "Again")
	fmt.Printf("Bytes written: %d, Error: %v, Data: %s\n", n, err, s.data)
}

type Store struct {
	data []byte // used to store 10 bytes of data
}

func (s *Store) Write(p []byte) (n int, err error) {
	// checks if more than 10 bytes of data is written
	if len(s.data) == 10 {
		return 0, io.EOF
	}

	// check length of the incoming data
	dataLen := len(p)
	// remaining space out of 10 bytes
	remainingSpace := 10 - len(s.data)
	// if remaining space is less than incoming data
	if remainingSpace <= dataLen {
		dataLen = remainingSpace //
		err = io.EOF
	}

	s.data = append(s.data, p[:dataLen]...)
	n = dataLen
	return
}
```
**output:**
```
Bytes written: 5, Error: <nil>, Data: Hello
Bytes written: 5, Error: EOF, Data: HelloWorld
Bytes written: 0, Error: EOF, Data: HelloWorld
```
### Standard I/O Streams
* `os.Stdin`, `os.Stdout`, `os.Stderr` etc implement `io.Writer` and `io.StringWriter` interfaces, hence we can call `Write()` or 
`WriteString()` functions on them to write data.
* The `fmt` package also provide some functions to write data to the objects which implement `io.Writer` interface.
```go
io.WriteString(os.Stdout, "Hello World!\n")
os.Stdout.Write([]byte("Hello World!\n"))

fmt.Fprintln(os.Stdout, "Hello World!")
fmt.Fprintf(os.Stdout, "%s", "Hello World!\n")
```
* Once I/O operations are done, the `io.Reader` or `io.Writer` objects are no longer needed. Hence those objects may implement `Close()`
method defined in `io.Closer` interface.
* Also few interfaces are combinations of `io.Reader`, `io.Writer` and `io.Closer`.
```go
type Closer interface{
	Close() error
}

type ReadCloser interface{
	Reader
	Closer
}

type WriteCloser interface{
	Writer
	Closer
}

type ReadWriteCloser interface{
	Reader
	Writer
	Closer
}
```
### Transferring Data Between io.Reader and io.Writer objects
* We can manually read from source and write to the destination but there are inbuilt functions which make it easier.
* If we want to write data to an `io.Writer` object coming from an `io.Reader` object, then we can use the `io.Copy` function.
	`func Copy(dst Writer, src Reader) (int64, error)`
* The Copy function reads data from the src and writes to the dst until `io.EOF` error is returned by the src (or other uneventful error). It returns the number of bytes copied from the src to the dst.
* The `io.EOF` error returned by src is silently dropped. However, if Read or Write operation returns an error, Copy will return that error along with the number of bytes copied to dst until that error has occurred.
* Copy uses a temporary buffer to stage the data read from the src. If you want a custom buffer size, use `io.CopyBuffer` function.
If src implements the `io.WriterTo` interface, then Copy function calls src.WriteTo(dst) internally. Otherwise, if dst implements the 
`io.ReaderFrom` interface, the Copy calls dst.ReadFrom(src).
```go
// create a io.Reader from a string
strReader := strings.NewReader("Hello World!\n")
// copy date from io.Reader to io.Writer(os.Stdout)
io.Copy(os.Stdout, strReader)
// copy n bytes(copies only 5 bytes)
io.CopyN(os.Stdout, strReader, 5)
```
* `io.Pipe()` function is another way to transfer data between `io.Reader` and `io.Writer` objects synchronously. When some data is written to the writer, instantly the data is made available to be for the reader.
```func Pipe() (*PipeReader, *PipeWriter)```
* `*PipeReader` and `*PipeWriter` which are returned by `Pipe()` function are objects of structs. These structs implement `ReadCloser` and `WriteCloser` interfaces respectively.
* Reader and Writer returned by pipe function can be used with goroutines. Let's say writer writes to pipe and reader reads from pipe in separate goroutines. So when reader reads from the pipe and reads all the data, it will blocks current routine and go compiler might schedule another routine which might write data to the pipe. In the same manner each write also blocks gorutine.
* If the writer close the pipe then `Read()` call returns `io.EOF` but if reader closes the pipe then `Write()` call returns `io.ErrClosedPipe`.
```go
reader, writer := io.Pipe()

go func() { // start go routine and write to the pipe
	writer.Write([]byte("Hello"))
	writer.Write([]byte("World"))
	writer.Close()
}()

packet := make([]byte, 5)
n, err := reader.Read(packet)
fmt.Printf("Bytes Read: %d, Data: %s, Error: %v\n", n, packet, err)

n, err = reader.Read(packet)
fmt.Printf("Bytes Read: %d, Data: %s, Error: %v\n", n, packet, err)

n, err = reader.Read(packet)
fmt.Printf("Bytes Read: %d, Data: %s, Error: %v\n", n, packet, err)
```
**output:**
```
Bytes Read: 5, Data: Hello, Error: <nil>
Bytes Read: 5, Data: World, Error: <nil>
Bytes Read: 0, Data: World, Error: EOF
```
#### Buffered Streams
* A buffer is a region of space in the memory. It can be a fixed or a variable size buffer to read data from or write data to. The `bytes` built-in package provides `Buffer` struct to construct a variable size buffer.
* `bytes` package provide functions to construct `Buffer` struct as bellow.
```
func NewBuffer(buf []byte) *bytes.Buffer
func NewBufferString(s string) *bytes.Buffer
```
* The `Buffer` struct provides many methods to perform read, write and other operations. 
* `Buffer` maintains the last read position and `Read()` call only reads bytes from the last read position.
```go
buf := bytes.NewBufferString("Hello") // create new buffer

fmt.Println(buf.WriteString("World")) // write to buffer

strReader := strings.NewReader(" Again\n")
fmt.Println(buf.ReadFrom(strReader)) // write to buffer from a reader

fmt.Println(buf.Read(make([]byte, 25))) // read first 5 bytes from buffer
fmt.Println(buf.WriteTo(os.Stdout))     // write remaining data to stdout
fmt.Println(buf.Read(make([]byte, 25))) // gives EOF
```
**output:**
```
5 <nil>
7 <nil>
17 <nil>
0 EOF
0 <nil>
```
* Go provides mechanism to store data in buffer before sending it to destination. `bufio` package provides a struct type called `Writer`
which internally holds a `io.Writer` object. It buffers the data during write calls and writes data to the internal `io.Writer`.
```go
dst := bufio.NewWriter(os.Stdout) // creates *bufio.Writer

fmt.Println(dst.WriteString("Hello World ")) // writes string to buffer
fmt.Println(dst.Write([]byte("Again!\n")))   // writes string to buffer

fmt.Println(dst.Flush()) // writes data to os.Stdout
```
**output:**
```
12 <nil>
7 <nil>
Hello World Again!
<nil>
```