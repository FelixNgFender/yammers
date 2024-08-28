---
title: "TL;DR: The Rust Programming Language"
description: "Learning notes from The Rust Programming Language book"
publishDate: "24 Jun 2024"
coverImage:
  src: "./rust-book.png"
  alt: "The Rust Programming Language"
ogImage: "./rust-book.png"
tags: ["books", "systems", "learning-notes", "rust", "tl;dr"]
---

# The Rust Programming Language

## 4. Understanding Ownership

### 4.1. What is Ownership?

#### Ownership Rules

1. Each value in Rust has an _owner_.
2. There can only be one owner at a time.
3. When the owner goes out of scope, the value will be dropped.

#### Memory and Allocation

For variables on the heap (size unknown at compile time), we need to allocate an amount of memory on the heap to hold the contents. This means:

- The memory must be requested from the memory allocator at runtime.
- We need a way of returning this memory to the allocator when we're done with our heap variable.

For `String` type:

- `String::from`'s implementation requests the memory it needs
- When a `String` type variable goes out of scope, Rust calls the special function `drop` for us.
  - It's where the author of `String` can put the code to return the memory.
  - Rust calls `drop` automatically at the closing curly bracket.
  - This pattern is also called _Resource Acquisition is Initialization (RAII)_ in C++.

#### Variables and Data Interacting with Move

When you reassign variables on the heap, Rust makes a shallow copy of the first variable (copying the pointer, length, and capacity without copying the data) and invalidates it. This is known as a _move_.

Only valid variables get free'd when they go out of scope, effectively eliminating the double free problem.

Rust's design choice of shallow copying means that any _automatic_ copying can be assumed to be inexpensive in terms of runtime performance.

#### Variables and Data Interacting with Clone

Use the common method `clone` to deeply copy the heap data. This is may be expensive.

#### Stack-Only Data: Copy

Types such as integers that have a known size at compile time are stored entirely on the stack, so copies of the actual values are quick to make. So it's all deep copying for these types.

Rust has the `Copy` trait that we can place on types that are stored on the stack. If a type implements the `Copy` trait, variables that use it do not move, but rather are trivially copied, making them still valid after assignment to another variable.

#### Ownership and Functions

The rules when passing a value to a function are similar to those when assigning a value to a variable (will move or copy).

#### Return Values and Scope

Returning values can also transfer ownership.

Moving a variable into a function and returning ownership through return value is tedious. _References_ let us use a value without transferring ownership.

### 4.2. References and Borrowing

_Reference_ is like a pointer, an address that can be followed to access the stored data; that data is owned by some other variable.

Unlike a pointer, a reference is guaranteed to point to a valid value of a particular type for the life of that reference.

_Borrowing_ is the act of creating a reference: you just borrow the value, you don't own it.

References are also immutable by default: you are not allowed to modify something we have a reference to.

#### Mutable References

Mutable references allow us to modify a borrowed value.

One big restrition: you cannot have other references to that value. This helps prevent data races at compile time.

A _data race_ is similar to a race condition and happens when these three occur:

- Two or more pointers access the same data at the same time.
- At least one of the pointers is being used to write to the data.
- There's no mechanism being used to synchronize access to the data.

Another restriction: you cannot have a mutable reference while having an immutable one to the same value.

A reference's scope starts from where it is introduced and continues through the last time that reference is used.

#### Dangling References

The Rust compiler guarantes that references will never be dangling references: if you have reference to some data, the compiler ensures that the data will not go out of scope before the reference to the data does.

#### The Rules of References

1. At any given time, you can have _either_ one mutable reference _or_ any number of immutable references.
2. References must always be valid.

### 4.3. The Slice Type

_Slices_ are a kind of reference that refer to a contiguous sequence of elements in a collection. It does not have ownership.

Internally, the slice data structure stores the starting position and the length of the slice.

String literals are stored in the binary and have type `&str`. It's a slice pointing to that specific point of the binary.

```rust
let s = "Hello, world!";
```

Defining a function to take a string slice instead of a reference to a `String` makes our API more general and useful without losing any functionality.

