
## WARNING unfinished

unfinished


## Rust in a Nutshell

* Syntax tokens similar to C
* Ownership of memory enforced at compile time
* Statically linked
* Functional influences, lots of ()()}{}()
* Integrated package manager and tester: cargo
* Concurrency: Rayon package
* formatter: rustfmt filename.rs

## Hello World

```rust
fn main() {
    println!("Hello World");
}
```

```bash
$ rustc main.rs
```

## Hello Packages, Tests, Dependencies

```bash
$ cargo new bin   # start new executable project
$ ls -lR          # creates skeleton of files
src/main.rs       # main executable, main()
Cargo.toml        # Cargo.toml defines packaging and
$ cat Cargo.toml  # also dependencies and other details
[package]
name = "helloworld"
version = "0.1.0"
authors = ["ada astra <ada@astra.moon>"]

[dependencies]
serde = "1.0.80"

$ cargo build     # downloads dependencies from crates.io 
$ cargo run       # runs program created from main.rs
$ cargo test      # runs tests (in parallel by default)
$ cargo test -- --test-threads=1  # run tests one at a time
$ cargo test -- --nocapture       # run tests, show output
```

## types, declarations, initialization
```rust

let x: bool = false; // let keyword
let y: char = 'n';   // chars are 4 bytes
let z = x;           // rust figures out type
let a: i8 = -2;      // 8 bit signed integers, also i16, i32, i64  
let b: u8 = 200;     // 8 bit unsigned integers, also u16, u32, u64 
let n: f32 = 0.32;   // 32 bit float, also f64 for 64 bit           
let r: [int;3] = [3,4,5];    // array of 3 int, cannot grow
let v:Vec<int> = Vec::new(); // vector of int, can grow
let (p,d,q) = (4,5,6)        // tuple
let m = (4,5,6)              // another tuple
let t,s = (2,"two")          // tuple can have different types
let o = String::from(" hello "); // String
, slice, str
usize, isize       

struct X { a: int, b: int };
let x = X{ 5, 6 };

let numx: u16 = 42;       // 16 bit unsigned int value to be casted
let numy: u8 = x as u8;   // casting to 8 bits
let numz: i32 = x as i32; // casting to signed 32 bit
```

## Operators

```rust
1 + 2 - 3 * 4 / 5  // arithmetic add, minus, multiply, divide
7 % 5              // modulo (remainder)
& | ^ << >>        // bitwise and, or, xor, leftshift, rightshift
&^                 // bitwise andnot
a == b != c < d <= e > f >= g  // logical comparison
a && b || c ! d    // logical boolean , and, or, not

let a = 5;         // pointer + dereference example
let b = &a;        // &a is 'address of a'
let c = *b;        // *b is contents of memory at address in b (dereference)
print("{}",c);     // 5

let s = String::from("上善若水");
println!("{} {} {}",s.as_ptr(), s.len(), s.capacity());

```

### Printing

```rust
println!("Hello, 你好, नमस्ते, Привет, ᎣᏏᏲ"); 
print!("Hi, the answer is {} ",42);
println!();

p := struct { X, Y int }{ 17, 2 }
println!( "My point: {} x coord={}", p, p.X )
let s = format!( "My point: {} x coord={}", p, p.X )

println!(format!("%d hex:%x bin:%b fp:%f sci:%e",17,17,17,17.0,17.0));
s2 := fmt.Sprintf( "%d %f", 17, 17.0 ) // formatted print to string variable

let hellomsg = r###" 
 "Hello" in Chinese is 你好 ('Ni Hao')
 "Hello" in Hindi is नमस्ते ('Namaste')
"###;

println!(" {:>4} {:>4} ", 232, 8 );   // pad to 4 spaces, align right

```
## If, conditionals, controls

if x == 4 {
    println!("x is four");
} else if x == 3 {
    println!("x is three");
} else {
    println!("x is something else");
}

```go
func main() {
	// Basic one
	if x > 10 {
		return x
	} else if x == 10 {
		return 10
	} else {
		return -x
	}

	// You can put one statement before the condition
	if a := b + c; a < 42 {
		return a
	} else {
		return a - 42
	}

	// Type assertion inside if
	var val interface{}
	val = "foo"
	if str, ok := val.(string); ok {
		fmt.Println(str)
	}
}
```


