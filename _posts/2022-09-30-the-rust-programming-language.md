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

match: like a switch statement (case => "arms"). Must have arms for all possibilities, or "other" for default case, or "_" for don't care about other cases. Arms are evaluated in the order specified.

if let: Less code if you only care about one value in the enum.
