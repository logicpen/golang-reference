### Time Package
* Time package provides may apis to manipulate and work with datetime.
* Following example shows how to get the current date time
```go
package main

import (
	"fmt"
	"time"
)

func main() {
	now := time.Now() // returns a struct of type Time defined in the time package
	fmt.Println("Current time: ", now)
	fmt.Println("Current year: ", now.Year())   // get year
	fmt.Println("Current month: ", now.Month()) // get month
	fmt.Println("Current day: ", now.Day())     // get day
    fmt.Println("UTC time: ", now.UTC())        // get UTC time
}
```
**output:**
```
Current time:  2022-03-28 15:38:48.774965 +0530 IST m=+0.000059059
Current time:  2022
Current time:  March
Current time:  28
UTC time:  2022-03-28 10:08:48.774965 +0000 UTC
```
* We can also create datetime object from custom data in go by using `time.Date()`. It returns the same object(`time.Time`)
  as `time.Now()`.
```go
datetime := time.Date(2006, time.January, 1, 1, 1, 1, 1, time.Now().Local().Location()) // returns same struct as time.Now()
fmt.Println("Custom datetime: ", datetime)
fmt.Println("Current year: ", datetime.Year())   // get year
fmt.Println("Current month: ", datetime.Month()) // get month
fmt.Println("Current day: ", datetime.Day())     // get day
```
**output:**
```
Current year:  2006
Current month:  January
Current day:  1
```
* We can format the datetime data in different ways as follows.
```go
now := time.Now()
fmt.Println("Current time: ", now.Format("15:04:05")) // hh:mm:sec
fmt.Println("Current time: ", now.Format("January 02, 2006"))
```
**output:**
```
Current time:  15:57:27
Current time:  March 28, 2022
```
* There are scenarios where we might need to convert datetime string to datetime objects. This can be done using
  various parsing functions.
```go
timeStr := "2022-01-31"
tm, err := time.Parse("2006-1-2", timeStr)
if err != nil {
    log.Fatal(err)
}
fmt.Println("Datetime: ", tm)
fmt.Println("Year: ", tm.Year(), "Month: ", tm.Month())

tm, err = time.ParseInLocation("2006-1-2", timeStr, time.Local)
if err != nil {
    log.Fatal(err)
}
fmt.Println("Datetime: ", tm)
fmt.Println("Year: ", tm.Year(), "Month: ", tm.Month())
```
**output:**
```
Datetime:  2022-01-31 00:00:00 +0000 UTC
Year:  2022 Month:  January
Datetime:  2022-01-31 00:00:00 +0530 IST
Year:  2022 Month:  January
``` 
* We can do datetime arithmetic like adding to a datetime value.
```go
now := time.Now()
fmt.Println("Datetime now: ", now)

t1 := now.Add(time.Hour * 2)
fmt.Println("Datetime after 2 hours: ", t1)

t2 := now.AddDate(1, 0, 0)
fmt.Println("Datetime after 1 year, 0 months, 0 days", t2)
```
**output:**
```
Datetime now:  2022-03-29 10:42:52.590066 +0530 IST m=+0.000082025
Datetime after 2 hours:  2022-03-29 12:42:52.590066 +0530 IST m=+7200.000082025
Datetime after 1 year, 0 months, 0 days 2023-03-29 10:42:52.590066 +0530 IST
```
* We can calculate duration between two points of time.
```go
t1 := time.Date(2021, time.January, 1, 0, 0, 0, 0, time.Local)
t2 := time.Date(2022, time.January, 1, 0, 0, 0, 0, time.Local)
elapsed := t2.Sub(t1)
fmt.Println("Time elapsed between t1 and t2: ", elapsed)
```
**output:**
```
Time elapsed between t1 and t2:  8760h0m0s`
```
* We can find out the time in a specific location zone.
```go
zoneUTC := "UTC"
loc, err := time.LoadLocation(zoneUTC)
if err != nil {
    log.Fatal(err)
}
now := time.Now()
t := now.In(loc)
fmt.Println(now)
fmt.Println(t)
```
**output:**
```
2022-03-29 11:15:15.192113 +0530 IST m=+0.000074994
2022-03-29 05:45:15.192113 +0000 UTC
```
* We can also work with UNIX time. UNIX time is the number of seconds elapsed since UNIX epoch(00:00:00UTC on 1st Jan, 1970).
```go
now := time.Now()
fmt.Println(now.Unix())
```
**output:**
```
1648533345
```
* We can compare datetime like is a instance of time is equal to another instance of time.
```go
t1 := time.Date(2021, time.January, 0, 0, 0, 0, 0, time.Local)
t2 := time.Date(2021, time.January, 0, 0, 0, 0, 0, time.Local)
t3 := time.Date(2022, time.January, 0, 0, 0, 0, 0, time.Local)

if t1.Equal(t2) {
    fmt.Println("t1 is equal to t2")
}

if t1.After(t3) {
    fmt.Println("t1 is after t3")
} else {
    fmt.Println("t3 is after t1")
}
```
**output:**
```
t1 is equal to t2
t3 is after t1
```