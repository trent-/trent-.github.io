---
layout: post
title:  Go structs and interfaces
date:   2015-10-11 11:28:00 +1200
---

Go doesn't have a `class` to define custom types. Instead, you would use a `struct`.

```go
type MotorVehicle struct {
    wheelSize int
    modelName string
    manafacturer string
}
```

Then, to add method to this type, we would just implement methods against this type as described on our previous post.

```go
func (mv MotorVehicle) startEngine(){

    fmt.Printf("Starting %s's engine\n", mv.modelName)

}
```

Multiple inheritance can be achieved by type embedding.

```go
type Car struct {
    MotorVehicle
    //otherFields
}
```

When type embedding, the fields (and methods of the parent type) can be referenced without sub-referencing the embedded type (assuming you don't overload that same field names). This of course only works when adding an anonymous field (not giving the embedded type a name) to the struct. So, using the above example, we could do:

```go
var mazdaCx9 Car
mazdaCx9.wheelSize = 17
//or
mazdaCx9.MotorVehicle.wheelSize = 17
```


..

To implement an interface is go, you firstly define the interface as a type (similar to how a struct is defined). Whereas struct's can only house fields, interfaces can only house function specifications.

```go
type PoweredVehicle interface {
    startEngine()
}
```

So, with that, any type that implements the method startEngine, becomes a PoweredVehicle. Then we can pass any derived types as a PoweredVehicle type to call any supporting methods.

```go
func goToShops(pv PoweredVehicle){
    pv.startEngine()
    //further implementation
}

myCar := MotorVehicle{}
myCar.wheelSize = 11
myCar.modelName = "abc"
myCar.manafacturer = "def"

goToShops(myCar)
```

Further reading:

* [http://spf13.com/post/is-go-object-oriented/](http://spf13.com/post/is-go-object-oriented/)
* [http://nathany.com/good/](http://nathany.com/good/)
* [http://www.drdobbs.com/open-source/go-introduction-how-go-handles-objects/240005949](http://www.drdobbs.com/open-source/go-introduction-how-go-handles-objects/240005949)
