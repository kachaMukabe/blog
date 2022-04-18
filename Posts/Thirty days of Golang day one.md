# 30 Days of Go: Day 1
## Introduction
I have decided to take part in a challenge to learn Golang in the next 30 days. I found a [Go hackathon](https://gohack.devpost.com) and thought this is the perfect opportunity because I have wanted to learn it for a while now. I have decided to split this learning up in a couple of ways. The idea is to build an application for the hackathon. My current app idea is a citizen science API. This API will allow users to upload photos of animals such as birds or insects.

I have split my learnings into the following categories:
- Weeks 1 & 2: Learn the syntax and basics of Golang and do the [gophercises](https://gophercises.com/)
- Week 3 & 4: Build out the API

## Golang syntax
This information is referenced Golang documentation and especially [Go by example](https://gobyexample.com).

### Variables
- Golang's variables are explicitly declared like C#, Java
- Golang allows multiple variables to be declared in one line
- If the type has not been specified go will infer the types from initialized values

```go
var a = "Hello world"
var age int = 8
// When the type has been declared it is used for both variables
var name, game string = "Scott Pilgrim", "vs The world"
// This is allowed because go will infer the types from initialization
var name, age = "Knives Chau", 18
```

- Variables that are declared but not initialized will be zero-valued
- All declared but not initialized variables must have a type

```go
var one int
// one = 0
var decision bool
// decision = false
var word string
// word = ''
```

- `:=` is shorthand for declaring and initializing a variable e.g 

```go
num := 50
```

### Constants
- Constants in Go work the same as in other languages. A const can be a string, character, bool, or numerical type.

```go
const test string = "Steven Stills"
```

- Numerical constants perform arithmetic with arbitrary precision
- A numerical constant has no type until it is given one from a function or is cast to a type

## Flow Control
### Loops
- Go only has for loops and they can be initialized in the following ways:

```go
i := 0
for i <= 4 {
	fmt.Println(i)
	i = i + 1
}

for j := 4; j <=8; j++ {
	fmt.Println(j)
}
```

- infinite loops have no conditions and can be stopped with a break statement

```go
for {
	fmt.Println("The infinite sadness")
	break
}
```

- the `continue` keyword can be used to go to the next iteration of a loop

```go
for i :=3; i <= 33; i++ {
	if i % 3 == 0 {
 continue
	}
	fmt.Println("Love evans")
}
```

### If else
- If else blocks are like most other programming languages
- Parenthesis are not needed in the if statement but braces are

```go
if x == 3 {
	fmt.Println("Young Neil")
} else if x > 5 {
	fmt.Println("Neil")
} else {
	fmt.Println("Meh")
}
```

- Go's if block can have a statement before the condition. Any variable declared in the statement can be used in all the branches of the if block.

```go
if x := 4; x < 0 {
	fmt.Println(x)
} else if x > 1 {
	fmt.Println("True")
} else {
	fmt.Println("Do something else")
}
```

- Go has no ternary if statements like javascript `x == 2? "Yes": "No"` 

### Switch statements
- A switch statement in Go is like other programming languages
- The default case is optional
- Cases can have multiple expressions
- a switch can have no expressions which will make it function like an if statement

```go
switch i {
case 1:
	fmt.Println("One")
case 2:
	fmt.Println("Two")
case 3, 4, 5:
	fmt.Println("Others")
default:
	fmt.Println("Default is optional")
}

// Swtich with no condition
t := 5
switch {
case t < 4:
	fmt.Println("Less than 4")
default:
	fmt.Println("Else")
}
```

- A type switch compares types instead of values
- Use this switch to discover an interface's type
- Using `.(type)` does not work outside a type switch

```go
myType := func(i interface{}){
	switch t := i.(type){
	case bool:
 fmt.Println("Im boolean")
	case int:
 fmt.Println("Im integer")
	default:
 fmt.Println("Im anything else")
	}
}
```

This was a fruitful first day and there's more to come.