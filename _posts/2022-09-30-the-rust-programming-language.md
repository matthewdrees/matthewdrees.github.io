---
layout: post
title:  "Book Notes: The Rust Programming Language"
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

Introduction to many common parts of rust language by [https://doc.rust-lang.org/book/ch02-00-guessing-game-tutorial.html](walking through a small program) that generates a random number between 1 and 100 and lets the user guess the number on the command line.

Variables are const by default. Nice! Const is such a pain in C++, between forgetting to make something const that should have been, or leaving something non-const to allow for moves.

Unhandled returns are compiler warnings. Nice! They are silently allowed in C++.

In this example they allow the same variable "guess" to be a string and then converted to a unsigned 32 bit number by "shadowing".

The cargo system for using the "rand" external crate is slick. The Cargo.lock file tracks which version was actually used, and can be updated with "cargo update".
