
## Warning

This cheat sheet is in a reasonably useful state for basic things, but it does contain some errors.

## Rust in a Nutshell

* Syntax tokens similar to C
* Ownership of memory enforced at build time
* Statically linked
* Functional-ish, generic-ish, not so objecty
* Control flow using patterns, 'match' keyword
* Packages: 'cargo' command, https://crates.io
* Testing: cargo test, #[test]
* Concurrency: ownership, mutability, channels, mutex, crossbeam + Rayon packages
* Auto formatter: rustfmt filename.rs (see rust-lang.org for installation)
* compiler engine: LLVM
* raw pointers, low level: unsafe{} keyword
* A survivial horror game where griefing is ... oops wrong Rust

## Hello World

& see https://www.rust-lang.org for installation details

```rust
fn main() {
    println!("Hello World");
}
```

```bash
$ rustc main.rs   # creates 'main' executable
```

## Hello Packages, Hello Tests, Hello Dependencies

```bash
$ rustup.sh       # install rust, see rust-lang.org for details
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

## Mutability basic

```rust
let x = false;           // all variable bindings are immutable by default
x = true;                // compile error. can't change an immutable binding
let mut p = false;       // "mut" designates a binding as mutable
p = true;                // ok, mutable binding can change;
```

## types, variables, declarations, initialization
```rust
let x: bool = false; // let keyword
let k = false;       // rustc can determine some types
let y: char = '上';  // all chars are 4 bytes
let 上 = 5;          // error. identifiers must be ASCII characters 
let a: i8 = -2;      // 8 bit signed integers, also i16, i32, i64  
let b: u8 = 200;     // 8 bit unsigned integers, also u16, u32, u64 
let n: f32 = 0.45;   // 32 bit float (automatcally converted+rounded from decimal to binary)
let n = 42.01f64;  c // 64 bit float literal of the number 42.01 (approximately)
let r: [u8;3] = [3,4,5];          // array of 3 int, cannot grow
let s = [0;500];                  // array of 500 integers, each initialized to 0
let s = &r[0..2];                 // slice of array, s==&[3,4]
let s = &r[0..2][0];              // index into slice, s==3
let mut u:Vec<u8> = Vec::new();   // create empty vector of unsigned 8 bit int, can grow
let mut v = vec![3,4,5];          // initialize mutable vector using vec! macro
let w = vec![1,12,13];            // vectors can be immutable too
u.push( 2 );                      // append item to vector
u.pop();                      // vectors can return+remove last input (like a stack)
v.contains(&3);               // true if vector contains value
v.remove(1);                  // remove the nth item from a vector...
v.append(u);                  // append v with u (u becomes empty ([]), both mutable)
v.extend(w);                  // extend v with w (v owns w, w can be immutable)
v.resize(200,0);              // make vector have 200 elements, set them to 0
let x = &w[1..];               // get a slice of a vector (a view into it's elements)
print("{:?}",x);              // [12,13]; 
let vs = v.len();             // length of vector
let (p,d,q) = (4,5,6);        // tuple() can assign multiple variables at once
print("{}",p);                // you can use them alone after tuple assignment
let m = (4,5,"a");            // tuples can have multiple different types as elements
let (a,b) = m.1, m.3;         // tuple dereference with .1, .2, .3
let (a,b) = m.p, m.q;         // error, cannot index into a tuple using a variable

let s = String::from("上善若水"); // String
let s2 = "水善利萬物而不爭";       // string literal, type is &str
let s3 = format!("{}{}",s2,s3); // concatenate strings
for i in "말 한마디에 천냥 빚을 갚는다".split(" ") {print!("{}",i);} // split string
let s4 = &s[0..2];               // Substring using indexes
let hellomsg = r###"            // Multi-line with embedded quotes
 "Hello" in Chinese is 你好 ('Ni Hao')
 "Hello" in Hindi is नमस्ते ('Namaste')
"###;

usize, isize              // this is the pointer size. used in loops, vector length, etc

static FOOBY: i32 = 5;    // static, global-ish variable

type Valid = bool;        // typedef ( make your own type names ) 

let mut v = vec![1u8,2u8,3u8];  // determine the type of expression expr by looking at rustc error
println!("{}",v.iter_mut());
12 |     println!("{}",v.iter_mut());   // type of v.iter_mut() is std::slice::IterMut<'_, u8>`
   |                   ^^^^^^^^^^^^ `std::slice::IterMut<'_, u8>` 
                         cannot be formatted with the default formatter
```

## Operators

```rust
1 + 2 - 3 * 4 / 5  // arithmetic add, minus, multiply, divide
7 % 5              // modulo (remainder)
& | ^              // bitwise and, or, xor
<< >>              // leftshift, rightshift, will crash on overflow
a.rotate_left(3)   // circular bit rotation, wrapping around
a.rotate_right(4)  // circular bit rotation, wrapping around
!                  // bitwise not
a == b != c < d <= e > f >= g  // logical comparison
a && b || c ! d    // logical boolean, and, or, not

let a = 5;         // pointer + dereference example
let b = &a;        // &a is 'address of a' in computers memory
let c = *b;        // *b is contents of memory at address in b (dereference)
print("{}",c);     // 5

overloading: see struct
```

## Run time errors, Crashing, panic, except, unwrap, Option

```rust
panic!("oops");             // panic!() instantly crashes program
let v = vec![3,4,5];
let a = v[0];               // ok, normal lookup, a is 3
let b = v[12];              // will call panic! at runtime, v[12] doesn't exist
let c = v.get(12);          // this will not crash, it will instead create an Option
print!("{:?}",v.get(12));   // prints the word "None", Option can be None or Some
print!("{:?}",v.get(0));    // prints the word "Some(3)"
let e = v.get(0).unwrap();  // ok, 'unwrap' the Option returned by get(0), e is now 3
let d = v.get(12).unwrap(); // this crashes. 'unwrap' of a None Option will call panic!
let f = v.get(5).unwrap_or(&0); // unwrap_or gives a value if get() is None. f = 0

```

```bash
$ export RUST_BACKTRACE=1
$ cargo run    # will tell you exact line where panic occured, with call stack trace
```

## Printing

```rust
println!("Hello, 你好, नमस्ते, Привет, ᎣᏏᏲ"); 
print!("Hi, the answer is {} ",42);           // variables replace {}