```rust
// instead of:
fn first_word(s: &String) -> &str
// do this:
fn first_word(s: &str) -> str
```

## 5. Using Structs to Structure Related Data

### 5.3. Method Syntax

Methods (Functions implemented on a struct, aka associated functions) have semantic parameters: Reading (`&self`), mutating (`&mut self`), or consuming (`self`).

Tuple structs: gives name to a tuple

## 6. Enums and Pattern Matching

### 6.1. Defining an Enum

Can attach data to each of the variant of the enum directly. Each variant can have different types and amounts of associated data. Able to define methods on enums using `impl`.

Rust has `Option`, an enum defined by the standard library. It encodes the scenario in which a value could be something or nothing.

```rust
enum Option<T> {
  None,
  Some(T),
}
```

### 6.3. Concise Control Flow with `if let`

Can use `match` or `if let` to extract and use values from `Option<T>` type values.

`if let` is the same as a `match` block where you only want to match against one pattern and does nothing for the rest. Less typing, less indentation, and less boilerplate, but you'll lose exhaustive checking.

## 7. Managing Growing Projects with Packages, Crates, and Modules

### 7.1. Packages and Crates

A package can contain multiple binary crates and optionally one library crate.

Rust module system includes:

- **Packages**: A Cargo feature that lets you build, test, and share crates
- **Crates**: A tree of modules that produces a library or executable
- **Modules** and **use**: Let you control the organization, scope, and privacy of paths
- **Paths**: A way of naming an item, such as a struct, function, or module

A _crate_ is the smallest amount of code that the Rust compiler considers at a time. Crates can contain modules.

A crate can come in a binary form or a library form:

- Binary crates are programs compiled to an executable you can run (e.g., command-line program, server, etc.)
  - Each must have a `main` function
- Library crates don't compile to an executable and don't have a `main` function
  - They define functionality intended to be shared with multiple projects

The _crate root_ is a source the the Rust compiler starts from and makes up the root module of your crate.

A _package_ is a bundle of one or more crates that provides a set of functionality.

- Contains a _Cargo.toml_ file that describes how to build those crates
  - Cargo is actually a package that contains the binary crate (command-line tool) used to build Rust code and a library crate that the binary crate depends on
- Can contain as many binary crates as you like, but at most only one library crate

`src/main.rs` is the crate root of a binary crate with the same name as the package.

`src/lib.rs` is the crate root of a library crate with the same name as the package.

A package can have multiple binary crates by placing files in the `src/bin` directory: each file will be a separate binary crate.

### 7.2. Defining Modules to Control Scope and Privacy

_Paths_ allow you to name items; the `use` keyword brings a path into scope; and the `pub` keyword makes items public.

## 8. Common Collections

### 8.2. Storing UTF-8 Encoded Text With Strings

ASCII: American Standard Code for Information Interchange. Each charater represented with 7 bits. Only 128 characters! English alphabets, integers, and special symbols.

Unicode consortium: Born to unite different text encodings from different countries when the World Wide Web got big in the 90's.

UTF-8: Widely used encoding by the Unicode consortium. Variable-width character encoding, a character can be 1-4 bytes. Can encode 1,112,064 characters! Backwards-compatible with ASCII. Used on web. Can encode emoijs.

UTF-8 is also the encoding used in Rust.

Rust has two string types:

- `&str`: the only string type in the core language - _string slices_, references to some UTF-8 encoded string data stored elsewhere (program binary, stack or heap). The string data actually lives somewhere else in memory (`str`) while we only use a "view" into it (`&str`). This is a _fat pointer_ (pointer + associated metadata) consisting of the pointer to the first character in memory and the length of string.
- `String`: _growable_, _mutable_, _owned_, _UTF-8 encoded_ string type. Use it when you need to own or modify your string data. The fat pointer consists of the pointer to the first character of the string on the heap, the length of the string, and its capacity.

String literals are string slices that live in the application binary.

Use `format!` over `+` for string concatenation since the latter takes ownership of the first value passed into it.

Use `concat!` over `format!` if you want `&str` instead of `String`.

## 9. Error Handling

`Result` enum represents success or failure (compared to `Option` enum which represents some value or none).

