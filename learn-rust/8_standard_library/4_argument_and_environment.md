# Arguments and the Environment

## Basic Concepts

Most applications accept some sort of parameters, much like a function. The parameters are often supplied through command line arguments and environment variables. In this lesson, you will dive into both terms in more detail.

## Arguments

1. The arguments with which the program has been invoked can be accessed through the `std::env` module.

    ```rust
    for argument in std::env::args() {
        println!("{argument}");
    }
    ```

    While the standard library allows you to read arguments, creating a proper command line application with subcommands, positional and optional parameters is probably best done by utilizing a library from the Rust ecosystem like clap.


## Environment Variables

1. The environment variables set for the program upon invocation can also be accessed through the std::env module.

    ```rust
    for (key, value) in std::env::vars() {
        println!("{key}={value}");
    }
    ```

## Current Working Directory

1.  The current working directory for the current process can be obtained with std::env::current_dir.

    ```rust
    fn main() {
        let path = std::env::current_dir().unwrap();
        println!("The current directory is {}", path.display());
    }
    ```

## Lesson Summary

In this lesson, you have been introduced to Arguments and Environment Variables. Via example, you have learned how to obtain the current working directory.