let v = vec![1,2,3];
println!( "v[0] is {:?} {}", v, v[0] )             // {:?} can print lots of special types
let s = format!( "x coord={}", p.X )               // print to string 
s2 := fmt.Sprintf( "{e}", 17.0 )                   // another way to print to string
println!("hex:{:x} bin:{:b} sci:{:e}",17,17,17.0); // C-ish, hex:11 bin:10001 sci:1.7e1
println!("dec:{:#04} hex:{:#06x} bin:{:08b} sci:{:09e}",17,17,17,17.0); 
// dec:0017 hex:0x0011 bin:00010001 sci:00001.7e1
// (Same as line above, with 0 padding aka leading zeros ):
println!(" {:.40} ", 1.0f32/3.0f32 );  // print 40 digits of precision for floating point

println!(" {:>4} {:>4} ", 232, 8 );    // pad as columns, width 4 spaces, align right

let mut s=String::new();               // build string, concatenate over lines 
s.push_str(&format!("{} {} ",1,2));
s.push_str(&format!("{} {} ",3,4));
println!("{}",s);
  
#[derive(Debug)]                       // derive Debug is a simple way to make your own
struct W{x:i8}                         // structs printable
println!("{:?}",vec![W{x:4},W{x:3},W{x:2}]); // [W { x: 4 }, W { x: 3 }, W { x: 2 }]

// fmt::Display makes your own structs and enums printable with ordinary {} symbol
use std::fmt
impl fmt::Display for W{
fn fmt(&self, f: &mut fmt::Formatter)->fmt::Result{write!(f, "W[{}]", self.x)}}

pub enum Apple{PinkLady,HoneyCrisp}  // can also be done for Enums 
impl fmt::Display for Apple {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        match self { Apple::PinkLady=>write!(f, "Ap:PLad"),
                     Apple::HoneyCrisp=>write!(f, "Ap:HonCr"),}}}
```

### loop, for, while
```rust
for i in 0..10 { print!("{},",x) };        // 0,1,2,3,4,5,6,7,8,9
for i in 0..10.rev() { print!("{},",x) };  // 9,8,7,6,5,4,3,2,1,0
for i in (0..10).step_by(2)      ;         // 0 2 4 6 8 
for i in (0..10).skip(1).step_by(2);       // 1 3 5 7 9
for i in (0..10).rev().step_by(2);         // 9 7 5 3 1 
for i in (0..=10).rev().step_by(2);        // 10 8 6 4 2 0 
for i in (0..=10).step_by(2)     ;         // 0 2 4 6 8 10 
for i in (0..9).rev().step_by(2) ;         // 8 6 4 2 0 
for i in (0..9).step_by(2)       ;         // 0 2 4 6 8 
for i in (0..10).cycle().skip(5).take(10)  // 5 6 7 8 9 0 1 2 3 4 
let v = vec![1, 35, 64, 36, 26];	   // vector to iterate
for n in v { println!("{}",n) }		   // ordinary vector iterate
for (i, n) in v.iter().enumerate() {       // iterate with index and item 
	println!("{},{} ", i, n);
}

let mut i = 10;				// while
while i > 0 {
	println("{}",i);
	i -= 2;   // if i actually goes negative, it will panic, subtraction overflow
}

let mut i = 0;                          // loop
loop { i=i+1; if i<10 { break; } };	// plain loop
let x = loop { i=i+1; if i>=10 { break i; } } // loop that returns value, x = 10

// While Let, can be used in situations where we expect Option::Some() for several iterations,
// but we expect a Option::None() to be at the end of the iterations. For example:
let mut n = 99;                
while let Some(i) = (0..n).filter(|x|x%2==0).last() {n/=2;print!("{}",i);}  
// this prints the highest even number below n, as n is repeatedly divided in half
// 98, 48, 22, 10, 4, 2, 0  