```rust
enum Result<T, E> {
  Ok(T),
  Err(E)
}
```

Instead of nesting `match` statements to pattern match `Result` values, you can use the `unwrap_or_else()` method with a closure to handle the error case.

Can use `unwrap()` method to quicky get the wrapped value or panic when error, useful when prototyping. Use `expect()` to add error messages.

The `?` operator unwraps the value stored in `Result` values or returns the error instead of panicking like `unwrap()` or `expect()`.

The `main` function can only return the unit value `()` or a `Result` type.

```rust
fn main() -> Result<(), Box<dyn std::error::Error>> {}
```

Using catch-all error types like `Box<dyn error::Error>` isn't recommended for library code, where callers might want to make decisions based on the error content, instead of printing it out or propagating it further.

## 10. Generic Types, Traits, and Lifetimes

### 10.2. Traits: Defining Shared Behavior

Traits allow us to define a set of methods that are shared across different types.

Implement a trait on a type gives behaviors to types. Each type that implements a trait can have their own implementation of the trait's methods.

Traits can have default implementation for its methods. Types that implement these traits can then override these default implementations with their own, as long as they agree with the method signature.

Use _trait bounds_ to use traits as parameters.

```rust
pub fn notify(item: &impl Summary) {}

// Syntactic sugar for
pub fn notify<T: Summary>(item: &T) {}

trait Summary {}
```

Can stack multiple traits in parameters.

```rust
pub fn notify(item1: &(impl Summary + Display), item2: &impl Summary) {}
```

Use `where` clause to improve readability.

```rust
fn some_function<T, U>(t: &T, u: &U) -> i32
where
    T: Display + Clone,
    U: Clone + Debug,
{
}
```

Can also use `impl TraitName` in return values of functions.

Implementations of a trait on any type that satisfies the trait bounds are called blanket implementations and are extensively used in the Rust standard library.

```rust
impl<T: Display> ToString for T {}
```

### 10.3. Validating References with Lifetimes

The Rust borrow checker runs at compile time and checks to make sure that all borrowed values and references are valid.

Generic lifetime annotations describe the relationships between lifetimes of multiple references and how they relate to each other. Also known as _lifetimes_.

```rust
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {}
```

- `&i32`: a reference
- `&'a i32`: a reference with an explicit lifetime
- `&'a mut i32`: a mutable reference with an explicit lifetime

This DOES NOT mean `x`, `y`, and the return value will have the same value. It actually means "the lifetime of the returned reference will be the smallest of the lifetime of the arguments".

One rule: The lifetime of a return value must always be tied to the lifetime of one of the function's parameters.

In previous chapters we always use owned types in structs. If we want to use references in struct fields, we have to specify their lifetimes.

```rust
struct ImportantExcerpt<'a> {
  part: &'a str,
}
```

Lifetime ellision rules:

1. Each parameter that is a reference gets its own lifetime parameter
2. If there is exactly one input lifetime parameter, that lifetime is assigned to all output lifetime parameters
3. If there are multiple input lifetime parameters, but one of them is `&self` or `&mut self`, the lifetime of `self` is assigned to all output lifetime parameters

The static lifetime means that the reference will live as long as the program. All string literals have a static lifetime since they are stored in the program binary.

```rust
let s: &'static str = "I have a static lifetime.";
```

## 11. Writing Automated Tests

`#[cfg(test)]` attribute/annotation on top of `mod tests` tells the compiler to only compile code when it's running in test mode. This module named `tests` is typically in each file that contains the code being tested.

Annotate functions we want to test inside the `tests` module with the `#[test]` attribute. Each function will be ran in a thread. The test fails when the thread panics.

When you run your tests with the `cargo test` command, Rust builds a test runner binary that runs the annotated functions and reports on whether each test function passes or fails.

`assert!`, `assert_eq!`, `assert_ne!` macros are useful for asserting predicates and panicking the thread, failing the test when the predicate is not met.

Annotate the test with `#[should_panic]` to test scenarios that should panic. Be even more specific (which `panic`?) with error messages `#[should_panic](expected = "Error message should be like this)`.

