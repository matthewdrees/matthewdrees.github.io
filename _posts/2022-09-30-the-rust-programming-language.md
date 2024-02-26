---
layout: post
title:  "Book notes: The Rust Programming Language"
category: book
---

![Book cover](/assets/the-rust-programming-language.jpg)

By Steve Klabnik and Carol Nichols, with contributions from the Rust Community.

Book club time. We opted for the online version of the [The Rust Programming Language](https://doc.rust-lang.org/book/title-page.html) rather than the paper book form.

## 1. Getting Started

[Installation](https://doc.rust-lang.org/book/ch01-01-installation.html) rust-all is available on apt package manager but I opted for the recommended rustup script.

[Hello world](https://doc.rust-lang.org/book/ch01-02-hello-world.html). This gets created automatically with "cargo new <project-name>". Note about [whether or not to commit Cargo.lock file](https://stackoverflow.com/questions/62861623/should-cargo-lock-be-committed-when-the-crate-is-both-a-rust-library-and-an-exec).

[Cargo](https://doc.rust-lang.org/book/ch01-03-hello-cargo.html) - Rust's build system and package manager. Touches on new, build (--release), run, check.

[Rust support on Visual Studio Code](https://code.visualstudio.com/docs/languages/rust). (Note I run VSCode with WSL on Ubuntu.) Installed rust-analyzer extension. I used "cargo new hello-world" to create the project, then "Open Folder" in VS Code. To run/debug I had to "add configuration" for launch.json -> "(gdb) Launch" (Note I already have c++ extensions installed), and update "program" to "${workspaceFolder}/target/debug/hello-world".

"Rustaceans" - LOL!

## 2. Programming a Guessing Game

Introduction to many common parts of Rust language by [https://doc.rust-lang.org/book/ch02-00-guessing-game-tutorial.html](walking through a small program) that generates a random number between 1 and 100 and lets the user guess the number on the command line.

Variables are const by default. Nice! Const is such a pain in C++, between forgetting to make something const that should have been, or leaving something non-const to allow for moves.

Unhandled returns are compiler warnings. Nice! They are silently allowed in C++.

In this example they allow the same variable "guess" to be a string and then converted to a unsigned 32 bit number by "shadowing".

The cargo system for using the "rand" external crate is slick. The Cargo.lock file tracks which version was actually used, and can be updated with "cargo update".

## 3. Common Programming Concepts

The chapter name says it all. Notes:
* Uh-oh... "shadowing" allows you to use "let" again on a variable to re-bind it. Immutable is a lie!
* Debug builds check for integer overflows/underflows, release builds wrap the number.
* Char is 4 bytes. (But note link to section about UTF-8.)
* Tuples & arrays.
* Expressions vs Statements. Expressions don't have an ending semicolon. I find them confusing at the moment but have faith it will make sense after a while.
* If statement code blocks are called "arms".
* Looping constructs.

## 4. Understanding Ownership

Ownership is a key feature of Rust. The lifetime of a value is defined by the scope. You can have a single mutable reference to a value or any number of immutable references.

Aside: I really like the diagram explanations for references and slices.

## 5. Using Structs to Structure Related Data

Nice features:
* Field init shorthand.
* Struct update syntax.
* Tuple structs.
* Unit-like structs (no fields).

Note that fields in a struct are public within the module (file), but private by default outside of it.

[Derive traits](https://doc.rust-lang.org/rust-by-example/trait/derive.html), e.g. for cloning, hashing, value format for debugging, etc.

Struct methods, associated functions.

## 6. Enums and Pattern Matching

Whoa, enums in Rust can be strings, structs, etc. Enums can have functions.

enum option<T>: allows the compiler to confirm proper checks for null. Tony Hoare calls null references a "billion-dollar mistake".

match: like a switch statement (case => "arms"). Must have arms for all possibilities, or "other" for default case if you use the value, or "_" if you don't use the value. Arms are evaluated in the order specified.

if let: Less code if you only care about one value in the enum.

## 7. Managing Growing Projects with Packages, Crates, and Modules

* Crate: Smallest unit of code for rustc. Usually means "library".
* Package: Bundle of 1 or more crates. Can only have 1 library, but can have unlimited executable crates. Crate type determined by presence of lib.rs or main.rs.
* Modules:
  + API for package.
  + Defined by "mod" keyword.
  + Can be in one or several files.
  + Default visibility is private for outside the module or module "ancestry" (great word), public for inside the module or child modules. Can be made public with "pub" keyword.
* [Rust API Guidelines](https://rust-lang.github.io/api-guidelines/) complete with checklist.
* Smaller random module stuff:
  + "pub use" for reexporting.
  + "use" keyword only good for current namespace.
  + "use" nested paths. E.g. "use std::{cmp:: Ordering, io}"
  + "use namespace::*" (glob) for importing all modules in a namespace.

## 8. Common Collections

Vec, String, and HashMap track close to similar structures in other languages.

String is UTF-8 which has variable-length characters. Can iterate with a char but not index into it.

## 9. Error Handling

Recoverable vs unrecoverable errors. Lots of good shortcuts for handling/propagating errors with expect, unwrap, and ?. Guidelines for when to panic.

## 10. Generic Types, Traits, and Lifetimes

Generics in Rust a lot like templates in C++. I'm impressed by how Rust enums behave like Unions/Variants and can take advantage of generics. As with templates, Rust "stamps" out code for concrete types when compiling so there is no runtime cost. Fun word: *monomorphisation*.

Traits are like C++ concepts. Trait methods can have a default implementation. Function parameters can require traits (many different examples). Functions can return a generic with a trait bound, but for now must be the same type (can't return different types with same trait).

Shout out to the borrow checker, one of the few compiler parts with its own branding!

Uh oh. Lifetime annotation syntax?! Blah. At first glance: for functions taking and returning references, they help establish lifetime relationships, which lead to better error messages.

There are "lifetime elision" rules that eliminate the need for lifetime annotation for more common cases, which  may change/grow over time. Three rules:
1. Compiler assigns a lifetime to all input references.
2. If exactly one input lifetime parameter, that lifetime is assigned to all output lifetime parameters.
3. &self or &mut self lifetime is assigned to all output lifetimes.

## 11. Writing Automated Tests

Rust compiler and type checking can't do it all, still have to write tests.

How to setup tests with good error messages for assert, assert_eq, assert_ne, tests that should panic (with specific panic messages), and tests that return Result<T, E>.

Tests run in parallel by default.

Annotate long-running tests with [ignore] attribute.

Can filter which tests run.

Unit tests: Have a "test" module in each file in src, test at function level. Annotate module with #[cfg(test)] and test function with #[test]. Can test private functions (because in the same file).

Integration tests: Separate "tests" subfolder. Each file is a crate that can specialize in specific use-cases. Put setup code in common/mod.rs. Can't create integration tests in a binary crate; for those prefer a "straightforward" main.rs that calls into a lib.rs (which can have integration tests).

Doc-tests: unit tests that can be put in documentation strings, likely inspired by Python's doctest.

If a section (unit, integration, doc-test) has a failed test, subsequent sections don't run.

## 12. An I/O Project: Building a Command Line Program

This chapter was great! It puts together many of the lessons learned so far in a real-ish example _minigrep_ command line tool. It builds with several refactors and points out "best practices" along the way. Someone put a lot of love into writing this chapter. I'd recommend this chapter as "minimum reading distance" for this book.

Lots of good tidbits but interesting note about what goes in lib.rs vs main.rs: Keep main.rs to a minimum. If your CLI handling gets complicated, move it to lib.rs.

## 13. Iterators and Closures

"Mastering closures and iterators is an important part of writing idiomatic, fast Rust code."

Closures are anonymous functions that capture values from environment in 3 ways: borrow mutably, borrow immutably, or take ownership. These are handled with std::ops:: traits: FnMut, Fn, and FnOnce. The *move* keyword forces ownership, which is "mostly useful for spawning a new thread".

Closures don't need type annotations like functions except in some rare cases. Types are deduced and that's what they are, you can't re-call it with different types.

Iterators are implemented with the *Iterator* trait and *next* method. Updated the *minigrep* example to use iterator on env::args() directly rather than cloning them into a Vec<String>.

Iterators are a *zero cost abstraction* for Rust. Performance examples with minigrep program and an audio decoder.

## 14. More about Cargo and Crates.io

Customizing builds. e.g. optimization levels for build profiles.

Documentation. *//!* for commenting the current item (e.g. crates and modules), *///* for comments to the next item (e.g. function, struct). cargo doc* to build documentation. Should document examples, panics, errors, and safety. Code samples in comments will run as doctests with cargo test. Promote (i.e. re-export) useful APIs that are buried in a module hierarchy with *pub use*.

Crates.io is a public repository of useful code. Anyone can publish to it. Code is permanent. Can *yank* a version to prevent other projects from using it. Use a crate by putting the it in *dependencies* section of your *Cargo.toml* file. Install and run binaries from it using *cargo install*.

"A *workspace* is a set of packages that share the same *Cargo.lock* and output directory."

## 15. Smart Pointers

Box<T> stores T on the heap. E.g. let n = Box::new(5) creates an i32 value of 5 on the heap.

The Deref trait provides the dereference operator *. Can then treat a reference like a type.

Deref coercion converts a reference to another type of reference that helps avoid unnecessary reference/dereference calls everywhere. E.g. &String -> &str.

The Drop trait provides code cleanup when the variable goes out of scope.

Rc<T> for reference counting.

RefCell<T> to check borrowing rules at runtime instead of compile time. *Interior mutability* pattern. Useful for Mock objects.

Can still get memory leaks with a reference cycle. Solution: use Weak<T> instead of Rc<T>.