```


## Concurrency, parallel processing

```rust
extern crate rayon;
use rayon::prelude::*;
fn main() {
    let mut v = Vec::new();  // create a vector of floats, to multiply each by 0.9
    for i in 0..1024*1280 { v.push(i as f32); }
    v.iter_mut().for_each(     |x| *x = *x * 0.9 ); // single thread version 
    v.par_iter_mut().for_each( |x| *x = *x * 0.9 ); // multiple threads version
    
    very_slow_function1(); // two single threaded functions that take a long time
    very_slow_function2(); 
    
    rayon::join( || very_slow_function1()    // run them in parallel if appropriate
    		 || very_slow_function2() );
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

```rust 
channels: todo
mutex: todo
ARC: todo
```

## Functions and closures
```rust
fn add( a:i8, b:i8 ) -> i32 { b + a }  // 'return' keyword optional
fn getcodes( a:i8, b:i32) -> (char,i32) { return ('s',a+b); }
let (x, s) = getcodes( 3, 56 );     // multi return via tuples
fn mulby6( a:i8, b:i8=5 ) -> i16 {} // error. Rust circa 2019 has no default parameters. 
fn f(t:i8) {          // nesting functions is OK
  fn g(u:i8) { u*5 }
  let a = t + g(2);
}

// function pointers
fn addtwo(t:i8)->i8{t+2}; // simple function, adds 2 to argument. 
println!("{}",addtwo(5)); // prints 7
let fp = addtwo;          // fp = function pointer to addtwo function
println!("{}",fp(5));     // now we can call fp() just like we called addtwo
fn f<F>(fp: F) where F: Fn(i8)->i8 { println!("{}",fp(1)) } 
// 'where F:Fn' lets us build a function that can accept another function as an argument
f(fp);  // call function f, passing a pointer to the 'addtwo' function. result=3

// closures
let c = |x| x + 2;    // define a closure, which is kinda like a lambda function 
fn f<F>(fp: F) where F: Fn(i8)->i8 { println!("{}",fp(1)) }  // f takes a function as an argument 
f(c);                 // a closure can be passed, like a function pointer, result = 3
let value = 5;        // a closure can also read values outside its scope
f(|x| x * value);     // and a closure can be anonymous, without a name. result = 5

for i in 0..4.filter(|x| x>1) // closures are used often with iterators (see below) 
print!("{} ",i)               // 2 3

type ZFillCallback = fn(bottom:u32,top:u32)->u32;  // typedef of a function

fn maximum(t:i8,...) {} // error, can't have variable number of arguments. see Macros! below

```

## Unit tests, integration tests

Unit tests, placed in the same file as the code being tested

```rust
./src/lib.rs:

pub fn process(v:&mut Vec<u8>)->&Vec<u8>{ v.update(|x| f(x)) } // main function called by users
fn f(x:u8)->u8 { x*x }   // small piece of our code, to test in unit testing

#[cfg(test)]        // cfg -> section will only compiled during 'cargo test'
mod tests {         // namespace helper
    use super::*;   // bring in our functions above
    #[test]         // next function will be a single test
    fn test_f() { assert!(f(4)==16); } // test f() by itself (unit)
}
```

Integration tests, for overall crate, lives under ./tests/*.rs

```rust
./tests/file.rs:         // will only be built dring 'cargo test'
extern crate mypackage;  // include package we are testing
#test                    // treat next function as a test
fn bigtest() {           // not a unit test. instead, test overall code
	let mut d = vec![1,2,3];               // set up some typical data users would have
	let expected_results = vec![1,4,9];    // some results we expect
	assert!(process(d)==expected_results); // test what a user would typically call, process()
}
```

```bash
$ cargo test           # test build, will include cfg(test) sections
-> test_f passed       # cargo reports on passed tests
-> test_bigtest failed # cargo reports on failed tests
```

## Documentation

rust-doc and cargo doc allow automatic building of html documentation
for code. precede documentation of your code with three slashmarks
instead of the normal two slashmarks, like so:

```rust
/// blorg() returns the blorgification of input x
/// # Details
/// this code implements the krishnamurthi procedure
/// for blimfication of zorgonautic primes 
/// # Arguments
/// * `x` - typically a square number
/// # Safety
/// Cannot panic unless x overflows u64
/// # Example
///     let n = blorg(36); 
fn blorg(x:u64)->u64 {
   x+x*x
}
```

Then run rust-doc or cargo doc and view the result.

```bash
$ cargo doc
$ firefox target/doc/cratename/index.html
```

Good examples of the results are on https://crates.io

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
	liked(boo) => put_a_ring(),    // => signifies a branch or leg of the match
	notliked(boo) => move_on(),    // have as many=> legs as you want
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


fn m(x:(i8,char))->{x.1=='a'};        // patterns can only be constant, but you can
let n = (0,'a');                      // use a conditional pattern, like this:
let y = match n {                     // match tuple
        (0,_) => "start 0",           // any tuple with first element 0, ok.
        (1,'a') if m(n) => "start 1", // this 'if' piece is called a 'match guard'
        _ => "stop",                  // match guard allows matching with variables.
};

let x = 5i32;                          // we can use the match value in match arms,
let y = match x-1 {                    // this is also called "binding with @", like so:
	0..=10 => x,                   // y becomes x
	m @ 10..=std::i32::MAX => m*2, // y becomes (x-1)*2
        _=>-x,                         
};


```


## Ownership, Borrowing, References, Lifetimes

Resources have exactly one owner. They can be 'moved' from one owner to another.
 
```rust
// stack memory, no moves, only copies
let a = 5;
let b = a;  // ok
let c = a;  // ok

// heap memory
let a = String::new(); 
let b = a;  // 'move' of ownership from a to b
let c = a;  // error. cannot "move" a again, b already owns it

// heap memory + function call
let a = String::new();
let b = a;  // 'move' of ownership from a to b
fn f(t:String) { } // function takes ownership of the variable passed as t
f(a); // error. f(a) would move a into f(), but a was already moved into b
```

Borrowing is an alternative to moving. It is done with References & (memory addresses)

```rust
// heap memory, using borrows and references instead of moves
let a = String::from("☪☯ॐ✡γ⚕✝");
let b = &a;  // this is borrowing, not moving, a to b
let c = &a;  // it is OK to have more than one borrower
println!("{}",a);    // ☪☯ॐ✡γ⚕✝
println!("{:p}",&a); // 0x7ffcffb6b278
println!("{:p}",b);  // 0x7ffcffb6b278  // b and c hold the address of a
println!("{:p}",c);  // 0x7ffcffb6b278
println!("{:p}",&b); // 0x7ffcffb6b290  // b and c are distinct variables
println!("{:p}",&c); // 0x7ffcffb6b298  // with their own addresses
```

However borrowing has special rules regarding mutability. 

In a block, only one of these statements can be true for a given resource R
* The program has one or more references to R
* The program has exactly one mutable reference to R 

```rust

// example error[E0502]: cannot borrow `a` as mutable because `a` is also borrowed as immutable
let mut a = 5;
let b = &a;
let c = &mut a;

// example error[E0499]: cannot borrow `a` as mutable more than once at a time
let mut a = 5;
let b = &mut a;
let c = &mut a;
```

Lifetimes: todo

Resources are destroyed, (their heap memory is freed), at the end of a 'scope'. Their owners are also destroyed. That is the point of ownership - so that resources won't be accessed after they are destroyed, which is the source of a huge number of errors in C programs.

Borrowed resources are not destroyed when the borrowed reference itself goes out of scope. However the borrow cannot "outlive" the destruction of the original resource nor it's owner.

https://medium.com/@jordan_98525/reference-iterators-in-rust-5603a51b5192

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
w.dump();    // ok , w is immutable, self inside dump() is immutable
w.okgrow();  // error, w is immutable, self inside okgrow() is mutable
             //  cannot borrow immutable local variable `w` as mutable
mw.dump();   // ok, mw is mutable, self inside dump is immutable.  
mw.okgrow(); // ok, mw is mutable, self inside grow() is mutable.

#[derive(Copy,Clone,Debug,PartialEq)]   // handy defaults
struct M{n:i8}
let (mut n,mut k)=(M{0},M{-1});
n=k;                // copy
vec![M{0};42];      // clone
println!("{:?}",n); // debug, formatter
if n==k {0};        // equality, operator overloading 

// customized operator overloading
impl PartialEq for Wheel{ fn eq(&self,o:&Wheel)->bool {self.r==o.r&&self.s==o.s} }
if mw == w { print!("equal wheels"); }

#[derive(Debug)]                       // Initialize one struct from another
struct Apple {color:(u8,u8,u8),price:f32};  
let a = Apple{color:(100,0,0),price:0.2};
let b = Apple{color:(9,12,38),..a };      // this is called "struct update"

#[derive(Default,Debug)]               // Initialization and Default
struct M {n:i8,y:i8,z:f64,s:String,p:bool,}
let m=M{..Default::default()};     // M { n: 0, y: 0, z: 0.0, s: "", p: false }
let m=M{n:1,..Default::default()}; // M { n: 1, y: 0, z: 0.0, s: "", p: false }

```

## Enums

```rust
enum ClipType { Intersection, Union, Difference, Xor }
let clipper = Clip( ClipType::Intersection );

```

## HashMap, aka associative array / key-value / map 
```rust
use std::collections::HashMap;
let mut m = HashMap::new();   
m.insert('a', 1);               // key is 'a', value is 1
let b = m['a'];                 // this crashes at runtime if 'a' is not in map
*m.get_mut('a').unwrap() += 2;  // changing value inside a map

```

There are no hashmap literals, but you can make your own macro [like Shepmaster, on StackOverflow, click here](https://stackoverflow.com/questions/27582739/how-do-i-create-a-hashmap-literal)

## Macros

Does not act like a preprocessor. It replaces items in the abstract syntax tree

```rust
macro_rules! hello {
    ($a:ident) => $a = 5;
}
hello!(a);    // a = 5

macro_rules! maximum {   
    ($x:expr) => ($x);
    ($x:expr, $($y:expr),+) => ( std::cmp::max($x, maximum!($($y),+)) )
}
maximum!(1,2,3,4);   // 4

macro_rules! dlog {
    ($loglevel:expr, $($s:expr),*) => (
        if DLOG_LEVEL>=$loglevel { println!($($s),+); }
    )
}
let DLOG_LEVEL=5;
dlog!(4,"program is running, dlog:{}",DLOG_LEVEL);  // "program is running, dlog:5"

/*
designators: 
block   // rust block, like {}.        expr    // expressions
ident   // variable/function names.    item    // 
pat     // pattern.                    path    // rust path
stmt    // statement.                  tt      // token tree
ty      // type.                       vis     // visibility qualifier
*/
```




## Arrays, Slices, Ranges

todo

## Split_At_Mut - Mutability and References into a Vector

For the special case for getting references to items within a vector:

Imagine we have three Wheels (struct W) with radius (W.r) and they are inside
a vector v. We start with wheels of radius 3,4,5 and want to change 
it to be radiuses of 2, 4, 8.

```

    #[derive(Debug)]
    struct W{ r:u32 }       // wheel struct
    let mut v = vec![W{r:3},W{r:4},W{r:5}];   // vector of structs
    let wheela = &mut v[0];      // mutable reference to Wheel with radius 3
    let wheelb = &mut v[2];      // compile error! two mutables for one variable!
    //  error[E0499]: cannot borrow `v` as mutable more than once at a time
    wheela.r = 2;  // nope
    wheelb.r = 8;  // nope
```
The borrow checker treats the entire vector as one gigantic variable, so
you can't edit part of it with a mutable reference and then edit another part,
because the parts aren't considered parts. They are considered as if you are
editing the variable v. 

But there is a workaround built into the language. It is called..

split_at_mut

It can create mutable slices, which allow mutable access to the vector.

```
     #[derive(Debug)]
    struct W{ r:u32 }       // wheel struct
    let mut v = vec![W{r:3},W{r:4},W{r:5}];   // vector of structs
    let (l, r) = v.split_at_mut(2);  // split after the wheel with r4
    let wheela = &mut l[0];  // first item of left part of split
    let wheelb = &mut r[0];  // first item of right part of split
    wheela.r = 2;       // no problem
    wheelb.r = 8;       // no problem
    println!("{:?} {:?}",l,r); // [W { r: 2 }, W { r: 4 }] [W { r: 8 }]
    println!("{:?}",v); // [W { r: 2 }, W { r: 4 }, W { r: 8 }]
```

## Object Oriented style

Inheritance - there is no inheritance. Typical alternatives are "composition" (struct within struct), Traits (sort of like Interfaces), and even Enums.

## Files

```rust
use std::fs::File;
use std::io::Read;

// read file, non-crashing example
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

// crash-on-error examples ( unwrap() calls panic! on error, crashes the program )
let mut s = String::new();
File::open("test.txt").unwrap().read_to_string(&mut s).unwrap();    // read into s
File::create("output.txt").unwrap().write(s.to_bytes()).unwrap();   // write string
f = File::create("output.txt").unwrap();                            // create if doesnt exist
f = OpenOptions::new().create(true).open("out.txt").unwrap();       // create if doesnt exist
f = OpenOptions::new().append(true).open("out.txt").unwrap();       // append
f = OpenOptions::new().append(true).create(true).open("out.txt").unwrap(); // append + create
write!(f,"{}",s).unwrap(); // write formatted string, given file object f

let mut v = vec![];                                                 // read binary data, vec of u8
f = File::open("test.bin").unwrap().read_to_end(&mut v);            // without using buffering

use std::io::{self,BufRead};  // read from stdin aka standard input
let line = io::stdin().lock().lines().next().unwrap().unwrap();

let x = include!("datafile.txt"); // include external data file, example: vec![0,0,0];

```

## System, arguments

```rust
std::env::args().for_each(|x| print!("{} ",x)); // main arguments as iterator, print each one 
for arg in std::env::args().collect::<Vec<String>>() { print!("{} ",arg); }; // same, as vector
if std::env::args().any(|x| x=="--help") {help()};            // if called with --help, run help()
```

packages: docopt, clap, getopts, structopt


## Reflection

todo

## Traits

todo. Traits are like 'interfaces' in other languages.

## Iterators, functional style programming

```rust
let v = vec![3,4,5];
let it = v.iter(); // iterator as object
println!("{:?} {:?} {:?}",it.next(),it.next(),it.next()); // consumed
println!("{:?}",it.next()); // None

let v = vec![3];
let mut i = v.iter().peekable();
println!("{:?}",i.peek());  // Some(3)
println!("{:?}",i.peek());  // Some(3)
println!("{:?}",i.next());  // 3
println!("{:?}",i.next());  // None

let mut i = v.iter();
print!("{:?}",i.collect::<Vec<_>>(); // iterator back to vector 

for i in v.iter() {print!("{} ",i);} // 3 4 5 
let biggest = v.iter().max();        // 5
let hasle2 = v.iter().any(|x| x<=4); // true if any element is less than or equal to 2
let biggest = v[0..1].iter().max();  // will return 4, not 5, b/c we took a slice of the vector
for i in v.iter().step_by(2) {print!("{} ",i);} // 3 5 vec![
for i in vec![3,4,5].iter().skip(1) {print("{}",i);} // 4, 5
for i in vec![3,4,5].iter().take(2) {print("{}",i);} // 3, 4
for i in vec![3,4,5].chain(vec![1,12,13]) {print!("{} ",i);} // 3 4 5 1 12 13
for i in vec![3,4,5].zip(vec![1,12,13]) {print!("{} ",i);} // (3,1) (4,12) (5,13)
print!("{:?}",vec![3,4,5].into_iter().map(|x| 2 * x).collect::<Vec<u8>>()); // 6 8 10
vec![3,4,5].iter().for_each(|x| print!("{} ",x)); // 3 4 5
for i in vec![3,4,5].iter().filter(|x| x<=4) {print!("{}",i);} // 3 4
for (i,n) in vec![3,4,5].iter().enumerate() {print!("{}:{} ",i,n);} // 0:3 1:4 2:5
for i in vec![4,5,3,3].iter().skip_while(|x| **x>=4) {print!("{},",i);} // 3,3
for i in vec![3,4,5,3].iter().skip_while(|x| **x>=4) {print!("{},",i);} // 3,4,5,3
for i in vec![4,5,3,3,9,6].iter().take_while(|x| **x>=4) {print!("{},",i);} // 4,5
for i in vec![3,4,5,3].iter().take_while(|x| **x>=4) {print!("{},",i);} //   (nothing) 
for i in vec![3,4,5].iter().scan(0,|a,&x| {*a=*a+x;Some(*a)}) {print!("{}",i)} // 3,7,12
print!("{}",vec![3,4,5].iter().fold(0, |a, x| a + x)); // 12
vec![3,4,5].iter().sum() // 3+4+5, 12
vec![3,4,5].iter().product() // 12*5, 60
println!("{}",vec![1.,2.,3.].iter().cloned().fold(std::f64::MIN, f64::max)); // max, 3
println!("{}",vec![1.,2.,3.].iter().cloned().fold(std::f64::MAX, f64::min)); // min, 1
print!("{:?}",vec![vec![3,4],vec![5,1]].iter().flatten().collect::<Vec<_>>()); // 3,4,5,1
for i in vec![Some(3),None,Some(4)].iter().fuse() {print!("{}",i);} // Some(3), None
let (a,b): (Vec<_>, Vec<_>) = vec![3,4,5].into_iter().partition(|x| x>&4);
println!("{:?} {:?}",a,b); // [5] [3,4]
vec![3,4,5].iter().all(|x| x>2) // true
vec![3,4,5].iter().all(|x| x>4) // false
vec![3,4,5].iter().any(|x| x<4) // true
vec![3,4,5].iter().any(|x| x<2) // false
vec![3,4,5,3].iter().find(|&&x| x == 3) // Some(&3), first three
vec![3,4,5].iter().position(|&&x| x == 5) // 2 // kind of like indexOf() in other langs

let v =[3,4,5];
let (i,j)=(v.iter(),v.iter().cycle().skip(2)); // cycle allows wraparound
i.zip(j).for_each(|x| println!("{:?}",x));  // (3, 5)  (4, 3)  (5, 4)

cloned() // clones each element
unzip() // backwards of zip()
rev() // reverse iterator
rposition() // combine reverse and position
max_by_key() // max using single func on each item
max_by()    // max using compare closure |x,y| f(x,y)
v.min_by(|a,b| a.x.partial_cmp(&b.x).unwrap_or(std::cmp::Ordering::Equal)); //min val,float
find_map() // combine find and map 
flat_map() // combine flatten and map 
filter_map() // combine filter and map

// comparisons: cmp, le, eq, ne, lt, etc
print!("{}",vec![3,4,5].iter().eq(vec![1,3,4,5].iter().skip(1))); // true

```



Itertools library: more adapters
```rust
    use itertools::Itertools;
    vec![13,1,12].into_iter().sorted(); // [1,12,13]
    vec![(3,13),(4,1),(5,12)].into_iter().sorted_by(|x,y| Ord::cmp(&x.1,&y.1)); // [(4,1),(5,12),(3,13)]            
    "四四".chars().join("十")); // 四十四
    (1..4).interleave(6..9).collect_vec(); // 1,6,2,7,3,8
    " 十是十 ".chars().intersperse('四').collect::<String>(); // "四十四是四十四"
    "az".chars().merge("lm".chars()).collect::<String>(); // "almz"
    "za".chars().merge_by("ml".chars(),|x,y| x>y ).collect::<String>(); // "zmla"
    vec!["az".chars(),"lm".chars()].into_iter().kmerge().collect::<String>(); //"almz"
    "ab".chars().cartesian_product("cd".chars()).collect_vec() ; // [(a,b),(a,d),(b,c),(b,d)]
    "bananas".chars().coalesce(|x,y| if x=='a'{Ok('z')}else{Err((x,y))}).collect::<String>(); // bzzz
    "Mississippi".chars().dedup().collect::<String>(); // Misisipi
    "agcatcagcta".chars().unique().collect::<String>(); // agct
    (1..=3).combinations(2).collect_vec(); // [[1,2], [1,3], [2,3]]
    "愛".chars().pad_using(4,|_| '.').collect::<String>(); // "愛..."
    Itertools::flatten( vec![vec![12,1,13], vec![4,5,3]].iter() ).collect_vec(); // [12,1,13,4,5,3]
    "Go raibh maith agat".chars().positions(|c| c=='a').collect_vec();// [4, 10, 15, 17]
    vec![1,2,3].into_iter().update(|n| *n *= *n).collect_vec(); // [1,4,9]
    ("四十四是四十四".chars().all_equal(), "謝謝".chars().all_equal()); // (false, true)
    vec![1,2,3].iter().foreach(|n| {print!("{},",n)}; // 1,2,3
    vec![vec![12,1,13], vec![4,5,3]].into_iter().concat() ; // [12,1,13,4,5,3]
    let mut s=['.';3];s.iter_mut().set_from("abcdefgh".chars()); s.iter().collect::<String>(); // "abc"
    (1..4).fold1(|x,y| x*y).unwrap_or(0)); // 6   // 6 is 1*2*3
    [1,5,10].iter().minmax().into_option().unwrap() // (1,10) // faster than min() then max()
    (0..3).zip_eq("abc".chars()).collect_vec(); // [(0,'a'), (1,'b'), (2,'c')]
```

## Implementing your own iterator for your own struct

If you want to be able to use all the cool adapters like filter(), map(), fold(), etc, 
on your own cusom data structure you will need to implement the Iterator trait for your 
struct. Basically you need to create another struct called a "XIterator" that implements 
a 'next()' method. Then you create a method in your own struct called 'iter()' that returns 
a brand new XIterator struct.

Here is a super simple example, all it does is iterate over a data inside of custom 
data struct Mine. The data is stored in a vector so it's pretty straightforward to access it.


```rust

#[derive(Debug)]
struct Mine{
    v:Vec<u8>
}

impl Mine{
    fn iter(self:&Mine) -> MineIterator {
        MineIterator {
            m:&self, count:0,
        }
    }
}

struct MineIterator<'a> {
    m: &'a Mine,
    count: usize,
}

