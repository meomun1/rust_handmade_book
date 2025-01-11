# Ownership Explanation

## What Is Ownership?

1. The ownership of dynamic memory allocations needs to be tracked regardless of the programming language used.

    `Solution #1`
    Using garbage collection, a method where the usage of memory allocated to the program is automatically tracked. The garbage collector automatically deallocates unused memory.

    `Solution #2`
    Using reference counted pointers, or smart pointers, to track ownership and sharing at run-time.

    `Rust`
    In most programming languages, it is either up to the programmer to properly keep track of memory or enforced through garbage collection. What makes Rust different is that it has a compile-time model of ownership.

2. The String type 

    
    `String`
    `Heap-allocated`: String is a growable, mutable, owned, heap-allocated string type.
    `Ownership`: It owns the data it contains, meaning it is responsible for managing the memory.
    `Mutable`: You can modify the contents of a String.
    `Methods`: Provides many methods for string manipulation.

    `str`
    `Slice`: str is a string slice, which is a view into a string. It is usually seen in its borrowed form &str
    `Immutable`: &str is an immutable reference to a string slice.
    `No Ownership`: It does not own the data it points to, so it cannot manage memory.
    `Fixed Size`: The size of a &str is fixed at compile time

3. Drop

    ```rust
    The way that Rust types clean up after themselves is through the Drop trait.

    When a value is no longer needed, Rust will run a destructor on that value. This happens when an initialized value is overwritten in an assignment statement, or when a value goes out of scope.

    As Rust automatically calls the destructors of all contained fields, you don’t have to implement Drop in most cases. But there are some cases where it is useful, for example, for types that directly manage a resource. That resource may be a memory. It may be a file descriptor, it may be a network socket. Once a value of that type is no longer used, it should “clean up” its resource by freeing the memory or closing the file or socket. This is the job of a destructor and, therefore, the job of Drop::drop.

4. Copy and Clone 

    ```rust
    If you have played around with Rust yourself a little bit, you may have tried to access a "moved value".

    let a = String::from("a");
    let b = a;
    println!("a = {a}");

    // error[E0382]: borrow of moved value: `a`
    // let a = String::from("a"); - move occurs because `a` has type `String`, which does not implement the `Copy` trait
    // let b = a;  - value moved here
    // println!("a = {a}"); value borrowed here after move

    remember you used to learn that a piece of data can only have one owner? when you write let b = a; in the example above, ownership of the data (String::from("a")) moves from the variable a into b after this move, the variable a is now essentially left empty in some sense. the compiler detects that you are trying to print the value in a. but that value has already moved elsewhere. you are not allowed to borrow (for printing) the value owned by a because it has since moved to b

    // Here is copy implmentation 
    values of types that implement the Copy trait can be copied implicitly by the compiler. if you implement Copy for a type T you promise to the compiler that it can duplicate any value of T by simply copying the bits. you could, in theory, copy the bits of any value, but as a programmer, you may decide that this is not a good idea. 🤔 try to think for yourself why our original example did not work.

    so if you cannot Copy a String, how do you duplicate a String if you want to? this is where the Clone trait comes in. the Clone trait can be implemented manually and do anything you wish as a programmer. in the case of String, it creates a new String object with its memory allocation and copies the text.

    let a = String::from("a");
    let b = a.clone(); // Explicitly create a duplicate.
    println!("a = {a}");
    ```

## Lesson Summary 

In this lesson, you’ve learned how to track data ownership to free up resources so that other programs may use them again. Rust achieves this by determining how values move from one variable to another at compile-time and preventing the use of values after they have moved away. The Drop trait allows a programmer to determine what happens to a value when it goes out of scope. Implementing the Copy trait for a type tells the compiler that copying bits are okay to duplicate values of that type. Sometimes copying bits is not alright, and any required custom duplication behavior can be implemented through the Clone trait.

