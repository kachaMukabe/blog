# 30 Days of Go: Day 2 and 3

## How it's going

This is my log update for my golang learnings for days two and three. I've been itching to actually start building something. I'm getting pretty close to that point. But before that I'd like to go through functions, methods and structs. Once that's done I'll start going a little in depth with building a few simple toy applications.

## Day 2

I got quite a bit done on this day.

### Data structures

#### Arrays

- Arrays in Go work like they do in a language like Java. All arrays need a type and size when they are declared.
- All values in an initialized array are zero-valued

```go
var array [40]int

// Declare and initialize
array2 := [5]int{1, 3, 4, 5, 6}

// Access an index in the array
fmt.Println(array[20])

// add something to an index in the array
array[10] = 300

// get the size of the array
fmt.Println(len(array))
```

- Golang has 2D arrays. These are composed in the following way

```go
var twoD [4][9]int
for i := 0; i < 4; i++ {
	for j := 0; j < 9; j++ {
 twoD[i][j] = i + 
	}
}
```

#### Slices

- A slice is a data structure that works like an array with a variable size. It is like an arraylist in Java or a list in python (with the caveat that all items in the slice have to be of the same type).
- To initialize a slice Go has a builtin function `make`

```go
// Create a slice pass the type and initial size to make
sl := make([]string, 3)

// Declaring and initializing a slice
t := []string{"ab", "bc", "cd"}

// Access an item in a slice
fmt.Println(sl[2])

// set an index in a slice
sl[1] = "Hello"
```

- To add more items in a slice use the builtin function `append`. The append function takes a slice and a value that you would like to add to that slice and returns a new slice 

```go
sl = append(sl, "new")
```

- Slices can be copied using the builtin `copy` function

```go
// Create a new slice with the length of the slice you want to copy
c := make([]string, len(sl))
// copies sl into c
copy(c, sl)
```

- slices support slicing, like python lists

```go
// creates a nes slice from index 1 to 2
newSlice := sl[1:3]
// creates slice from index 0 to 3 (excluding 4)
newSlice = sl[:4]
// slice from index 2 to the last one
newSlice = sl[2:]
```

- slices support multidimensional data structures. The length of the inner slice can be variable.

```go
twoD := make([][]int, 3)
for i := 0; i < 3; i++ {
	innerLen := i + 1
	twoD[i] = make([]int, innerLen)
	for j := 0; j < innerLen; j++ {
 twoD[i][j] = i + j
	}
}

// result = [[0] [1 2] [2 3 4]]
```

#### Maps

- These are key/value associative data structures like python dicts
- Use `make` to create an empty map

```go
m := make(map[string]int)

// declare and initialize a map
m2 := map[string]int{"foo": 4, "bar": 40}

// set key/value pairs
m["one"] = 2
m["two"] = 3

// get a value with a key
v := m["one"]

// get number of key/value pairs
fmt.Println(len(m))

// To delete a key value pair use builtin function
delete(m, "two")
```

- A second value is returned when getting from a map, `value, prs := map[key]`. prs will be true if the key is in the map and false if it isn't

```go
_, prs := m["two"]
```

- Blank identifier `_` is used when a value is not needed. This is necessary because Go requires that you use every variable that has been declared.

#### Range

- In Go we can use a range to iterate over data structures like arrays, slices, maps and strings

```go
// array, slices
for index, value := range array {
	fmt.Println(value)
}

//map 
for key, value := range exMap {
	fmt.Printf("%s -> %s\n", key, value)
}

// iterate over map keys only
for key := range exMap {
	fmt.Println(key)
}

// iterate over strings
for index, ru := range "Golang" {
	fmt.Println(ru)
}
```

## Day 3

I didn't get through as much today but still interesting things were coverd.

#### Functions
- Functions can have multiple return values. These functions are usually used to return the result and error values from a function
- Variadic functions can be called with any number of arguments
- Functions are defined in the following way:

```go
// function structure
// function name(arguments) return type {}
func sayHello(name string) string {
	return "Hello " + name 
}
// call the function sayHello("Kacha")

// function with multiple return values
func names() (string, string) {
	return "Scott", "Ramona"
}

// variadic function
func add(nums ...int) {
	total := 0
	for _, num := range nums {
 total += num
	}
	return total
}
```

- Go also supports anonymous functions which can form closures. I do not quite understand closures quite yet so I'll have that in a different blog post.

## ...

I want to try and blog on each day but that might not be possible so I will blog my progress at least every two or three days. 

