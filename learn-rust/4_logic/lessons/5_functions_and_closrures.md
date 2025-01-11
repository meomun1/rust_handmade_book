# Functions and Closures

## Basic concepts

1. Functions are the building blocks of readable, maintainable, and reusable code. In this lesson, you’ll discover different functions and how to declare them

2. While implementing a program, you often think of different layers of abstraction. When writing data into a database, you are not concerned about how to write bytes to the network interface and ensure you don’t lose any information in transit. Use functions, closures, and methods to facilitate abstracting and reusing pieces of code.

## Functions 

1. In Rust, a function can be declared as follows:

    ```rust 
    fn fn_name(input1: InputType1, input2, InputType2) -> OutputType {
    // body
    }

    // example 
    fn u32_add(a: u32, b:u32) -> u32{
        return a + b;
    }

    // function in function => function g can not be accessed outside function f
    fn f(n: u32) -> u32 {
        fn g(n: u32) -> u32 {
            n + 1
        }

        g(n * 2)
    }
    println!("{}", f(3));
    ```

2. The main function. A Rust executable actually needs a function that defines the entry point of the program

    ```rust
    fn main(){
        println!("I am the first statement executed by this program!");
    }

3. Associated Functions. You know how to create your data types through `struct` and `enum` => You can attach functions to these types. If those functions take a value of, or a reference to, a value of the type, they are also referred to as methods. 👉 A method of a type `T` is a function where the first argument is a reference to or value of `T`

    ```rust 
    struct X(&'static str);

    impl X {
        fn associated_fn() -> &'static str {
            "I am always the same"
        }

        fn method(self : &Self) -> &'static str {
            self.0
        }
    }

    // Call a function associated to the type `X`.
    println!("{}", X::associated_fn());
    // Create an instance of X and call a method on the instance.
    let instance = X("My value depends on an instance of `X`!");
    println!("{}", instance.method());

    // I am always the same!
    // My value depends on an instance of `X`!
    ```

## Closures 

1. Closures are very similar to functions, except they have the ability to "capture their environment". This can be useful, for example when working with iterators. `n` is incremented with every invocation of `c`. Closures are turned into a`struct` itself contains a number reference to `n` and you have to declare `let mut c`

    ```rust
    let mut n = 0;
    let mut c = |x| {
        n += 1;
        x + n - 1
    };
    println!("{}", c(2));
    println!("{}", c(2));
    println!("{}", c(2));

    
    2
    3
    4
    ```

2. Closures are most commonly used when iterating over collections of values.

    ```rust
    let a = [1, 2, 3];
    let n : i32  = a.iter().map(|x| x * 2).sum();
    println!("Sum of {:?} after doubling: {}", a, n);

    // Sum of [1, 2, 3] after doubling: 12
    ```

## Lesson Summary

A function is a set of statements to perform a specific task. Functions organize the program into logical blocks of code. In this lesson, you’ve learned about functions and closures. As well, you have discovered the way of their implementation.