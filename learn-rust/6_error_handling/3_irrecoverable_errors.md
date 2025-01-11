# Irrecoverable Errors

## Basic Concepts

Some errors can occur, which indicate a problem with the software itself. It can very well be that an invariant expected to be upheld been violated, indicating a programming error that needs to be solved. In other cases, a program may not reasonably be expected to recover from a certain error. Most applications, which are not safety critical, are not expected, for example, to recover from out-of-memory errors. Errors from which an application is not expected to recover can be classified as irrecoverable errors. In this lesson, you will learn how irrecoverable errors are realized in Rust through a mechanism called panicking.

## Introduction a Panic 

1. You can easily introduce a panic with the `panic!` macro like so.

    ```rust
    panic!("Oh no!");

    👉 when run, this will output:

    thread 'main' panicked at 'Oh no!', src/main.rs:2:5
    note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
    ```

    The panicking mechanism works much like exceptions do in some other programming languages. When panic occurs, the call stack is unwound, and all initialized values that implement Drop are deconstructed.

2. From recoverable to irrecoverable 

    Often times when you implement a function, you don't know if the user of that function can handle an error or not.

    Consider, for example, the function std::fs::File::open, which attempts to open a file for reading. For one program, it may be a requirement that the file exists. ☝️ For another program, the file is not strictly necessary. The user of a function always knows more about how to handle errors than the implementor.

    😀 It is easy to turn a recoverable error wrapped in an `Option<T>` or `Result<T, E>` into a panic 

    ```rust
    std::fs::File::open("some-non-existing-file").unwrap();

    thread 'main' panicked at 'called `Result::unwrap()` on an `Err` value: Os { code: 2, kind: NotFound, message: "No such file or directory" }', src/main.rs:10:51
    note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
    ```

    Generally, panicking should only be used when it is extremely unlikely that the program should continue working. Panics can occur in a couple of places in the standard library. 👉 For example, accessing an array using an index that is out of the array's bounds or when a memory allocation is requested, but no more memory is available. When writing libraries, it is often better to implement error handling by returning 

3. Recovering from a panic 

    It is possible to catch a panic using `std::panic::catch_unwind` .☝️ This is not a good way to implement error handling, but may be useful under certain circumstances.

    ```rust

    match std::panic::catch_unwind(|| {
        panic!("Stop!");
    }) {
        Ok(_) => println!("No panics occurred."),
        Err(_) => println!("The code panicked."),
    }

    ```

    😀 As you can see, the panic did cause some text to be written to stdout. However, the program continued to run and printed The code panicked  before stopping.

4. Double panicking 

    When an application panics, the call stack is unwinded, and any drop implementations are called in order to release resources properly. Should a panic occur during a panic, a so-called double panic, the program aborts immediately.

    ```rust

    struct Boom;

    impl Drop for Boom {
        fn drop(&mut self) {
            panic!("Boom!");
        }
    }

    fn main() {
        let _boom = Boom;
        panic!("Stop!");
    }

    ```

    It is impossible to recover from a double panic, as the program immediately stops.

## Lesson Summary

In this lesson, you've learned that irrecoverable errors are raised through panicking, which unwinds the call stack and normally causes the application to exit.
