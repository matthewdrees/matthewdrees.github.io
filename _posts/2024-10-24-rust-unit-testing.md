---
layout: post
title:  "Rust Unit Testing"
category: software
---

# Rust Official Unit Test Documentation

The Rust unit testing is described [here](https://doc.rust-lang.org/rust-by-example/testing/unit_testing.html). It gives an example of testing an add function ...

```rust
// Simple add function to test
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}
```

... with a unit test looking something like this ...

```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_add_official() {
        assert_eq!(add(1, 1), 2);
        assert_eq!(add(1, 2), 3);
        assert_eq!(add(2, 1), 3);
        assert_eq!(add(-1, 1), 0);
        assert_eq!(add(1, -1), 0);
    }
}
```

I like that it has few lines of code. When run, the passing case is great ...

    $ cargo test
    running 1 test
    test proper_unittest_pattern::tests::test_add_official ... ok

    test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s

... but here is the failing case (Note I modified 2 cases to fail), with 2 things not to like:

1. If a case fails the remaining cases aren't executed
2. The failure output is only left and right, it doesn't give the function inputs or which is expected vs actual.

    $ cargo test
    running 1 test
    test proper_unittest_pattern::tests::test_add_official ... FAILED

    failures:

    ---- proper_unittest_pattern::tests::test_add_official stdout ----
    thread 'proper_unittest_pattern::tests::test_add_official' panicked at src/proper_unittest_pattern.rs:39:9:
    assertion `left == right` failed
    left: 3
    right: 2
    note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace


    failures:
        proper_unittest_pattern::tests::test_add_official

    test result: FAILED. 0 passed; 1 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s


# Preferred Rust Unit Testing Pattern

I've settled on this as a preferred unit testing pattern ...

```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_add() {
        let mut passed = true;
        #[derive(Debug)]
        struct TC {
            a: i32,
            b: i32,
            exp: i32,
        }

        let test_cases = vec![
            TC { a: 1, b: 1, exp: 2 },
            TC { a: 1, b: 2, exp: 3 },
            TC { a: 2, b: 1, exp: 3 },
            TC { a: -1, b: 1, exp: 0 },
            TC { a: 1, b: -1, exp: 0 },
        ];

        for tc in test_cases.iter() {
            let act = add(tc.a, tc.b);
            if tc.exp != act {
                passed = false;
                eprintln!("{tc:?}, act: {act}");
            }
        }
        assert!(passed);
    }
}
```

Failed test logging has these improvements: (Note: modified 2 cases to fail)
* All the tests run even one of them fails.
* You can see the input/expected/actual values in the failed test output.
* Note: adjust struct_lit_width ( >= 80?) in rustfmt.toml to keep individual test cases from autoexpanding to vertical formatting.

    $ cargo test
    running 1 test
    test proper_unittest_pattern::tests::test_add ... FAILED

    failures:

    ---- proper_unittest_pattern::tests::test_add stdout ----
    TC { a: 1, b: 2, exp: 2 }, act: 3
    TC { a: 1, b: -1, exp: 1 }, act: 0
    thread 'proper_unittest_pattern::tests::test_add' panicked at src/proper_unittest_pattern.rs:34:9:
    assertion failed: passed
    note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace


    failures:
        proper_unittest_pattern::tests::test_add

    test result: FAILED. 0 passed; 1 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s

There is a notable downside: More lines of code than the official case above. (Although the boilerplate isn't prone to copy/paste errors.)

# Another Unit Test Pattern - Each Test Case in its Own Function

Out of curiousity, here is each test in it's own function. Didn't like it:
* It's tedious to come up with a unique function name for each case. It's also prone to copy/paste errors if the function name doesn't match the actual inputs (if you did it that way).
* The test runner reporting is much noisier: I'd rather have tests reported at the function rather than case level.

```rust
// To reiterate: don't do it this way.
#[test]
fn test_add_1_1_2() {
    assert_eq!(add(1, 1), 2);
}
#[test]
fn test_add_1_2_3() {
    assert_eq!(add(1, 2), 3);
}
#[test]
fn test_add_2_1_3() {
    assert_eq!(add(2, 1), 3);
}
#[test]
fn test_add_n1_1_0() {
    assert_eq!(add(-1, 1), 0);
}
#[test]
fn test_add_1_n1_0() {
    assert_eq!(add(1, -1), 0);
}
```

The passing case output:

    $ cargo test
    running 5 tests
    test proper_unittest_pattern::tests::test_add_1_1_2 ... ok
    test proper_unittest_pattern::tests::test_add_1_2_3 ... ok
    test proper_unittest_pattern::tests::test_add_1_n1_0 ... ok
    test proper_unittest_pattern::tests::test_add_2_1_3 ... ok
    test proper_unittest_pattern::tests::test_add_n1_1_0 ... ok

    test result: ok. 5 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s

Failing case output:

    $ cargo test
    running 5 tests
    test proper_unittest_pattern::tests::test_add_1_1_2 ... FAILED
    test proper_unittest_pattern::tests::test_add_1_2_3 ... ok
    test proper_unittest_pattern::tests::test_add_1_n1_0 ... ok
    test proper_unittest_pattern::tests::test_add_2_1_3 ... ok
    test proper_unittest_pattern::tests::test_add_n1_1_0 ... FAILED

    failures:

    ---- proper_unittest_pattern::tests::test_add_1_1_2 stdout ----
    thread 'proper_unittest_pattern::tests::test_add_1_1_2' panicked at src/proper_unittest_pattern.rs:48:9:
    assertion `left == right` failed
    left: 2
    right: 3
    note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace

    ---- proper_unittest_pattern::tests::test_add_n1_1_0 stdout ----
    thread 'proper_unittest_pattern::tests::test_add_n1_1_0' panicked at src/proper_unittest_pattern.rs:60:9:
    assertion `left == right` failed
    left: 0
    right: 1


    failures:
        proper_unittest_pattern::tests::test_add_1_1_2
        proper_unittest_pattern::tests::test_add_n1_1_0

    test result: FAILED. 3 passed; 2 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s