### loop, for, while
```rust
for i in 0..10 { print!("{} ",x) };               // 0 to 10
for i in (0..10).rev() { print!("{} ",x) };       // 10 down to 0
for i in (0..10).step_by(2) { print!("{} ",x) };  // skip every other item

let v = vec![1, 35, 64, 36, 26];	// vector iterate
for n in v { println!("{}",n) }		// ordinary
for (i, n) in v.iter().enumerate() {	// use index and item 
	println!("{},{}", i, n); }

done = false;
loop { if done { break; } }		// loop

let i = 10;				// while
while i > 0; {
	println("{}",i);
	i -= 2;
}
```

## Functions
```rust
fn adder( a:i8, b:i32) -> i32 { b + a }
fn multi_return( a:i8, b:i32) -> (char,i32) { ('s',a+b) }
let (x, s) = multi_return( 3, 56 ); 

Function with variable number of parameters: doesn't exist. 
Alternative: builder pattern, different named functions. 
https://www.reddit.com/r/rust/comments/4jgvho/idiomatic_way_to_implement_optional_arguments/
```

### Functions As Values And Closures
```go
func main() {
    // assign a function to a name
    add := func(a, b int) int {
        return a + b
    }
    // use the name to call the function
    fmt.Println(add(3, 4))
}

// Closures, lexically scoped: Functions can access values that were
// in scope when defining the function
func scope() func() int{
    outer_var := 2
    foo := func() int { return outer_var}
    return foo
}

func another_scope() func() int{
    // won't compile because outer_var and foo not defined in this scope
    outer_var = 444
    return foo
}


// Closures
func outer() (func() int, int) {
    outer_var := 2
    inner := func() int {
        outer_var += 99 // outer_var from outer scope is mutated.
        return outer_var
    }
    inner()
    return inner, outer_var // return inner func and mutated outer_var 101
}
```

### Variadic Functions
```go
func main() {
	fmt.Println(adder(1, 2, 3)) 	// 6
	fmt.Println(adder(9, 9))	// 18

	nums := []int{10, 20, 30}
	fmt.Println(adder(nums...))	// 60
}

// By using ... before the type name of the last parameter you can indicate that it takes zero or more of those parameters.
// The function is invoked like any other function except we can pass as many arguments as we want.
func adder(args ...int) int {
	total := 0
	for _, v := range args { // Iterates over the arguments whatever the number.
		total += v
	}
	return total
}
```

### Switch
```go
    // switch statement
    switch operatingSystem {
    case "darwin":
        fmt.Println("Mac OS Hipster")
        // cases break automatically, no fallthrough by default
    case "linux":
        fmt.Println("Linux Geek")
    default:
        // Windows, BSD, ...
        fmt.Println("Other")
    }

    // as with for and if, you can have an assignment statement before the switch value
    switch os := runtime.GOOS; os {
    case "darwin": ...
    }

    // you can also make comparisons in switch cases
    number := 42
    switch {
        case number < 42:
            fmt.Println("Smaller")
        case number == 42:
            fmt.Println("Equal")
        case number > 42:
            fmt.Println("Greater")
    }

    // cases can be presented in comma-separated lists
    var char byte = '?'
    switch char {
        case ' ', '?', '&', '=', '#', '+', '%':
            fmt.Println("Should escape")
    }
```

## Arrays, Slices, Ranges

### Arrays
```go
var a [10]int // declare an int array with length 10. Array length is part of the type!
a[3] = 42     // set elements
i := a[3]     // read elements

// declare and initialize
var a = [2]int{1, 2}
a := [2]int{1, 2} //shorthand
a := [...]int{1, 2} // elipsis -> Compiler figures out array length
```

### Slices
```go
var a []int                              // declare a slice - similar to an array, but length is unspecified
var a = []int {1, 2, 3, 4}               // declare and initialize a slice (backed by the array given implicitly)
a := []int{1, 2, 3, 4}                   // shorthand
chars := []string{0:"a", 2:"c", 1: "b"}  // ["a", "b", "c"]

var b = a[lo:hi]	// creates a slice (view of the array) from index lo to hi-1
var b = a[1:4]		// slice from index 1 to 3
var b = a[:3]		// missing low index implies 0
var b = a[3:]		// missing high index implies len(a)
a =  append(a,17,3)	// append items to slice a
c := append(a,b...)	// concatenate slices a and b

// create a slice with make
a = make([]byte, 5, 5)	// first arg length, second capacity
a = make([]byte, 5)	// capacity is optional

// create a slice from an array
x := [3]string{"Лайка", "Белка", "Стрелка"}
s := x[:] // a slice referencing the storage of x
```