Tests that return a `Result` type:

```rust
#[test]
fn it_works() -> Result<(), String> {}
```

Ignore test with `#[ignore]`.

Rustaceans think of tests in two categories:

- Unit tests
  - Small
  - Focused
  - Test one module in isolation
  - Test private interfaces
- Integration tests
  - External to library
  - Test the library's public interface

In Rust:

- Unit tests
  - Live in the same file as project code
- Integration tests
  - Live in the `tests` directory at the project root
  - Each file in `tests` is a crate
  - Files in sub-directories of `tests` do not get compiled as crates
    - Can be used for common setup code between different integration test crates

## 13. Functional Language Features: Iterators and Closures

Closures are anonymous functions.

```rust
let anonymous = |n: u32| -> u32 {}
```

The compiler can infer input and output concrete type of a closure from its usage.

All closures implement one of the three traits: `Fn`, `FnMut`, `FnOnce`. Same for regular functions.

Closures capture the environment they are used, incurring more overhead than regular functions that don't.

Use the `move` to force the closure to take ownership of input parameters in its environment.

All iterators in Rust implement the `Iterator` trait in the Rust standard library.

```rust
pub trait Iterator {
  type Item; // associated type

  fn next(&mut self) -> Option<Self::Item>;

  // methods with default implementations elided
}
```

Use `iter()`, `iter_mut()`, and `into_iter()` methods to tell `next()` to return immutable references, mutable references, or owned types respectively.

In the default methods provided by `std` to the `Iterator` trait, there are two categories:

1. Adapters: Takes in an iterator and return another iterator.
2. Consumers: Takes in an iterator and return some other type.

Rust follows the zero-cost abstraction principle - using higher-level abstractions does not have a meaningful impact to performance. Using iterators or loops is the same in terms of speed. Rustaceans tend to prefer iterators for methods (ergonomics) and higher abstraction level than loops.

## 15. Smart Pointers

The most common kind of pointer in Rust is a reference, indicated by the `&` symbol and borrows the value it points to. Only refer to data and have no overhead.

_Smart pointers_ are data structures that act like a pointer but have additional metadata and capabilities. They also own the data they point to in many cases, unlike references.

`String` and `Vec<T>` are smart pointers since they own some memory, allow you to manipulate it, have metadata and extra capabilities or guarantees.

Usually implemented using structs. Unlike ordinary structs, smart pointers implement the `Deref` and `Drop` traits.

- The `Deref` trait allows an instance of the smart pointer struct to behave like a reference so you can interoperate references and smart pointers.
- The `Drop` trait allows you to customize the code that's run when an instance of the smart pointer goes out of scope.

### 15.1. Using `Box<T>` to Point to Data on the Heap

Boxes allow you to store data on the heap rather than stack. What remains on the stack is the pointer to the heap data. Boxes don't have performance overhead, other than storing their data on the heap.

Use cases:

- When you have a type whose size can't be known at compile time and you want to use a value of that type in a context that requires an exact size
- When you have a large amount of data and you want to transfer ownership but ensure the data won't be copied when you do so
- When you want to own a value and you care only that it's a type that implements a particular trait rather than being of a specific type

_Recursive types_ pose an issue because at compile time Rust needs to know how much space a type takes up. Because boxes have a known size, wrapping a recursive type in its definition would enable recursive types.

Rust computes the size of a non-recursive type by looking at each field. For enums, it's similar to unions in C, take the space it needs to store the largest variant. For structs, it's the sum of the struct fields.

For recursive types, since Rust knows how much space a `Box<T>` needs: a pointer's size doesn't change based on the amount of data it points to.

```rust
enum List {
  Cons(i32, Box<List>),
  Nil,
}
```

will have the size `i32` or `usize` depedent on the target platform.

`Box<T>` implements the `Deref` trait, which allows `Box<T>` values to be treated like references.

When a `Box<T>` value goes out of scope, the heap data that the box is pointing to is cleaned up as well because of the `Drop` trait implementation.

### 15.2. Treating Smart Pointers Like Regular References with the `Deref` Trait