impl<'a> Iterator for MineIterator<'a> {
    type Item = &'a u8;
    fn next(&mut self) -> Option<Self::Item> {
        self.count+=1;
        if self.count<=self.m.v.len() {
		Some(&self.m.v[self.count-1])
	} else {
		None
	}
    }
}

fn main() {
    let m=Mine{v:vec![3,4,5]};
    m.iter().for_each(|i| print!("{:?} ",i));println!(""); // 3 4 5
    m.iter().filter(|x|x>3).fold(0,|a,x| a+x); // 9 
}

```

If you want to create a Mutable Iterator, you will (as of writing, 2018)
have to use an unsafe code with a raw pointer. This is alot like what the
standard library does. If you are not extremely careful, your program may
exhibit bizarre behavior and have security bugs, which defeats the purpose
of using Rust in the first place. 

* https://gitlab.com/Boiethios/blog/snippets/1742789
* https://doc.rust-lang.org/book/ch19-01-unsafe-rust.html#dereferencing-a-raw-pointer

```rust


impl Mine{
    fn iter_mut(self:&mut Mine) -> MineIterMut {
            MineIterMut {
            m:self, count:0,//_marker:std::marker::PhantomData,
        }
    }
}

pub struct MineIterMut<'a> {
    m: &'a mut Mine,
    count: usize,
}