### Operations on Arrays and Slices
`len(a)` gives you the length of an array/a slice. It's a built-in function, not a attribute/method on the array.

```go
// loop over an array/a slice
for i, e := range a {
    // i is the index, e the element
}

// if you only need e:
for _, e := range a {
    // e is the element
}

// ...and if you only need the index
for i := range a {
}

// In Go pre-1.4, you'll get a compiler error if you're not using i and e.
// Go 1.4 introduced a variable-free form, so that you can do this
for range time.Tick(time.Second) {
    // do it once a sec
}

```

## Maps

```go
var m map[string]int
m = make(map[string]int)
m["key"] = 42
fmt.Println(m["key"])

delete(m, "key")

elem, ok := m["key"] // test if key "key" is present and retrieve it, if so

// map literal
var m = map[string]Vertex{
    "Bell Labs": {40.68433, -74.39967},
    "Google":    {37.42202, -122.08408},
}

// iterate over map content
for key, value := range m {
}

```

## Structs

There are no classes, only structs. Structs can have methods.
```go
// A struct is a type. It's also a collection of fields

// Declaration
type Vertex struct {
    X, Y int
}

// Creating
var v = Vertex{1, 2}
var v = Vertex{X: 1, Y: 2} // Creates a struct by defining values with keys
var v = []Vertex{{1,2},{5,2},{5,5}} // Initialize a slice of structs

// Accessing members
v.X = 4

// You can declare methods on structs. The struct you want to declare the
// method on (the receiving type) comes between the the func keyword and
// the method name. The struct is copied on each method call(!)
func (v Vertex) Abs() float64 {
    return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

// Call method
v.Abs()

// For mutating methods, you need to use a pointer (see below) to the Struct
// as the type. With this, the struct value is not copied for the method call.
func (v *Vertex) add(n float64) {
    v.X += n
    v.Y += n
}

```
**Anonymous structs:**
Cheaper and safer than using `map[string]interface{}`.
```go
point := struct {
	X, Y int
}{1, 2}
```

## Pointers
```go
p := Vertex{1, 2}  // p is a Vertex
q := &p            // q is a pointer to a Vertex
r := &Vertex{1, 2} // r is also a pointer to a Vertex

// The type of a pointer to a Vertex is *Vertex

var s *Vertex = new(Vertex) // new creates a pointer to a new struct instance
```

## Interfaces
```go
// interface declaration
type Awesomizer interface {
    Awesomize() string
}

// types do *not* declare to implement interfaces
type Foo struct {}

// instead, types implicitly satisfy an interface if they implement all required methods
func (foo Foo) Awesomize() string {
    return "Awesome!"
}
```

## Embedding

There is no subclassing in Go. Instead, there is interface and struct embedding.

```go
// ReadWriter implementations must satisfy both Reader and Writer
type ReadWriter interface {
    Reader
    Writer
}

// Server exposes all the methods that Logger has
type Server struct {
    Host string
    Port int
    *log.Logger
}

// initialize the embedded type the usual way
server := &Server{"localhost", 80, log.New(...)}

// methods implemented on the embedded struct are passed through
server.Log(...) // calls server.Logger.Log(...)

// the field name of the embedded type is its type name (in this case Logger)
var logger *log.Logger = server.Logger
```

## Errors
There is no exception handling. Functions that might produce an error just declare an additional return value of type `Error`. This is the `Error` interface:
```go
type error interface {
    Error() string
}
```

A function that might return an error:
```go
func doStuff() (int, error) {
}

func main() {
    result, err := doStuff()
    if err != nil {
        // handle error
    } else {
        // all is good, use result
    }
}
```

# Concurrency

## Goroutines
Goroutines are lightweight threads (managed by Go, not OS threads). `go f(a, b)` starts a new goroutine which runs `f` (given `f` is a function).

