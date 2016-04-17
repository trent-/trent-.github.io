---
layout: post
title:  Go Concurrency
date:   2016-04-17 18:25:00 +1000
---

Go has a concurrency system that allows you to run program units in parallel. At the most basic level, this is done by prefixing a function call with the `go` command. To demonstrate this, I set up a simple example as per"

```go
func doLoop(strPrefix string){

    for i := 0; i < 10; i ++ {
        fmt.Println(strPrefix + ": ", i)
    }
}

func main(){
    fmt.Println("Program starting")

    go doLoop("new thread")
    doLoop("main thread")
}
```

However, I found that the concurrent thread never output anything. I can only conclude the main thread completed before the concurrent thread got started. I slightly tweaked this to include a 100ms sleep between outputs (as per the example on [tour.golang.org](https://tour.golang.org/concurrency/1)), and the desired result can be demonstrated:

```go
func doLoop(strPrefix string){

    for i := 0; i < 10; i ++ {
        fmt.Println(strPrefix + ": ", i)
        time.Sleep(100 * time.Millisecond)
    }
}

func main(){
    fmt.Println("Program starting")

    go doLoop("new thread")
    doLoop("main thread")
}
```

Output:
```
Program starting
main thread:  0
new thread:  0
main thread:  1
new thread:  1
main thread:  2
new thread:  2
main thread:  3
new thread:  3
main thread:  4
new thread:  4
main thread:  5
new thread:  5
main thread:  6
new thread:  6
main thread:  7
new thread:  7
main thread:  8
new thread:  8
main thread:  9
new thread:  9
```

Which, as we can see is running both routines simultaneously. Not a very useful example, but just demonstrated both threads running.

The problem with the above example, once the concurrent process has started - we have no way to communicate with the process. It would be more beneficial if we could open up a communication channel between threads. Luckily, this is possible with `channels`.

First, a channel is created with the `make` function where you pass in the commands `chan` and the data type used to communicate (`int`, `string`, etc).

```go
numChannel := make(chan int)
```

Then, to send and receive values across the channel, you use the channel operator (`<-`).

To send a value down the channel, you start with the channel variable, then the channel operator and finally the value you wish to send across the channel (`numChannel <- 1` -- to send 1 across the channel).

To receive a value, you put the channel variable immediately after the channel operator (`v := <-numChannel` -- assign the value received into `v`).

For a basic example of using a channel to return a value (one that doesn't really require concurrency):

```go
package main

import (
    "fmt"
)

func main(){
    fmt.Println("Program starting")

    numChannel := make(chan int)

    go get_inv(55, numChannel)
    go get_inv(-134, numChannel)

    a, b := <-numChannel,<-numChannel

    fmt.Println("55 inverted is", a)
    fmt.Println("-134 inverted is", b)

}

func get_inv(i int, c chan int){

    inversedVal := i/-1
    c <- inversedVal

}
```

By default, a channel will be unbuffered - but you can opt to buffer it by passing in a second parameter that is the size of the buffer. Once the specified size of the buffer fills up, the channel won't be able to open up any more communications.

Removing the concurrency aspect, if we make a channel with a buffer and try to use the channel with more values than the buffer size permits, we will get a deadlock error.

```go
func main(){
    fmt.Println("Program starting")

    numChannel := make(chan int, 2)

    numChannel <- 10
    numChannel <- 20
    numChannel <- 30

    fmt.Println(<-numChannel)
    fmt.Println(<-numChannel)
    fmt.Println(<-numChannel)
}
```

Output:

```
Program starting
fatal error: all goroutines are asleep - deadlock!
```


When receiving values from a channel, there are two values you can retrieve - the value being send, and a boolean on if the channel is still open. Closing a channel is a way for the sender to flag that no more values will be coming through, so the receiver can act accordingly (useful if using the `range` function). A channel is closed with the `close` function, passing in a variable to the channel.

```go
func main(){
    fmt.Println("Program starting")

    numChannel := make(chan int, 10)

    numChannel <- 10
    close(numChannel)


    v,isOpen := <- numChannel
    fmt.Println(v, isOpen)

    v,isOpen = <- numChannel
    fmt.Println(v, isOpen)
}

```

Output:
```
Program starting
10 true
0 false
```

or, if a function will return a series of values, you can use the `range` function which will end as soon as the channel is closed.

```go
func main(){
    fmt.Println("Program starting")

    numChannel := make(chan int, 10)

    numChannel <- 10
    numChannel <- 20
    close(numChannel)


    for num := range numChannel {
        fmt.Println(num)
    }
    fmt.Println("No more reading from channel")


}
```


When using channels, the `select` statement can come in handy - which is typically used with a combination of `for` (before) and `case` (beneath) which is used to retrieve values from different channels that may have been set up. The select will block until one of the channels received a value. You may opt to have a `default` block to sleep between scans.

```go
func chanHandler(numbers, quit chan int){
    //var x int
    numbers <- 5
    quit <- 1
}

func main(){
    fmt.Println("Program starting")

    numChannel := make(chan int)
    quitChannel := make(chan int)
    var numReceived int
    go chanHandler(numChannel, quitChannel)

    for {
        select {
        case numReceived = <-numChannel:
            fmt.Println("Received number", numReceived)
        case <-quitChannel:
            fmt.Println("Quit received")
            return
        default:
            //fmt.Println("Nothing received")
            time.Sleep(50 * time.Millisecond)
        }
    }
}
```

Output
```
Program starting
Received number 5
Quit received
```

Keep in mind, without the `default` condition, the select will block until a value is received.
