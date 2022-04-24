# 30 Days of Go: Pointers and Structs

## Learning Update

This post covers days 4, 5, and six of my 30 days of Golang journey. I was not able to cover a whole lot because sometimes life gets in the way. What I was able to cover was quite important though. It was the last step I needed before trying to create an application.

These are my notes on Go's pointers and Structs.

## Pointers

Go pointers allow you to pass references to values within a program. An example of this would be as follows.

```go
package main

import "fmt"

// This is a regular function in Go
// It has a regular integer argument
func nonPointerFunc(val int) {
	val = 0
}

// This function takes a pointer as an argument
// This is denoted by the *int, this means pval is a pointer to an integer value
func pointerFunc(pval *int) {
	// The * infront of pval dereferences the pointer so that we can work with the
	// value that the pointer references
	*pval = 0
}

func main() {
	// Initialize a value
	x := 1
	// Pass the value to nonPointerFunc
	nonPointerFunc(x)
	// After execution print the value of x and you will find it is 1
	fmt.Println("Value after non pointer function: ", x)
	
	// Pass a pointer to x into pointerFunc
	// To get a pointer to a function use the syntax &value
	// &x is the pointer for x
	pointerFunc(&x)
	// Print x after the execution and you will get a value of 0
	fmt.Println("Value after pointer function:", x)
	
	// To see the pointer itself you can print it out this way.
	fmt.Println("Pointer ", &x)
}
```

To understand pointers better I watched [this very helpful video by Junmin Lee](https://www.youtube.com/watch?v=sTFJtxJXkaY). She explains Golang's stack and heap to illustrate how pointers work. These are the takeaways I got based on my understanding:

- When you pass an argument into a Go function e.g calling a function from the main function. Go will create a copy of it and that copy is what will be used for computations within the called function. The variable that was passed in from main will remain unchanged.
- Passing a pointer of a value into a function will not create a copy of the value. The value referenced by the pointer is what will be modified by the function.
- Using pointers is more efficient because it does not create copies of variables. By using pointers we are giving up immutability for efficiency. To understand this better I will do more research and write a blog post on this.

## Structs

Structs are typed collections of fields. Go uses structs to group data together to form records.

Creating a struct

```go
type macbook struct {
	model string
	price int
}

// creating a variable with the struct
air = macbook{"M1 air 8gb", 1000}
pro = macbook{model: "M1 pro 32gb", price:2000}
// Fields that have been ommitted will be zero-valued
proMax = macbook{price: 3500}

// struct fields can be accessed with a dot
fmt.Println(pro.model)

// dots can also be used with a struct pointer, the pointer will be automatically
// dereferenced
// Note that structs are mutable so updating the pointer will also update the initial value

sp := &air
fmt.Println(sp.price)
```

To write more idiomatic Go constructor functions are used to create new structs. 

```go
func newMacbook(model string, price int) *macbook {
	m := macbook{model: model, price: price}
	m.price = 1000
	return &m
}
```

- Returning the pointer of a local variable is allowed because it will survive the scope of the function. 
To use this constructor function we write this line of code.
- I need to read more on stack and heap allocation with this type of constructor function. 

```go
mini = newMacbook("M1 mini 8gb", 700)
fmt.Println(mini)
```

### Methods

Go allows for methods on struct types. These methods are defined as follows:

```go
type rect struct {
	width, height int
}

// golang methods can have pointer recivers
func (r *rect) area() int {
	return r.width * r.height
}

// golang methods can have value recivers
func (r rect) perim() int {
	return 2*r.width + 2*r.height
}

func main() {
	r := rect{width: 20, height: 10}
	
	// Go automatically handles conversion between values and pointers for method calls.
	rp := &r
	fmt.Prinln("area: ", rp.area())
}
```

Pointer receiver types can be used to avoid copying on method calls. They also allow the method to mutate the receiving struct.

## What next

I have gone through quite a few basics and now would like to learn by actually building something. The first thing I'm going to do is exercise one from the gophercises website. The exercise is to build a quiz game. The game reads questions and answers from a CSV and allows a user to answer them from the terminal. 

I'll be documenting building this over the next few days.


