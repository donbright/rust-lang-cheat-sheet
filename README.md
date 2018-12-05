
## WARNING very unfinished

unfinished, has not been quality checkedm contains errrors

Based on a8m's go-lang-cheat-sheet, github.com/a8m/go-lang-cheat-sheet


## Rust in a Nutshell

* Syntax tokens similar to C
* Ownership of memory enforced at build time
* Statically linked
* Functional language influences
* Control flow using patterns, 'match' keyword
* Packages: 'cargo', https://crates.io
* Testing: cargo test, #[test]
* Concurrency: Rayon package
* formatter: rustfmt filename.rs (in place)
* compiler engine: LLVM
* Unsafe Rust: pointers, C-ish things

## Hello World

```rust
fn main() {
    println!("Hello World");
}
```

```bash
$ rustc main.rs   # creates 'main' executable
```

## Hello Packages, Tests, Dependencies

```bash
$ cargo new bin   # start new executable project
$ ls -lR          # list our skeleton of files
src/main.rs       # main.rs, has main() entry point
Cargo.toml        # Cargo.toml defines packaging
$ $EDITOR Cargo.toml  # add dependencies and other details
[package]
name = "helloworld"
version = "0.1.0"
authors = ["ada astra <ada@astra.moon>"]

[dependencies]
serde = "1.0.80"

$ cargo build     # downloads dependencies + builds main.rs 
$ cargo run       # runs program created from main.rs
$ cargo test      # runs tests (in parallel by default)
$ cargo test -- --test-threads=1  # run tests one at a time
$ cargo test -- --nocapture       # run tests, show output
```

## types, variables, declarations, initialization
```rust
let x: bool = false; // let keyword
let k = false;       // rustc can determine some types
let y: char = '上';  // all chars are 4 bytes
let 上 = 5;          // error. identifiers must be ASCII characters 
let a: i8 = -2;      // 8 bit signed integers, also i16, i32, i64  
let b: u8 = 200;     // 8 bit unsigned integers, also u16, u32, u64 
let n: f32 = 0.45;   // 32 bit float           
let n = 42.01f64;  c // special syntax. numberf64 gives a 64 bit float. n is 64bit
let r: [u8;3] = [3,4,5];      // array of 3 int, cannot grow
let v:Vec<u8> = Vec::new();   // vector of int, can grow
let v = vec![3,4,5];          // initialize vector using vec! macro
v.push( 2 );                  // append item to vector
let vs = v.len();             // length of vector
let (p,d,q) = (4,5,6);        // tuple is a way to assign multiple variables
print("{}",p);                // you can use them alone after assignment
let m = (4,5,"a");            // tuples can mix types
let (a,b) = m.1, m.3;         // tuple dereference with .1, .2, .3

let s = String::from("上善若水"); // String
let s2 = "水善利萬物而不爭";       // string literal, type is &str
let s3 = format!("{}{}",s2,s3); // concatenate strings
let hellomsg = r###"            // Multi-line with embedded quotes
 "Hello" in Chinese is 你好 ('Ni Hao')
 "Hello" in Hindi is नमस्ते ('Namaste')
"###;

usize, isize              // for loops, vector length, etc

let numx: u16 = 42;       // casting, start with 16 bit unsigned integer
let numy: u8 = x as u8;   // cast to unsigned 8 bit integer
let numz: i32 = x as i32; // cast to signed 32 bit integer

static FOOBY: i32 = 5;    // static, global-ish variable

let _ = expr; // determine the type of expression expr by looking at rustc error

```

## Mutability basics

```rust
let x = false;           // all variable bindings are immutable by default
x = true;                // error. can't change an immutable var
let mut p: bool = false; // "mut" designates a binding as mutable
p = true;                // ok, mutable binding can change;

// temporary mutability
let mut point = 6;  // point is mutable
point = 5;
let point = point; // now point is immutable
point = 6; // error
```


## Structs

```rust
struct Wheel{ r:i8, s:i8};  // basic struct, like C, pascal, etc
struct badWheel{ mut r: i8, mut h: i8, }; // error, mut keyword doesnt work inside struct
let w = Wheel{r:5,s:7};   // new wheel, radius 5, spokes 7, immutable binding
w.r = 6; // error, cannot mutably borrow field of immutable binding
let mut mw = Wheel{r:5,s:7}; //  new mutable wheel. fields inherit mutability of struct;
mw.r = 6;  // ok

impl Wheel {              // impl -> implement methods for struct, kinda like a class
        fn dump(    &self) { println!("{} {}",self.r,self.s); }  // immutable self
	fn badgrow(    &self) { self.s += 4; } // error, cannot mutably borrow field of immutable binding
        fn  okgrow(&mut self) { self.s += 4; } // ok, mutable self
};
w.dump(); // ok , w is immutable, self inside dump() is immutable
w.okgrow(); // error, cannot borrow immutable local variable `w` as mutable
mw.dump(); // ok 
mw.okgrow(); // ok, mw is mutable, self inside grow() is mutable

```