impl<'a> Iterator for MineIterMut<'a> {
    type Item = &'a mut u8;
    fn next(&mut self) -> Option<&'a mut u8> {
            if self.count == self.m.v.len() {
                None
            } else {
                let ptr: *mut u8 = &mut self.m.v[self.count];	    
                self.count += 1;
		unsafe{ Some(&mut *ptr) }
            }
    }
}

fn main() {
    let mut m=Mine{v:vec![3,4,5]};
    m.iter().for_each(|i| print!("{:?} ",i));println!(""); // 3 4 5
    m.iter_mut().for_each(|i| *i += 1);
    m.iter().for_each(|i| print!("{:?} ",i));println!(""); // 4 5 6
}
```

## Math

### arithmetic

```rust

let x=250u8;           // addition can crash, max value of u8 is 255
println!("{}",x+10);   // crashes, attempt to add with overflow
println!("{}",x.wrapping_add(10));   // wraps around, result: 5
println!("{}",x.saturating_add(10)); // stays at max, result: 255
println!("{}",x.wrapping_sub(u8::MAX)); // wraparound subtraction
std::f32::MIN;         // minimum binary floating point 32 bit value
std::i32::MAX;         // maximum 32 bit integer value

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

### number conversion

```rust
let numx: u16 = 42;       // casting, start with 16 bit unsigned integer
let numy: u8 = x as u8;   // cast to unsigned 8 bit integer
let numz: i32 = x as i32; // cast to signed 32 bit integer

let s = format!("{:e}",0.0f32);    // convert float32 to base-10 decimal string (scientific format)
let n = s.parse::<f32>().unwrap(); // parse float from string, panic/crash if theres an error
let mut n = 0.0f32;                // parse float from string, without panic/crashing
match s.parse::<f32>() {
	Err(e)=>println!("bad parse of {}, because {}",s,e),
	Ok(x)=>n=x
}
let a = "537629.886026485"                            // base-10 decimal string to binary float pt
print!("{:.50}",a.to_string().parse::<f32>().unwrap();// 537629.875000000000000000000000000000000 
let b = 537629.886026485;    print!("{:.50}",b);      // 537629.886026485008187592029571533203125
let c = 537629.886026485f32; print!("{:.50}",c);      // 537629.875000000000000000000000000000000
let d = 537629.886026485f64; print!("{:.50}",d);      // 537629.886026485008187592029571533203125

let x = '1'.to_digit(10);  // convert from char to integer, base 10
let y = 'f'.to_digit(16);  // convert from char to integer, base 16
let z = std::char::from_digit(x,10).unwrap(); // convert from integer to char, base 10. z is now '1'

println!("{:?}","abc".as_bytes()); // [97, 98, 99] ( utf8 string into bytes. note basic ASCII = utf8 )
println!("{:?}","ᏣᎳᎩ".as_bytes()); // [225, 143, 163, 225, 142, 179, 225, 142, 169] ( Cherokee utf8 )
let s = b"\x61\x62\x63"; // b precedes sequence of bytes, hexadecimal, pseudo-string form
println!("{}",std::str::from_utf8( s ).unwrap()); // abc // decodes utf8 to string, crash if invalid utf8

println!("{:?}", "सूर्य नमस्कार्कार".as_bytes()); // [224, 164, 184,...] 
let s = [224, 164, 184, 224, 165, 130, 224, 164, 176, 224, 165, 
         141, 224, 164, 175, 32, 224, 164, 168, 224, 164, 174, 224, 
	 164, 184, 224, 165, 141, 224, 164, 149, 224, 164, 190, 224, 164, 176];
println!("{}",std::str::from_utf8( &s ).unwrap()); // सूर्य नमस्कार   
println!("{:?}",std::str::from_utf8( &s ).unwrap()); // "स\u{942}र\u{94d}य नमस\u{94d}कार" different decoding

let m = 0b0001; // binary literal
let m = 0o0007; // octal literal
let m = 0x000f; // hexadecimal literal
let (a,b,c) = (0b00_01,0o00_07,0x00_0f); // literals with underscores for ease of reading

use std::i64;
let z = i64::from_str_radix("0x1f".trim_left_matches("0x"), 16).unwrap(); // hex string to integer

println!("{:x}",0x12345678u32.swap_bytes());  // 0x78563412 32-bit byteswap 

// using byteorder crate
extern crate byteorder; // modify your Cargo.toml to add byteorder crate. then:
use byteorder::{BigEndian, ReadBytesExt, NativeEndian, ByteOrder, LittleEndian, WriteBytesExt};
let arr = [0,1,2,3];
let s = NativeEndian::read_u32(&arr[0..4]);   // array of four bytes into u32.

// converting raw vectors of u8 bytes into ints, floats, etc.
let v = vec![0u8;80]; let i = 0;
let n:i32 = {unsafe { std::mem::transmute::<&[u8],&[i32]>(&v[i..i+4])}}[0]; 
let x:f32 = {unsafe { std::mem::transmute::<&[u8],&[f32]>(&v[i..i+4])}}[0];
let mut block = vec![0u8;64];  // convert entire block at once
let mut X = unsafe { mem::transmute::<&mut [u8], &mut [u32]>(&mut block) }; 
#[cfg(target_endian = "big")]   // deal with endian issues if needed
for j in 0..16 { X[j] = X[j].swap_bytes(); }
```

