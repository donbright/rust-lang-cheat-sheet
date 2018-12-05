
## WARNING very unfinished

very raw, has not been quality checkedm , contains errrors

Based on a8m's go-lang-cheat-sheet, github.com/a8m/go-lang-cheat-sheet


## Rust in a Nutshell

* A survivial horror game where griefing is ... oops wrong Rust
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

usize, isize              // this is the pointer size. used in loops, vector length, etc

let numx: u16 = 42;       // casting, start with 16 bit unsigned integer
let numy: u8 = x as u8;   // cast to unsigned 8 bit integer
let numz: i32 = x as i32; // cast to signed 32 bit integer

static FOOBY: i32 = 5;    // static, global-ish variable

let _ = expr; // determine the type of expression expr by looking at rustc error

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
let b = &a;        // &a is 'address of a' in computers memory
let c = *b;        // *b is contents of memory at address in b (dereference)
print("{}",c);     // 5


```

## Run time errors, Crashing, panic, except, unwrap, Option

```rust
panic!("oops");             // panic!() instantly crashes program
let v = vec![3,4,5];
let a = v[0];               // ok, normal lookup, a is 3
let b = v[12];              // will call panic! at runtime, v[12] doesn't exist
let c = v.get(12);          // this will not crash, it will instead create an Option
print!("{:?}",v.get(12));   // prints the word "None", Option can be None or Some
print!("{:?}",v.get(0));    // prints the word "Some(2)"
let e = v.get(0).unwrap();  // ok, 'unwrap' the Option returned by get(0), e is now 3
let d = v.get(12).unwrap(); // this crashes. 'unwrap' of a None option will call panic!

```

```bash
$ export RUST_BACKTRACE=1
$ cargo run    # will tell you exact line where panic occured, with call stack trace
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


## HashMap, aka associative array / key-value / map 
```rust
use std::collections::HashMap;
let mut m = HashMap::new();   
m.insert('a', 1);               // key is 'a', value is 1
let b = m['a'];                 // this crashes at runtime if 'a' is not in map
*m.get_mut('a').unwrap() += 2;  // changing value inside a map

```

