# Generics

## Basic concepts 

1. The term generics is used as a method to facilitate generic programming. Generic programming usually means implementing functionality that can be used in many situations.

2. Generics is one solution to generic programming. It allows writing an implementation using types that only know how they behave, but not exactly what type it is.

## Generic Type Parameter 

1. What you want to be able to do is for the sequence type to take the types of the items in the sequence as a parameter. 👉 This is possible with Rust

    ```rust

    struct Sequence3<T> {
        first : T,
        second : T,
        third : T,
    }

    impl<T> Sequence3<T> {
        pub fn new(fisrt : T, second : T, third : T) -> Self {
            Self{first, second, third};
        }
    }

2. Unfortunately, binary operation `==` cannot be applied to type `T.`

    ```rust

    impl<T> Sequence3<T> {
        pub fn all_same(&self) -> bool {
            self.first == self.second 
            && self.second == self.third
        }
    }

3. Traits can be used in generic type parameter bounds written as `T : Trait1 + Trait2 + ... `. For exmaple, with `PartialEq` trait can be compared with binary `==` operator. 

    ```rust
    use std::cmp::PartialEq;

    // For all types T implementing PartialEq, implement for Sequence3<T> ...
    impl<T: PartialEq> Sequence3<T> {
        pub fn all_same(&self) -> bool {
            self.first == self.second && self.second == self.third
        }
    }

    // Another version 
    impl<T> Sequence3<T> where T: PartialEq {
        pub fn all_same(&self) -> bool {
            self.first == self.second && self.second == self.third
        }
    }
    ```

4. Associated Types in Trait Bounds. 

    ```rust
    // a so-called associated type 
    use std::ops::Add;

    // Output that describes the result type of the addition
    impl<T> Sequence3<T> where T: Copy + Add<Output = T> {
        pub fn sum(&self) -> T{
            self.first + self.second + self.third 
        }
    }
    ```

5. Using Multiple Generic Type Parameters

    ```rust
    struct MyStruct<A, B> {
    a: A,
    b: B,
    }

    enum MyEnum<A, B> {
        A(A),
        B(B),
    }

    fn main() {
        let s = MyStruct { a: 10, b: "Hello" };

        // We have to specify the type of the `MyEnum::A` variant here because Rust does not have
        // information to infer it.
        let e = MyEnum::<i32, _>::B("Hello");
    }
    ```

6. Generic Functions 

    ```rust
    fn say_hello<T: std::fmt::Display>(value: &T) {
        println!("Hello, {value}!");
    }

    fn main() {
        say_hello(&true); // Hello, true!
        say_hello(&String::from("World")); // Hello, World!
        say_hello(&1337); // Hello, 1337!
    }

## Lessson Summary 

Generic type parameters allow writing types and functions that work for multiple types. You will find this used in many places, for example, in the 
Vec<T>
 type from the standard library. In generic implementations, it is often helpful to require generic types to implement particular behavior so that the implementation can use that behavior. This is realized through generic type parameter bounds and traits.