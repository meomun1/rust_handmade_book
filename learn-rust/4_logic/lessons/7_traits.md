# Traits

## Basic Concepts

1. When writing abstractions, you need some method of requiring types to expose some kind of interface or contract. In Rust, you do this through traits.

## Trait 

1. The standard library defines many useful traits, such as the Display trait.

    ```rust 
    trait Display {
        fn fmt(&self, f: &mut Formatter<'_>) -> Result;
    }

    // implement Display trait for bool type 
    impl Display for bool {
        fn fmt(&self, f: &mut Formatter<'_>) -> Result {
            Display::fmt(if *self { "true" } else { "false" }, f)
        }
    }

    // implement Display trait for string type 
    impl Display for str {
        fn fmt(&self, f: &mut Formatter<'_>) -> Result {
            f.pad(self)
        }
    }

## Bounds 

1. Earlier in this lesson, you’ve left off with an example that did not compile:

    ```rust 
   struct MyStruct<A, B> {
        a: A,
        b: B,
    }
    impl<A, B> MyStruct<A, B> {
        fn print(&self) {
            println!("a: {}, b: {}", self.a, self.b);
        }
    }
    
    // error[E0277]: `A` doesn't implement `std::fmt::Display`

    use std::fmt::Display;

    impl<A: Display, B: Display> MyStruct<A, B> {
        fn print(&self) {
            println!("a: {}, b: {}", self.a, self.b);
        }
    }

2. If the trait bounds get too complicated, you can move the bounds to a `where` clause 

    ```rust
    impl<A, B> MyStruct<A, B>
    where
        A: Display,
        B: Display {
    ```

## Derive 

1. Some trait implementations may be very predictable. They can be derived based on the type definition. For example, let's take the `Debug` trait. The `Debug` trait is very similar to `Display`, except that it is meant to accurately convey the value rather than accurately presenting a human-friendly string. Almost all types in the standard library implement `Debug`

    ```rust
    // formatting option like so 
    println!("{:?}", "Hello");
    println!("{:?}", vec!["Hello", "World"]);

2. You can derive a Debug implementation with a procedural macro defined by the standard library as follows:

    ```rust 
    #[derive(Debug)]
    struct Persion {
        name : String, 
        age : i32, 
    }

    let mick = Person {
        name : "Mick".to_string(),
        age : 30 
    };

    println!("{:?}", mick);

    // Person { name: "Mick", age: 30 }

3. Quite a few traits from the standard library can be derived. You will explore them further within the course.

## Lesson Summary 

You now know what traits are and how to declare, implement and use them as generic type parameter bounds. There is another use for traits, which is dynamic dispatch. You also know that some trait implementations do not have to be written manually and can be derived through procedural macros.