There are no hashmap literals, but you can make your own macro [ike Shepmaster, on StackOverflow, click this link](https://stackoverflow.com/questions/27582739/how-do-i-create-a-hashmap-literal)

## Ownership

Resources have exactly one owner. They can be 'moved' from one owner to another.

Resources are destroyed, (their heap memory is freed), at the end of a 'scope'

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

fn f(t:i8) {          // nesting functions is OK
  fn g(u:i8) { u*5 }
  let a = t + g(2);
}

// function pointers
fn addtwo(t:i8)->i8{t+2}; // simple function, adds 2 to argument. 
let fp = addtwo;          // fp = function pointer to addtwo function
fn f<F>(fp: F) where F: Fn(i8)->i8 { println!("{}",fp(1)) } 
// 'where F:Fn' lets us build a function that can accept another function as an argument
f(fp);  // call function f, passing a pointer to the 'adder' function. result=3

// closures
let c = |x| x + 2;    // define a closure, which is kinda like a lambda function 
f(c);                 // a closure can be passed, like a function pointer, result = 3
let value = 5;        // a closure can also read values outside its scope
f(|x| x * value);     // and a closure can be anonymous, without a name. result = 5

fn maximum(t:i8,...) {} // error, can't have variable number of arguments. see Macros! below

```

## If, conditionals, patterns, match, control flow

```rust
if you_like_it() {                   // normal if else, like C, Pascal, etc
	put_a_ring();
} else if you_dont_like() {
	movin_on();
} else {
	therapy();
}

match boo {                            // match a variable
	if liked(boo) => put_a_ring(), // => signifies a branch or leg of the match
	if notliked(boo) => move_on(), // have as many => legs as you want
	_ => therapy();                // _ will match anything not previously matched
}

let x = [1i8,2i8];                    // match patterns can be structs, enums, arrays, etc
let y = match x {                     // match can 'return a result' to y
        [1,0] => "1,0",               // match a specific array
        [2,0]|[4,0] => "2,0 or 4,0",  // |, binary or, can be used in patterns
        [_,2] => "ends with 2",       // [_,2] will match [0,2] [1,2] [2,2], [3,2], etc
        _ => "other"
};
println!("{}",y);                     // "ends with 2"
```

## Macros

Does not act like a preprocessor. It reaplces items in the abstract syntax tree

```rust


macro_rules! maximum {   
    ($x:expr) => ($x);
    ($x:expr, $($y:expr),+) => ( std::cmp::max($x, maximum!($($y),+)) )
}
maximum(1,2,3,4);   // 4

```

## References

In a block, only one of these statements can be true for a given resource R
* The program has one or more references to R
* The program has exactly one mutable reference to R 


### Math

```rust
use std::cmp
let a = cmp::max(5,3); // maximum value (non-floats only)
let b = -5;
let c = b.abs();       // error, abs not defined on generic integer
let b = -5i32;
let c = b.abs();       // ok, abs is defined on i32, 32 bit integer
42.25f32.round();      // can also call functions on float literals    
9.0f32.sqrt();         // square root approximation
9.sqrt()               // error, sqrt only for floats
let x = 3/4;           // 0
let y = 3.0/4;         // error, no implementation for `{float} / {integer}`
let z = 3.0/4.0;       // 0.75
```


## Arrays, Slices, Ranges

todo

### Slices

todo, create, concatenate, cut, strings,

x := [3]string{"Лайка", "Белка", "Стрелка"}


## subclass, inheritance

todo

## Concurrency, parallel processing

```rust
extern crate rayon;
use rayon::prelude::*;
fn main() {
    let mut v = Vec::new();  // create a vector of floats, to multiply each by 0.9
    for i in 0..1024*1280 { v.push(i as f32); }
    v.iter_mut().for_each(     |x| *x = *x * 0.9 ); // single thread version 
    v.par_iter_mut().for_each( |x| *x = *x * 0.9 ); // multiple threads version
}
```

```bash
$ $EDITOR Cargo.toml  # add rayon dependency
[package]
name = "beatrixpotter"
version = "0.1.1"
authors = ["flopsy mopsy <ra@bb.it>"]
[dependencies]
rayon = "1.0.0"

$ cargo run          # installs rayon, runs program
```

### Files

```rust
use std::fs::File;
use std::io::Read;
match File::open("test.txt") {
	Err(why) => println!("failed to open file '{}': {}", filename, why),
        Ok(mut f) => {
        	println!("ok, opened {}",filename);
                let mut data = String::new();
                match f.read_to_string( &mut data ) {
                	Err(why) => println!("failed to read {}, {}",filename,why),
                        Ok(n) => println!("read {} bytes",n),
                };
	},
}
```



## Reflection

todo

## Interface to other languages, FFI

c++ - https://hsivonen.fi/modern-cpp-in-rust/

## Metacritic rating

Rust has an excellent crafting and building system. The character models are a bit wide, and combat is lacking. Multiplayer is a bit twiddly, as the central server can be hard to find a match on. There is a steep difficulty level for beginners. These factors make Rust a cult favorite but not a megahit. 7.3/10. 

## Credits

- carols10cent's js-rust cheatsheet, https://gist.github.com/carols10cents/65f5744b9099eb1c3a6f
- c0g https://stackoverflow.com/questions/29483365/what-is-the-syntax-for-a-multiline-string-literal
- codngame https://www.codingame.com/playgrounds/365/getting-started-with-rust/primitive-data-types
- rust-lang.org, Rust book, https://doc.rust-lang.org/book/second-edition
- rust-lang.org, Rust reference, https://doc.rust-lang.org
- rust-lang.org, Rust by example, https://doc.rust-lang.org/rust-by-example/
- Adam Leventhal post here, http://dtrace.org/blogs/ahl/2015/06/22/first-rust-program-pain/
- Shepmaster posts, https://stackoverflow.com/questions/33133882/fileopen-panics-when-file-doesnt-exist