```go
// just a function (which can be later started as a goroutine)
func doStuff(s string) {
}

func main() {
    // using a named function in a goroutine
    go doStuff("foobar")

    // using an anonymous inner function in a goroutine
    go func (x int) {
        // function body goes here
    }(42)
}
```

## Channels
```go
ch := make(chan int) // create a channel of type int
ch <- 42             // Send a value to the channel ch.
v := <-ch            // Receive a value from ch

// Non-buffered channels block. Read blocks when no value is available, write blocks until there is a read.

// Create a buffered channel. Writing to a buffered channels does not block if less than <buffer size> unread values have been written.
ch := make(chan int, 100)

close(ch) // closes the channel (only sender should close)

// read from channel and test if it has been closed
v, ok := <-ch

// if ok is false, channel has been closed

// Read from channel until it is closed
for i := range ch {
    fmt.Println(i)
}

// select blocks on multiple channel operations, if one unblocks, the corresponding case is executed
func doStuff(channelOut, channelIn chan int) {
    select {
    case channelOut <- 42:
        fmt.Println("We could write to channelOut!")
    case x := <- channelIn:
        fmt.Println("We could read from channelIn")
    case <-time.After(time.Second * 1):
        fmt.Println("timeout")
    }
}
```

### Channel Axioms
- A send to a nil channel blocks forever

  ```go
  var c chan string
  c <- "Hello, World!"
  // fatal error: all goroutines are asleep - deadlock!
  ```
- A receive from a nil channel blocks forever

  ```go
  var c chan string
  fmt.Println(<-c)
  // fatal error: all goroutines are asleep - deadlock!
  ```
- A send to a closed channel panics

  ```go
  var c = make(chan string, 1)
  c <- "Hello, World!"
  close(c)
  c <- "Hello, Panic!"
  // panic: send on closed channel
  ```
- A receive from a closed channel returns the zero value immediately

  ```go
  var c = make(chan int, 2)
  c <- 1
  c <- 2
  close(c)
  for i := 0; i < 3; i++ {
      fmt.Printf("%d ", <-c)
  }
  // 1 2 0
  ```

### Ownership, Borrowing, Movement


```rust
// variables have one owner at a time. trying to have two owners
// is called "moving", and causes a compile time error.

let s1 = String::from("hello");
let s2 = s1; // s1 "moved" to s2, which is new owner
println!("{}, world!", s1); // error, s1, as String, has no 'Copy' trait
println!("{}, world!", s1.clone()); // OK, copy of s1 made, s1 itself is not 'moved'
```

### Files

```rust
use std::fs::File;
use std::io::Read;

let filename = "test.txt";
match File::open(filename) {
	Err(why) => panic!("failed to open file '{}': {}", filename, why),
        Ok(mut f) => {
        	println!("ok, opened {}",filename);
                let mut data = String::new();
                match f.read_to_string( &mut data ) {
                	Err(why) => panic!("failed to read {}, {}",filename,why),
                        Ok(s) => println!("read {} bytes",s),
                };
	},
}
```



## Reflection
### Type Switch
A type switch is like a regular switch statement, but the cases in a type switch specify types (not values), and those values are compared against the type of the value held by the given interface value.
```go
func do(i interface{}) {
	switch v := i.(type) {
	case int:
		fmt.Printf("Twice %v is %v\n", v, v*2)
	case string:
		fmt.Printf("%q is %v bytes long\n", v, len(v))
	default:
		fmt.Printf("I don't know about type %T!\n", v)
	}
}

func main() {
	do(21)
	do("hello")
	do(true)
}

// Credits

/*
Based on a8m's go-lang-cheat-sheet, github.com/a8m/go-lang-cheat-sheet
carols10cent's js-rust cheatsheet, https://gist.github.com/carols10cents/65f5744b9099eb1c3a6f
c0g https://stackoverflow.com/questions/29483365/what-is-the-syntax-for-a-multiline-string-literal
codngame https://www.codingame.com/playgrounds/365/getting-started-with-rust/primitive-data-types
rust-lang.org, rust book, https://doc.rust-lang.org/book/second-edition
rust-lang.org, rust reference, https://doc.rust-lang.org
Adam Leventhal, http://dtrace.org/blogs/ahl/2015/06/22/first-rust-program-pain/
Shepmaster https://stackoverflow.com/questions/33133882/fileopen-panics-when-file-doesnt-exist
*/

