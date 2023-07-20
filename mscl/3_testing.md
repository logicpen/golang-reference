### Testing In Golang
* Golang provides inbuilt testing package to write tests.
* Code for testing should be in a file with the naming convention `randomName_test.go`. After file is created
  we write test function with the naming convention as `TestRandomName.go`.
* To run test in a specific directory, go the that directory and run `go test`. We can add `-v` to verbose output and
  `-cover` to find how much code is covered with the test. (`go test -v -cover`)
* To run all tests in current directory and it's subdirectories we can use `go test ./...`.
### Basic Tasting
```
testing
      |
      -----add.go
      |
      -----add_test.go
```
```go
// ./testing/add.go
package testing

func Add(a, b int) int {
	return a + b
}
```
```go
// ./testing/add_test.go
package testing

import "testing"

func TestAdd(t *testing.T) {
	type Data struct {
		a      int
		b      int
		result int
	}
	testCases := []Data{{1, 2, 3}, {2, 3, 5}, {10, 20, 30}}
	for _, data := range testCases {
		res := Add(data.a, data.b) // from add.go file
		if res != data.result {
			t.Errorf("Expected %v but got %v", data.result, res)
		}
	}
}
```
* After running `go test -v -cover`, we get the following output
```
=== RUN   TestAdd
--- PASS: TestAdd (0.00s)
PASS
coverage: 100.0% of statements
ok      go-tutorial/testing     0.505s
```
* Both files are in the same package
### Mocking
* While working with microservice sometimes we need to make request to external api to get the data form other systems.
  While testing these functions we can give actual credentials and make requests to the actual api which is not suitable 
  because lets say the external api might have request limit or might have price per request.
* To avoid above situations we can mock the external api for testing.
* Let's say we have function which calls an end point with pass integer in request body and the api in response sends square of 
  the integer. Bellow we are mocking the request and response without making the request to the external api.
```go
// ./mocking/mocking.go
package mocking

import (
	"bytes"
	"encoding/json"
	"fmt"
	"io/ioutil"
	"net/http"
)

type Input struct {
	Data int64 `json:"data"`
}
type Result struct {
	Square int64 `json:"result"`
}

func GetSquare(baseURL string, inputData int64) (int64, error) {
	URL := fmt.Sprintf("%s/square", baseURL)
	jsonStr, _ := json.Marshal(Input{Data: inputData})

	req, _ := http.NewRequest(http.MethodPost, URL, bytes.NewBuffer(jsonStr))
	req.Header.Add("Accept", "application/json")

	client := http.Client{}
	res, err := client.Do(req)
	if res.StatusCode != 200 {
		return 0, err
	}

	data, err := ioutil.ReadAll(res.Body)
	if res.StatusCode != 200 {
		return 0, err
	}

	result := &Result{}
	err = json.Unmarshal(data, result)
	if res.StatusCode != 200 {
		return 0, err
	}
	return result.Square, nil
}
```
```go
package mocking

import (
	"encoding/json"
	"net/http"
	"net/http/httptest"
	"testing"
)

func TestGetSquare(t *testing.T) {
	server := httptest.NewServer(http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		if r.URL.Path != "/square" {
			t.Errorf("Expected path 'square' but got '%v'", r.URL.Path)
		}
		if r.Header.Get("Accept") != "application/json" {
			t.Errorf("Expected application/json but got %v", r.Header.Get("Accept"))
		}

		data := &Input{}
		_ = json.NewDecoder(r.Body).Decode(data)
		w.WriteHeader(http.StatusOK)
		json.NewEncoder(w).Encode(Result{Square: data.Data * 2})
	}))
	defer server.Close()

	value, _ := GetSquare(server.URL, 2)
	if value != 4 {
		t.Errorf("Expected %v but got %v", 4, value)
	}
}
```
* Output after running the command `go test -v -cover`
```questuser@INLT2167-Mac mock % go test -v -cover
=== RUN   TestGetSquare
--- PASS: TestGetSquare (0.00s)
PASS
coverage: 81.2% of statements
ok      go-tutorial/testing/mock        0.820s
```
