---
layout: post
title:  Go variable basics
date:   2015-09-18 11:41:00
---

To declare a variable in go, you start with the keyword `var` (or `const` if you want the variable to be a constant), follow it with the name of the variable and finally specify the data type. If you have a bunch of variables you know will have the same type, you can separate variable names with a comma, and specify the data type last.

You can also initialise the variables the same time as declaration. If you do initialise, you can also omit the data type, and it will be derived based on the value. You can also explicitly set the data type by passing in the value to the type initialiser `T(v)` where `T` is one of the types specified below, and `v` is the value you are casting.

```go
var a,b,c int = 3,4,5
//or
var a,b,c = 3,4,5
```

If you do not choose to initialise the variable, they will be given their respective `zero` value[[1](https://tour.golang.org/basics/12)]. That is: 0 for numeric types; false for boolean; and empty string for strings.

The other option is to exclude the `var` keyword, and use the `:=` assignment operator (note: this can only be used inside a function body). This will implicitly derive the data type based on the value entered. The `:=` operator can only be used for variables that are being declared for the first time. In consecutive assignments, you must use the `=` operator to assign a new value.

Types can be classed as:

### Boolean

Booleans are `true` and `false` values, and are denoted by the `bool` data type.

```go
var isReady bool
isReady = true
fmt.Printf("isReady %v\n", isReady)

isFinished := false
fmt.Printf("isFinished %v\n", isFinished)

isFinished := true//compile error
```

### String

Strings represent textual data and are denoted by the `string` data type.

```go
var name string = "Trent"
fmt.Printf("My name is %s\n", name)
```

### Numeric

There are a number of different numeric types to target various levels of precision[[2](https://golang.org/ref/spec#Numeric_types)].

* uint8
* uint16
* uint32
* uint64
* int8
* int16
* int32
* int64
* float32
* float64
* complex64
* complex128

The preferred approach when dealing with integers is to use the `int` or `uint` data type, which will use the relevant type depending on your systems architecture. e.g. on a 32 bit system, `int` will be an alias for `int32`[[3](https://tour.golang.org/basics/11)].

1: [https://tour.golang.org/basics/12](https://tour.golang.org/basics/12)  
2: [https://golang.org/ref/spec#Numeric_types](https://golang.org/ref/spec#Numeric_types)  
3: [https://tour.golang.org/basics/11](https://tour.golang.org/basics/11)