The `Deref` trait allows you to customize the behavior of the _dereference_ operator `*`.

```rust
use std::ops::Deref;

impl<T> Deref for MyBox<T> {
    type Target = T;

    fn deref(&self) -> &Self::Target {
        &self.0
    }
}
```

### 15.3. Running Code on Cleanup with the `Drop` Trait

The `Drop` trait lets you customize what happens when a value is about to go out of scope. You can implement the `Drop` trait on any type, and that code can be used to release resources like files or network connections.

```rust
struct CustomSmartPointer {
    data: String,
}

impl Drop for CustomSmartPointer {
    fn drop(&mut self) {
        println!("Dropping CustomSmartPointer with data `{}`!", self.data);
    }
}

fn main() {
    let c = CustomSmartPointer {
        data: String::from("my stuff"),
    };
    let d = CustomSmartPointer {
        data: String::from("other stuff"),
    };
    println!("CustomSmartPointers created.");
}
```

Rust automatically calls `drop` for us when our isntances went out of scope.

You can drop a value early with `std::mem::drop`. One example is when using smart pointers that manage locks: you might want to force the `drop` method that releases the lock so that other code the same scope can acquire the lock.

Rust doesn't allow you to call the `Drop` trait's `drop` method manually because Rust would still automatically call `drop` on the value when it goes out of scope. This would cause a _double free_ error.

The `drop` function in `std` is simply an empty function that takes ownership of the value and make it go out of scope, no magic compiler sauce.

### 15.4. `Rc<T>`, the Reference Counted Smart Pointer

Enable multiple ownership explicitly by using the Rust type `Rc<T>`, an abbreviation for _reference counting_. The `Rc<T>` type keeps track of the number of references to a value to determine whether or not the value is still in use. If there are zero references to a value, the value can be cleaned up without any references becoming invalid.

Use case: Allocate some data on the heap for multiple parts of the program to read and we can't determine which part will finish using the data last at compile time.

**Note**: `Rc<T>` is only for use in single-threaded scenarios. Reference couting in multithreaded programs will be discussed in Chapter 16.

Convetion: Use `Rc::clone` instead of `.clone()` method although both behave the same way. This method increases the reference count.

### 15.5. `RefCell<T>` and the Interior Mutability Pattern

_Interior mutability_ is a design pattern in Rust that allows you to mutate data even if there are immutable references to that data. Normally this is disallowed by the borrow checker. The pattern uses `unsafe` code inside a data structure to bypass Rust mutation and borrowing rules. `unsafe` code is not checked for memory safety at compile time. Even so, we can still enforce those rules at runtime.

If you break the borrowing rules at runtime, your program will panic and exit. The reasoning for runtime checking instead of the default, compile-time checking is because certain properties of a program are impossible to detect using static analysis.

**Note**: `RefCell<T>` is only for use in single-thread scenarios. Same functionality for multithreaded programs will be discussed in Chapter 16.

## 16. Fearless Concurrency

### 16.1. Using Threads to Run Code Simultaneously

In most OSs, an executed program's code is run in a process, and the OS will manage multiple processes at once.

Within a program, you can also have independent parts that run simultaneously. These parts are run by _threads_. E.g., a web server can have multiple threads to respond to more than one request at a time.

This improves program performance, but adds complexity.

- Race conditions, where threads are accessing data or resources in an inconsistent order
- Deadlocks, where two threads are waiting for each other, preventing both from continuing
- Bugs that happen only in certain situations and are hard to reproduce and fix reliably

The Rust standard library uses a _1:1_ model of thread implementation, a program uses one OS thread per one language thread. Other crates implement other models of threading that make different tradeoffs to the 1:1 model.

There are two types of threads:

- One-to-one/OS/native/system threads: Many OS's provide an API to create new threads
- Green/user/program threads (m:n model): Program or runtime library's special implementation of threads. These do not have a 1:1 mapping with OS threads

Create a new thread with the `thread::spawn` function and pass it a closure containing the code we want to run in the new thread.

When the main thread of a Rust program completes, all spawned threads are shut down, whether or not they have finished running.