### string conversion

```rust
let s = "hello" ;                   // s = &str
let s = "hello".to_string();        // s = String
let m = s.replace("hello","new");   // m = "new"
let y = s.to_uppercase();           // y = "NEW"
```

Regular expressions typically use an external crate.
Syntax for regex: https://docs.rs/regex/1.1.2/regex/index.html#syntax

In cargo.toml,
[dependencies]
regex = "1.1.0"

In main.rs:
```rust
use regex::Regex;
let text = r###"The country is named France, I think the capital city is Paris;
the Boulangerie are beautiful on la rue du Cherche-Midi"###;              // multiline string

let re = Regex::new(r"country is named (?P<country>.*?),.*?city is (?P<capcity>.*?);").unwrap();
let caps = re.captures(text).unwrap();
println!("{}, {}",&caps["capcity"],&caps["country"]);                  // Paris, France

let re = Regex::new(r"(?ms)city is (?P<citname>.*?).$.*?beautiful on (?P<streetname>.*?)$").unwrap();
let caps = re.captures(text).unwrap();
println!("{}, {}",&caps["streetname"],&caps["citname"]);               // la Rue de Cherche Midi, Paris
```


### comparison and sorting

```rust

use std::cmp           			// max/min of values
let a = cmp::max(5,3); 			// maximum value of 2 integers
let b = cmp::max(5.0,3.0); 		// build error, floats cant compare b/c of NaN,Inf
let v = vec![1,2,3,4,5];   		// list, to find max of
let m = v.iter.max().unwrap(); 		// max of numbers in a list, crash on error
match v.iter().max() { 			// max of numbers in a list, don't crash
    Some(n)=>println!("max {}",n), 	// do stuff with max value n
    None=>println!("vector was empty")
}
let m = v.iter.max().unwrap_or(0);      // max of numbers in a list, or 0 if list empty

v = vec![1,3,2];                               
v.sort();                                      // sort integers
v = vec![1.0,3.0,2.0];                         // sort floats
v.sort();                                      // error, float's NaN can't be compared
v.sort_by(|a, b| b.cmp(a));                    // sort integers using closure
v.sort_by(|a, b| a.partial_cmp(b).unwrap());   // sort floating point using closure
v.min_by(|a,b| a.x.partial_cmp(&b.x).unwrap_or(std::cmp::Ordering::Equal)); // minimum value
println!("{}",vec![1.,2.,3.].iter().cloned().fold(std::f64::MIN, f64::max)); // 3
println!("{}",vec![1.,2.,3.].iter().cloned().fold(std::f64::MAX, f64::min)); // 1

struct Wheel{ r:i8, s:i8};                     // sort a vector that holds a struct
let mut v = vec![Wheel{r:1,s:2},Wheel{r:3,s:2},Wheel{r:-2,s:2}];
v.sort_by(|a, b| a.r.cmp(&b.r));               // sort using closure, based on value of field 'r'
v.sort_by(|a, b| a.r.cmp(&(&b.r.abs())));      // sort using closure, based on abs value of r

fn compare_s( a:&Wheel, b:&Wheel ) -> std::cmp::Ordering {      // sort using a function
     a.s.partial_cmp(&b.s).unwrap_or(std::cmp::Ordering::Equal)
}
v.sort_by( compare_s );                        
```

