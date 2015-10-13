---
layout: post
title:  Go list structures
date:   2015-10-13 16:27:00 +1200
---

The most basic form of list in Go is an array. As you would expect, it is declared with square brackets, and has a fixed size. So, if you want an array of a given data type, immediately before the datatype, you would add the square brackets with the expected size. You can have go automatically determine the array size by specifying `...` inside the square brackets.

```go
var a [5]string
a2 := [5]string{"one","two","three","four","five"}
a3 := [...]string{"one","two","three"}
```

You can always determine the length of an array at runtime with the `len` function.

```go
s3 := [...]string{"one","two","three"}
fmt.Printf("Length of s3 is %d\n", len(s3))
```

Building on from an array, is a slice - which are much more common due to their increased flexibility. Unlike an array where in the declaration you should be specifying the size, with a slice you leave this component out. When you don't create the slice with a slice literal, you need to allocate some initial length (and optionally, capacity) with the `make` function. If you ommitt the capacity parameter, it automatically equals the length.

```go
var s []int
s = make([]int, 5, 5)

s2 := []int{1,5,3,5}
```

We can use the `len` (length) function to get the number of elements and `cap` (capacity) returns the number of elements in the underlying array. Following on from using `s` in the example above, we can slice the slice using the `[low:hi]` on the slice to extract elements into a new slice. Whilst we will not be able to recover elements before `low`, we can recover the space beyond hi/len, by slicing again upto the capacity of the structure.

```go
s := make([]int, 5, 10)
fmt.Println("s len: ", len(s))
fmt.Println("s cap: ", cap(s))

s = s[:len(s)+2]
fmt.Println("s len: ", len(s))
fmt.Println("s cap: ", cap(s))

//s = s[:cap(s)+1]
//panic: runtime error: slice bounds out of range
```

It's important to note, that when slicing an array/slice into a new variable, they will still be pointing to the same memory address. So updating the value in one will be reflected in the other.

```go
a1 := [4]int{4,2,6,8}

a2 := a1[:]
a2[0] = 5

fmt.Println("a1", a1)
fmt.Println("a2", a2)
```

Once a slice reaches its capacity, a new slice needs to be created to cater for the bigger size. There are a couple of built in functions to aid in growing a slice. The first is `copy` which just copies each element into a new slice that you have created.

```go
s := make([]int, 2,2)

s[0] = 22
s[1] = 55

fmt.Println("S len", len(s))
fmt.Println("S cap", cap(s))

s2 := make([]int, 4,4)
//copy(dest, src)
copy(s2, s)

fmt.Println("s2 len", len(s2))
fmt.Println("s2 cap", cap(s2))

fmt.Println("s", s)
fmt.Println("s2", s2)
```

The other function is `append` which returns a new slice with the data appended, and growing the slice if necessary.

```go
s := make([]int, 2,2)

s[0] = 22
s[1] = 55

fmt.Println("S len", len(s))
fmt.Println("S cap", cap(s))

s = append(s, 12, 44)

fmt.Println("S len", len(s))
fmt.Println("S cap", cap(s))

sAppend := []int{91, 38}
s = append(s, sAppend...)

fmt.Println("S len", len(s))
fmt.Println("S cap", cap(s))
```

The other structure Go offers is a `map` - sometimes known as an associative array in other languages. These are a key/value structure, commonly with a string as the key to offer a more meaningful lookup than a numeric index. They can be created with a map literal, which will automatically initialise them; otherwise, like slices, they need to be initialised with the make function.

```go
m1 := map[string]int{
    "Sydney": 10,
    "Wellington": 12,
}
//last value in map must end with a ,

var m2 map[string]int

m2 = make(map[string]int)
m2["Sydney"] = 10
m2["Wellington"] = 12

fmt.Println(m1)
fmt.Println(m2)
```

When the map value is a user-defined type, in the map literal, you can leave out the type name in the literal.

```go
type Coords struct {
    Long, Lat float64
}

m1 := map[string]Coords{
    "Sydney": {10, 10},//Coords left off from value literal
    "Wellington": Coords{20, 20},
}
```

As you'd expect, elements can be retrieved by referencing the key against the map - `m1["Sydney"]`. If the specified key does not exist, you will simply have an empty value. So here you can assign two values - first, the element value, and second, a boolean value as to if the element existed or not.

```go
var el Coords
var ok bool

el,ok = m1["Sydeney"]
fmt.Println("Element exists?", ok)
fmt.Println("Element val", el)
```

The other operation you will likely want to perform is removing values. This is done with the `delete` operation.

```go
delete(m1, "Sydney")
```