`thread::sleep` forces a thread to stop its execution for a short duration, allowing a different thread to run.

Fix the problem of a spawned thread not running or ending prematurely by saving the return value of `thread::spawn` in a variable, whose type is `JoinHandle`. This is a owned value that, when we call the `join` method on it, will wait for its thread to finish.

This method blocks the thread currently running until the thread represented by the handle terminates. _Blocking_ means that the thread is prevented from performing work or exiting.

We often use the `move` keyword with closures passed to `thread::spawn` because the closure will then take ownership of the values it uses from the environment, transferring ownership of those values from one thread to another. To use data from the main thread in the spawned thread, the spawned thread's closure must capture the values it needs.

### 16.2. Using Message Passing to Transfer Data Between Threads

One popular approach to ensure safe concurrency is _message passing_, where threads or actors communicate by sending each other messages containing data.

Go proverb:

> Do not communicate by sharing memory; instead, share memory by communicating.

Rust standard library accomplishes message-sending concurrency with an implementation of _channels_, a general programming concept by which data is sent from one thread to another.

A channel has two parts: a transmitter and a receiver. One part of your code calls methods on the transmitter, passing in the data you want to send and another part of your code is listening to the receiver for arriving messages. A channel is _closed_ if either end is dropped.

Create a new channel using the `mpsc::channel` function; `mpsc` stands for _multiple producer, single consumer_. This means a channel can have multiple _sending_ ends that produce values but only one _receiving_ end that consumes those values.

A thread can either send or receive. Spawn new threads to send. The spawned thread needs to own the transmitter to be able to send messages through the channel.

The `send` method that takes and sends the value we want to send.

`recv` blocks the main thread's execution and wait until a value is sent down the channel. The `try_recv` method doesn't block but return a `Result<T, E>` immediately. This is useful if this thread has other work to do while waiting for messages: we ould write a loop that calls `try_recv` every so often, handles a message if one is available, and otherwise does other work for a little while until checking again.

Create multiple producers by cloning the transmitter, which should be passed to a newly spawned thread.

### 16.3. Shared-State Concurrency

Another concurrency method would be for multiple threads to access the same shared data.

Rust type system and ownership rules assist in getting the management of multiple ownership of a same memory location correct. Mutexes is one of the common concurrency primitives for shared memory.

_Mutex_ is an abbreviation for _mutual exclusion_, meaning only one thread can access a piece of data at any given time.

To access the data in a mutex, a thread must first signal that it wants access by asking to acquire the mutex's _lock_, a data structure that is part of the mutex that keeps track of who currently has exclusive access to the data.

In short, the mutex is _guarding_ the data it holds via the locking system.

Two rules regarding mutexes:

1. You must attempt to acquire the lock before using the data.
2. When you're done with the data that the mutex guards, you must unlock the data so other threads can acquire the lock.

Create a `Mutex<T>` using the associated function `new`.

To access the data inside the mutex, we use the `lock` method to acquire the lock. This call blocks the current thread so it can't do any work until it's our turn to have the lock.

`Mutex<T>` is a smart pointer. Calling `lock` returns the smart pointer `MutexGuard`, wrapped in a `LockResult`.

This smart pointer:

- implements `Deref` to point at its inner data
- implements `Drop` to release the lock automatically when a `MutexGuard` goes out of scope.

To solve multiple ownership in multithreaded scenarios, we could use `Rc<T>`. Unfortunately, `Rc<T>` is not safe to share across threads, thus not _thread-safe_. We have to use `Arc<T>`, atomic reference counting. This has the same API as `Rc<T>`.

Thread safety comes with a performance penalty that you only want to pay when you really need to. Choose `Rc<T>` in single-threaded scenarios and `Arc<T>` in multithreaded ones.

### 16.4. Extensible Concurrency with the `Sync` and `Send` Traits

Allow transference of ownership between threads with `Send`.

Allow access from multiple threads with `Sync`.

Implementing `Send` and `Sync` manually is unsafe.

## 17. Object-Oriented Programming Features of Rust

### 17.1. Characteristics of Object-Oriented Languages

In Rust, instead of classes, structs and enums hold data, and have implementations that provide methods.