### Pseudo randoms

```rust
extern crate rand;   /// use external crate
use rand::Rng;
let x = rand::thread_rng().gen_range(0, 100); 

// alternative, without needing external crate
// Jack @ https://stackoverflow.com/questions/3062746/special-simple-random-number-generator
let mut seed = 123456789;
let mut r=||{seed=(1103515245u32.wrapping_mul(seed).wrapping_add(12345)) % 2147483648;seed};
for i in 0..99 {print!("{} ",r());}; // print 99 pseudo random integers  
```

### Hashing

```rust
use std::collections::hash_map::DefaultHasher; 
use std::hash::{Hash, Hasher};
let mut hasher = DefaultHasher::new(); // hashing, via a Hasher object, which holds state
let x = 1729u32;
let y = 137u32;
hasher.write_u32( x );                 // input x to hash func, store output in hasher 
hasher.write_u32( y );                 // input y, combine w existing state, store in hasher
println!("{:x}", hasher.finish());     // .finish() function Hasher gives current state
345.hash(&mut hasher);                 // update hasher's state using '.hash()' trait
println!("{:x}", hasher.finish());     // .finish() does not 'reset' hasher objects

```

### Time

```
use std::time::{Instant};
let t = Instant::now();
// do something for 5.3 seonds
println!("{}",t.elapsed().as_secs());          // 5, seconds, rounded
println!("{}",t.elapsed().subsec_millis());    // 300. remainder in milliseconds

```

## Errors, Result, Option, Exceptions

Rust has no Exceptions of the type found in other languages like Java or C++. It favors special return types called Result and Option. Here is an example with Result:

```rust
fn calculate_blorg_level(some_data: &[u8]) -> Result<u8, &'static str> {
    match some_data[0] {
    	255=>Err("we cannot calculate blorg if data begins with 255"),
        _=>Ok((some_data[0]+some_data[1]*17).rotate_right(3)),
    }
}
```

Note our Result can return two different things, a u8 or a str, but the u8 will be wrapped inside Ok() while the str would
be wrapped inside an Err(). Not 'catching' a Result when you call calcualte_blorg_level() will show an error at compile time.



## Annotations

Aka hashtags aka warning thingies. These statements typically affect compilation and begin with a hashtag, and square brackets. They are sort of like a mix of #pragma and #ifdef from C. 

```rust
#![allow(dead_code)]             // stop compiler from printing warnings on unused funcs/vars,   
#![allow(unused_variables)]      // this is good when you are working on a library

let mut a = 0x00ffee22;          // modify numbers for big endian machines.
#[cfg(target_endian = "big")]    // target = machine the code will be run on
a = a.swap_bytes();              // the line (or block) immediately following the # gets affected.
```

## Linked Lists

Linked Lists are different in Rust because textbook C/Java-style implementations often involve ownership that is not allowed by the borrow checker. However it can be accomplished. There is builting "LinkedList" type in std::collections, also some resources from A. Beinges :

https://cglab.ca/~abeinges/blah/too-many-lists/book/

You can also implement a linked list as a Vector of structs, using integer indexes into the vector instead of pointers.

## FFI, Calling C functions

foreign function interface, aka calling code from other languages. 

layout
```bash
   src/lib.rs
   src/ccode.c
   build.rs
   Cargo.toml
```

Cargo.toml
```rust
[package]
name = "duh"
version = "0.1.0"
authors = ["akhmatova"]
build = "build.rs"
[build-dependencies]
cc = "1.0"
```

build.rs
```rust
extern crate cc;

fn main() {
    cc::Build::new().file("src/ccode.c").compile("ccode");
}
```

src/ccode.c
```C
int quadrance( int x1, int y1, int x2, int y2) {
	return (x1-x2)*(x1-x2)+(y1-y2)*(y1-y2);
}
```

src/main.rs
```rust
use std::os::raw::c_int;
extern "C" {
    fn quadrance(x1: c_int, y1: c_int, x2: c_int, y2: c_int) -> c_int;
}
fn main() {
    unsafe {
        println!("4^2+3^2={:?}", quadrance(0, 0, 4, 3));
    }
}
```

Run:
```bash
don@oysters:~/duh$ cargo run
   Compiling duh v0.1.0 (/home/don/duh)                                         
    Finished dev [unoptimized + debuginfo] target(s) in 1.95s                   
     Running `target/debug/duh`
4^2+3^2=25
```

See also: https://s3.amazonaws.com/temp.michaelfbryan.com/index.html

c++ - https://hsivonen.fi/modern-cpp-in-rust/

CVarArgs is a special Rust type to help with C


## Metacritic rating

Rust has an excellent crafting and building system. The character models are a bit wide, and combat is lacking. Multiplayer is a bit twiddly, as the central server can be hard to find a match on. There is a steep difficulty level for beginners. These factors make Rust a cult favorite but not a megahit. 7.3/10. 

## Thanks

Based on a8m's go-lang-cheat-sheet, https://github.com/a8m/go-lang-cheat-sheet, and

- rust-lang.org, Rust book, https://doc.rust-lang.org/book/second-edition
- rust-lang.org, Rust reference, https://doc.rust-lang.org
- rust-lang.org, Rust by example, https://doc.rust-lang.org/rust-by-example/
- rust playground, from integer32, https://play.integer32.com/
- Phil Opp builds an OS in rust, https://os.phil-opp.com/unit-testing/
- Rust Cookbook, Language Nursery https://rust-lang-nursery.github.io/rust-cookbook/algorithms/sorting.html
- Itertools docs https://docs.rs/itertools/*/itertools/trait.Itertools.html for more details
- carols10cent's js-rust cheatsheet, https://gist.github.com/carols10cents/65f5744b9099eb1c3a6f
- c0g https://stackoverflow.com/questions/29483365/what-is-the-syntax-for-a-multiline-string-literal
- codngame https://www.codingame.com/playgrounds/365/getting-started-with-rust/primitive-data-types
- Adam Leventhal post here, http://dtrace.org/blogs/ahl/2015/06/22/first-rust-program-pain/
- Shepmaster, https://stackoverflow.com/questions/33133882/fileopen-panics-when-file-doesnt-exist
- again, https://stackoverflow.com/questions/32381414/converting-a-hexadecimal-string-to-a-decimal-integer
- user4815162342, https://stackoverflow.com/questions/26836488/how-to-sort-a-vector-in-rust
- mbrubek, https://www.reddit.com/r/rust/comments/3fg0xr/how_do_i_find_the_max_value_in_a_vecf64/
- https://stackoverflow.com/questions/19671845/how-can-i-generate-a-random-number-within-a-range-in-rust
- Amir Shrestha https://amirkoblog.wordpress.com/2018/07/05/calling-native-c-code-from-rust/ 
- Julia Evans https://jvns.ca/blog/2016/01/18/calling-c-from-rust/
- huon https://stackoverflow.com/questions/23850486/how-do-i-convert-a-string-into-a-vector-of-bytes-in-rust
- EvilTak https://stackoverflow.com/questions/43176841/how-to-access-the-element-at-variable-index-of-a-tuple
- https://www.90daykorean.com/korean-proverbs-sayings/
- oli_obk https://stackoverflow.com/questions/30186037/how-can-i-read-a-single-line-from-stdin
- ogeon https://users.rust-lang.org/t/how-to-get-a-substring-of-a-string/1351
- A.R https://stackoverflow.com/questions/54472982/converting-a-vector-of-integers-to-and-from-bytes-in-rust
- dten https://stackoverflow.com/questions/25060583/what-is-the-preferred-way-to-byte-swap-values-in-rust
- How To Rust-doc https://brson.github.io/2012/04/14/how-to-rustdoc
- Pavel Strakhov https://stackoverflow.com/questions/40030551/how-to-decode-and-encode-a-float-in-rust
- Zargony https://stackoverflow.com/questions/19650265/is-there-a-faster-shorter-way-to-initialize-variables-in-a-rust-struct/19653453#19653453
