## Unsafe
* Before discussion about `unsafe` package lets understand about pointers in golang.
    * Mathematical operations such as addition, subtraction etc can't be performed on pointers(allowed in C).
    * One type of pointer can't be converted to another type of pointer i.e can't convert an `*int64` to `*float64` pointer.
    * Different types of pointers can't be compared with `==` or `!=` operators.
* All the properties listed below are characteristics of an unsafe pointer.
* `unsafe` package provides type `unsafe.Pointer`, which bypass type safety of go and provides more flexibility(conversion between different types of pointers etc).