Encapsulation by utilizing the Rust module system.

Inheritance by utilizing `Default` trait method implementations. Polymorphism by using generics to abstract away concrete types and trait bounds to restrict the characteristics of those types and trait objects.

### 17.2. Using Trait Objects That Allow for Values of Different Types

Define a trait object:

```rust
Box<dyn SomeTraitName>
```

The `dyn` keyword means _dynamic dispatch_. Rust will ensure at compile any object in referenced by the trait object will implement the `SomeTraitName` trait.

```rust
T: SomeTraitName
```

So why not generics and trait bound? A generic type parameter can only be substituted with one concrete type at a time, whereas trait objects allow for multiple concrete types to fill in for the trait object at runtime.

So `Vec<T> where T:SomeTraitName` can only contain one concrete type that implements `SomeTraitName`, whereas `Vec<Box<dyn SomeTraitName>>` can contain a mixture of concrete types that all implement `SomeTraitName`.

If you'll only ever have homogenous collections, use generics and trait bounds because the definitions will be monomorphized at compile time to use concrete types. Trait objects have runtime performance implications to them.

Recall monomorphization: the compiler generates nongeneric implementations of functions and methods for each concrete type that we use in place of a generic type parameter. This is _static dispatch_, the compiler knows what methods you're calling at compile time.

_Dynamic dispatch_ is when the compiler does not know what methods you're calling at compile time. In these cases, the compiler emits code that at runtime will figure which method to call.

When using trait objects, Rust uses the pointers inside the trait object to know which method to call, which incurs a runtime cost.

### 17.3. Implementing an Object-Oriented Design Pattern

In the state pattern, we have some value which has an internal state that is represented by a state object. Each state object is responsible for its own behavior and deciding when to transition into another state. The value that holds the state objects knows nothing about the different behaviors of states or when to transition to different states.

Encoding state into the type system is more ergonormic in Rust compared than pure object-oriented state design pattern (which is also possible). But they have their tradeoffs.

## 19. Advanced Features

### 19.5. Macros

Macros are a way of writing code that writes other code, which is known as _metaprogramming_. In other words, code-to-code transformation.

Macros like `vec!` or `println!` expands to more code than you write manually.

It reduces the amount of code you have to write and maintain, like functions. Unlike functions:

- Macros can accept a variable amount of parameters, with functions it has to be a set number
- Macros expand before the program finishes compiling while functions are called at runtime
- More powerful than functions, in exchange for more complexity

Rust has two types of macros: _declarative_ and _procedural_.

#### Declarative Macros with `macro_rules!` for General Metaprogramming

Most widely used form of Rust macros. Written similar to `match` expressions, replacing code with other code.

#### Procedural Macros for Generating Code from Attributes

Written more like functions, take code as input, operate on that code, produce code as output. There are three kinds of procedural macros:

- Custom-derive: `#[derive(MacroName)]`
- Attribute-like
- Function-like

## 20. Final Project: Building a Multithreaded Web Server

You can improve the throughput of a web server with a thread pool. A _thread pool_ is a group of spawned threads that are waiting and ready to handle a task.

When the program receives a new task, it assigns one of the threads in the pool to the task, and that thread will process the task. The remaining threads in the pool are available to handle any other tasks that omein while the first thread is processing. When the first thread is done processing its task, it returned to the pool of idle threads, ready to handle a new task.

This allows the web server to process connections concurrently, increasing its throughput.

For a web server with a thread pool, we'll have a fixed number of threads waiting in the pool. The pool will maintain of a queue of incoming requests, each of the threads in the pool will pop off a request from this queue, handle the request, and then ask the queue for another request.

If each thread is busy responding, incoming requests will stil back up in the queue, but we've increased the number of threads before reaching that point.

Other options to improve the throughput of a web server are the _fork/join model_, the _single-thread async I/O model_, or the _multi-threaded async I/O model_.

## 21. Appendix

### 21.4. D - Useful Development Tools

- Automatic formatting with `rustfmt`
- Fix your code with `rustfix`
- More lints with Clippy
- IDE integrations with `rust-analyzer`
