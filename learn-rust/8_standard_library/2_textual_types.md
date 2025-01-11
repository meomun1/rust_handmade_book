# Textual Types

## Basic Concepts

☝️ Text data can be represented and stored in many different ways. The simplest way to represent text in a computer system is to assign a number to each character you want to be able to store.

This is what the American Standard Code for Information Interchange (ASCII) character encoding standard did. The ASCII standard has been widely used, but only allows storing 256 characters.

In order to be able to represent text written in different scripts, such as 汉字 (Chinese characters), and to integrate symbols such as emoji 😊, a new standard was introduced; the Unicode Transformation Format (UTF) character encoding standard.

## String 

1. The `String` type defined by the Rust standard library represents an owned piece of text stored in the UTF-8 character encoding. It is closely related to the primitive type `str` , which represents a borrowed piece of text stored in the UTF-8 character encoding.

    ```rust
    let mut hello = String::from("Hello, ");

    hello.push('w');
    hello.push_str("orld!");

    println!("{hello}"); // Hello, world!
    ```
    In a UTF-8 encoded string, not every character takes up the same number of bytes.For example, the letter a takes up 1 byte and the emoji 😊  takes up 4 bytes:

    ```rust
    println!("{:?}", "a".as_bytes()); // [97]
    println!("{:?}", "😊".as_bytes()); // [240, 159, 152, 138]
    ```

    Strings cannot be indexed by usize because it is unclear whether "😊"[0] should return the byte 240  or the character '😊,' which cannot be done constantly. After all, UTF-8 encoding uses different numbers of bytes for different characters.

    `To obtain the n-th byte, use` => "😊".as_bytes()[n]

    `To access the n-th` => "😊".chars().nth(n)

2. There is much more to learn about the String so consider the 

https://doc.rust-lang.org/std/string/struct.String.html