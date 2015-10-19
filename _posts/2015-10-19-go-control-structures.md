---
layout: post
title:  Go control structures
date:   2015-10-19 14:56:00 +1000
---

Like any language, Go comes equipped with loops and conditional statements. The first key point to take note of is that Go does away with the enclosing parenthesis. The blocks are also wrapped in curly braces, but the opening brace must be on the same line as the initial statement. One other important aspect is the fact that other languages generally allow you to leave out the curly braces if the body is only one command. This is not the case in Go - the curly braces are mandatory - always.

## Loops

Common loop constructs in other languages are `do`, `while`, `do-while` and `for`. Go does away with these varieties and has just one - the `for` loop.

The for loop you see in other languages typically consists of variable initialiser, condition; incrementer. This remains the case in Go:

```go
for i := 0; i < 10; i++ {
    fmt.Println(i)
}
```

You can leave the initialiser and incrementer aspects, then your loop becomes the equivalent of a while loop.

```go
i := 0
for i < 10 {
    fmt.Println(i)
    i++
}
```

To loop forever, other languages you often see `while(true)`. Here, we still use a `for` loop, but don't add any statements.

```go
for {
    fmt.Println(i)
    i++
}
```

## Conditional

As previously mentioned, one of the key differences in Go is that you do not wrap the conditions in parenthesis. But one extra addition is that on the opening if statements, you can add variable initialisers which are then available for the life of the control structure for the life of the block (any if, or else blocks).

```go
if i := 10; i < 8 {

    fmt.Println("i less than 8")

} else {

    fmt.Println("i not less than 8; it is", i)

}
```

The other conditional statement, `switch`, follows this same syntax. That is, you can initialise a variable at the start, before specifying which variable you are switching for.

```go
switch i:=3;i {

    case 1:
        fmt.Println("i is 1")
    case 2:
        fmt.Println("i is 2")
    case 3:
        fmt.Println("i is 3")
    default:
        fmt.Println("Something else")
}
```

As an alternative to a messy looking if-elseif block, you could opt to use a switch statement passing in no variable, where each case represents a condition.

```go
switch {
    case i == 1:
        fmt.Println("i is 1")
    case i == 2:
        fmt.Println("i is 2")
    case i == 3:
        fmt.Println("i is 3")
    default:
        fmt.Println("Something else")

}
```

The final thing you will want to be aware of is the `defer` statement. This statement evaluates the statement when it occurs in the code, but doesn't execute it at that point - it does so by pushing the statement onto a stack, and won't run it until the function returns (executing ina LIFO basis). This is useful for closing any streams that you may have opened during your function.

```go
defer fmt.Println("Closing streams")
fmt.Println("Task 1")
fmt.Println("Task 2")

/*
Output:

Task 1
Task 2
Closing streams
*/
```