## Ownership

Resources can only have one owner. They can be 'moved' from one owner to another.

Variables are destroyed, (their heap memory is freed), at the end of a 'scope'

```rust
// stack memory, no moves, only copies
let a = 5;
let b = a;
let c = a;

// heap memory
let a = String::new(); 
let b = a;  // 'move' of ownership from a to b
let c = a;  // error. cannot "move" a again, b owns it

// heap memory + function call
let a = String::new();
let b = a;  // 'move' of ownership from a to b
fn f(t:String) { } // function takes ownership of the variable passed as t
f(a); // error. cannot 'move' a again, and calling f(a) moves a into f

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


```


## Printing

```rust
println!("Hello, 你好, नमस्ते, Привет, ᎣᏏᏲ"); 
print!("Hi, the answer is {} ",42);           // variables replace {}

p := struct { X, Y int }{ 17, 2 }
println!( "My point: {:?} x coord={}", p, p.X )  // {:} prints types that implement propr trait
let s = format!( "x coord={}", p.X )             // print to string 

println!(format!("%d hex:%x bin:%b fp:%f sci:%e",17,17,17,17.0,17.0)); // C-ish formatting
s2 := fmt.Sprintf( "%d %f", 17, 17.0 ) // another way to print to string

println!(" {:>4} {:>4} ", 232, 8 );   // pad as columns, width 4 spaces, align right

let mut s=String::new();              // build string, concatenate over lines 
s.push_str(&format!("{} {} ",1,2));
s.push_str(&format!("{} {} ",3,4));
println!("{}",s);
  
```
## If, conditionals, controls

```rust
if you_like_it() {
	put_a_ring();
} else if you_dont_like() {
	movin_on();
} else {
	therapy();
}
```

### loop, for, while
```rust
for i in 0..10 { print!("{},",x) };               // 0,1,2,3,4,5,6,7,8,9
for i in (0..10).rev() { print!("{} ",x) };       // 9,8,7,6,5,4,3,2,1,0
for i in (0..10).step_by(2) { print!("{} ",x) };  // 0,2,4,6,8

let v = vec![1, 35, 64, 36, 26];	// vector to iterate
for n in v { println!("{}",n) }		// ordinary vector iterate
for (i, n) in v.iter().enumerate() {	// iterate with index and item 
	println!("{},{} ", i, n);
}

done = false;                           // loop
loop { if done { break; } }		

let i = 10;				// while
while i > 0 {
	println("{}",i);
	i -= 2;
}
```

## Functions and closures
```rust
fn adder( a:i8, b:i8) -> i32 { b + a }  // 'return' keyword optional
fn multi_return( a:i8, b:i32) -> (char,i32) { return ('s',a+b); }
let (x, s) = multi_return( 3, 56 );   // multi return via tuples

fn addtwo(t:i8)->i8{t+2}; // simple function, adds 2 to argument. 
let fp = addtwo;          // fp = function pointer to addtwo function
fn f<F>(fp: F) where F: Fn(i8)->i8 { println!("{}",fp(1)) } 
// ^^^ define a function using 'where' to accept another function as argument
f(fp);  // call function f, passing a pointer to the 'adder' function. result=3

let c = |x| x + 2;    // define a closure, which is kinda like a lambda function 
f(c);                 // a closure can be passed, like a function pointer, result = 3
let value = 5;        // a closure however can also read values outside its scope
f(|x| x * value);     // and a closure can be anonymous, without a name. result = 5

Function with variable number of parameters: doesn't exist. 
Alternative: builder pattern, different named functions. 
https://www.reddit.com/r/rust/comments/4jgvho/idiomatic_way_to_implement_optional_arguments/
```

## References

Only one of these can be true:
* The program has one or more references (&T) to a resource,
* The program has exactly one mutable reference (&mut T).


### Math

```rust
use std::cmp
let a = cmp::max(5,3); // maximum value (non-floats only)
let b = -5;
let c = b.abs();       // absolute value 
let d = 42.3;
let e = d.round();     // round floating point
let f = d.sqrt();      // floating point square root approximation

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

##

Interface to other languages:

c++ - https://hsivonen.fi/modern-cpp-in-rust/

## Credits

/*
carols10cent's js-rust cheatsheet, https://gist.github.com/carols10cents/65f5744b9099eb1c3a6f
c0g https://stackoverflow.com/questions/29483365/what-is-the-syntax-for-a-multiline-string-literal
codngame https://www.codingame.com/playgrounds/365/getting-started-with-rust/primitive-data-types
rust-lang.org, rust book, https://doc.rust-lang.org/book/second-edition
rust-lang.org, rust reference, https://doc.rust-lang.org
Adam Leventhal, http://dtrace.org/blogs/ahl/2015/06/22/first-rust-program-pain/
Shepmaster https://stackoverflow.com/questions/33133882/fileopen-panics-when-file-doesnt-exist
*/

