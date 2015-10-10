---
layout: post
title:  Go functions and methods
date:   2015-10-10 18:40:00 +1200
---

It's important to get the terminology right when talking about functions in go. The common terminology is method and/or function, but in Go these are distinguishable by how they behave. In go, a method interfaces off a variable of that type, where a function is free-standing so to speak. So in one form, you pass the variable in one of the paremeters and in the other you call the function off the variable (with the dot notation).

A function starts with the `func` keyword, followed by the function name; any expected parameters; and finally the return types.

```go
func prefix(a,b string) string {
    return a + " " + b
}
```

As you can see, we are accepting two input parameters, both of type string - which follows the rule we learnt about with variables. If two or more variables are of the same type, you can continue with one data type specification.

The return type here is `string` - the two parameters concatenated together. Go also supports returning more than one value. To do so, we can declare a list enclosed in parenthesis.


``` go
func prefix2(a,b string) (string,string) {
    retA := a + " " + b
    retB := b + " " + a

    return retA, retB
}
```

Similarly, when we call this function, we just assign value by separating the number of return types with an equal number of variables, e.g `retA,retB := prefix2("abc", "def")`. Or, if we wish to discard any of the return values, we can assign it into the `_` special character. `_,retB := prefix2("abc", "def")` - which would discard the first return value.

You can optionally name the return variables at the function specification level, and issue a simple return statement at the end (without specifying the return variables).

```go
func prefix2(a,b string) (retA, retB string) {
    retA = a + " " + b
    retB = b + " " + a

    return
}
```

A method on the other hand interfaces off a specific type - all variables of that type can then use that function. It can only be used on types that have been declared in your packages. e.g. you can't use it on string types, but can be used on derived types that you declare in your package.

```go
type myString string

func (a myString) prefix(b string) string {
    return b + " " + string(a)
}
```

Then this would be used like:

```go
s := myString("my name is")
fmt.Printf("%s\n", s.prefix("And"))
```
