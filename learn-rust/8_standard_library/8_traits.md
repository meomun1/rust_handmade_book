# Traits 

## Basic Concepts 

A trait describes an interface that implementors of a trait must implement. Traits are used to abstract implementation details, so you can generalize the behavior. The Rust standard library provides a lot of traits. In this lesson, you will go over most of the categories of traits.

## From and Into trait 

1. Traits are closely related. They specify infallible value-to-value conversions.

    ```rust 
    pub trait From<T> {
        fn from(&self : T) -> Self; 
    }

    pub trait Into<T> {
        fn into(&self : T) -> Self; 
    }

2. If you implement `From<A>` for B then `Into<B>` is automatically implemented for A  thanks to the blanket implementation that looks something like this 👉

    ```rust 

    impl<A, B> Into<B> for A where B: From<A> {
        fn into(&self) -> B {
            B::from(self)
        }
    }
    ```

3. The From and Into  traits are useful when implementing conversions from a specific to a more generic type. 👇 For example:

    ```rust 
    #[derive(Debug)]
    enum DatabaseError {
        DatabaseClosed,
        ProtocolViolation,
    }

    #[derive(Debug)]
    enum ApplicationError {
        Database(DatabaseError),
        IO(std::io::Error),
    }

    impl From<DatabaseError> for ApplicationError {
        fn from(value : DatabaseError) -> Self {
            Self::Database(value)
        }
    }

    fn main() {
        // You can use `Into::into` to convert the `DatabaseError` into an `ApplicationError`.
        let app_err: ApplicationError = DatabaseError::ProtocolViolation.into();
    }

4. You should prefer to implement `From` as `Into` gets automatically derived. You should prefer to use `Into`  in generic parameter bounds because that will work even if only `Into` was implemented.

## TryFrom and TryInto

1. Provide an abstraction over fallible value-to-value conversions.

    ```rust 
    impl TryFrom<ApplicationError> for DatabaseError {
        type Error = ApplicationError;

        fn try_from(value : ApplicationError) -> Result<Self, Self::Error> {
            match value {
                ApplicationError::DatabaseError(value) => Ok(value),
                _ => Err(value),
            }
        }
    }

    fn main() {
        // You can use `Into::into` to convert the `DatabaseError` into an `ApplicationError`.
        let app_err: ApplicationError = DatabaseError::ProtocolViolation.into();

        // You can use `TryInto::try_into` to convert the `ApplicationError` back into a `DatabaseError`.
        let db_err: DatabaseError = app_err.try_into().unwrap();

        // You can not turn an `ApplicationError::IO` variant into a `DatabaseError`, so this conversion fails.
        let still_app_err: ApplicationError = DatabaseError::try_from(ApplicationError::IO(
            std::io::Error::new(std::io::ErrorKind::Other, "Something bad happened!"),
        ))
        .unwrap_err();
    }
    ```

## Display, ToString and FromStr

1. Types that implement `Display` can be formatted as a user-facing human-readable string. Implementing the `Display` trait for a type will automatically implement `ToString` for that type. The standard library recommends not implementing `ToString` manually. You can implement Display like 

    ```rust

    enum Color {
        Blue,
        Yellow,
    }

    impl std::fmt::Display for Color {
         fn fmt(&self, f: &mut std::fmt::Formatter<'_>) -> std::fmt::Result {
            f.write_str(match self {
                Color::Blue => "blue",
                Color::Yellow => "yellow",
            })
         }
    }

    fn main(){
        println!("{}", Color::Yellow); // yellow
    }

2. The `FromStr` trait should be implemented when you want to allow parsing a value from a string.

    ```rust 
    impl std::str::FromStr for Color {
        type Err = ();

        fn from_str(s: &str) -> Result<Self, Self::Err> {
            match s {
                "blue" => Ok(Color::Blue),
                "yellow" => Ok(Color::Yellow),
                _ => Err(()),
            }
        }
    }

    fn main() {
    // `str::parse::<T>` is defined if `T` implements `FromStr`.
        let color = "blue".parse::<Color>().unwrap(); // Color::Blue
    }

    // As shown in the example above, the FromStr implementation can be invoked through str::parse , which is implemented like so 👇

    impl str {
        pub fn parse<F: FromStr>(&self) -> Result<F, F::Err> {
            FromStr::from_str(self)
        }
    }

## AsRef and AsMut

1. Traits can be implemented for types that can be temporarily interpreted as another type.

    ```rust
    use std::path::{Path, PathBuf};

    fn print_path<P: AsRef<Path>>(path: P) {
        let path = path.as_ref();

        println!("{}", path.display());
    }

    fn main() {
        let a: &'static str = "static_str";
        print_path(a);

        let b: String = String::from("owned_string");
        print_path(b);

        let c: PathBuf = PathBuf::from("owned path");
        print_path(c);
    }
    static_str
    owned_string
    owned path
    ```

## Borrow and BorrowMut

1. Traits are similar to the previously discussed `AsRef` and `AsMut`. The function signatures of the traits are ever the same. `Borrow` differs from `AsRef` because types that implement `Borrow` provide additional semantic guarantees about how the borrowed references can be used. Precisely, when you implement `Borrow` in addition to `AsRef` for type `T`, you promise that for x:T and y:T

2. The compiler does not prevent you from implementing `Borrow` incorrectly. Here is an example of an incorrect implementation of `Borrow`: 

    ```rust

    use std::borrow::Borrow;

    #[derive(Default, Eq, PartialEq, PartialOrd, Ord, Hash)]
    struct StringBool {
        s: String,
        b: bool,
    }

    // There is no problem with implementing `AsRef<str>` or `AsRef<bool>`.
    impl AsRef<str> for StringBool {
        fn as_ref(&self) -> &str {
            &self.s
        }
    }

    // WARNING: Incorrect implementation of Borrow because it does not uphold the
    // `Eq`, `Ord` and `Hash` guarantees!
    impl Borrow<str> for StringBool {
        fn borrow(&self) -> &str {
            &self.s
        }
    }

    fn main() {
        let x = StringBool {
            s: String::from("Banana"),
            b: true,
        };
        let y = StringBool {
            s: String::from("Banana"),
            b: false,
        };
        let xs: &str = x.borrow();
        let ys: &str = y.borrow();
        assert_eq!((x == y), (xs == ys));
    }

    // assertion `left == right` failed
    // left: false
    // right: true
    // note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace

## Deref and DerefMut 

1. Traits can be implemented for types that wrap another type and where the wrapping should be "invisible." It is used for explicit dereferencing operations with the (unary) *  operator in immutable contexts. `Deref` is also used implicitly by the compiler in many circumstances. This mechanism is called `Deref coercion`. 😊 In mutable contexts, `DerefMut` is used 

2. 👉 Implementing `Deref` for smart pointers makes accessing the data behind them convenient, which is why they implement `Deref`. On the other hand, the rules regarding `Deref` and `DerefMut` were explicitly designed to accommodate smart pointers. Because of this, `Deref` should only be implemented for smart pointers to avoid confusion. For similar reasons, this trait should never fail. Failure during dereferencing can be highly confusing when `Deref` is invoked implicitly. Here is an example of how to implement `Deref` 

    ```rust
    struct DerefExample<T> {
    value: T
    }

    impl<T> std::ops::Deref for DerefExample<T> {
        type Target = T;

        fn deref(&self) -> &Self::Target {
            &self.value
        }
    }

    let x = DerefExample { value: 'a' };
    assert_eq!('a', *x);

## Closures 

1. Some traits help you take closures as parameters for functions you write. They are `Fn, FnOnce, and FnMut`. Unlike other traits, these traits have special syntax and are automatically implemented. They serve mostly as trait bounds.

    😀 This program shows you how to write a function that takes a closure that takes no arguments and produces a `&'static str`

    ```rust 
    fn twice<F: Fn() -> &'static str>(f: F) {
        println!("{} {}", f(), f());
    }

    fn main() {
        twice(|| "One"); // One One
    }

2. `FnMut`  allows you to take a closure that may mutate the variables it captures:

    ```rust
    fn twice<F: FnMut() -> &'static str>(mut f: F) {
        println!("{} {}", f(), f());
    }

    fn main() {
        let mut iter = ["One", "Two"].into_iter();
        twice(|| iter.next().unwrap()); // One Two
    }
    ```

3. `FnOnce` accepts closures that may only be invoked once:

    ```rust 

    fn once<F: FnOnce() -> String>(f: F) {
        println!("{}", f());
    }

    fn main() {
        let one = String::from("One");
        once(move || one); // One
    }

## Operator Overloading 

It is possible to implement modification, assignment, and indexing operators for custom types through the arithmetic and comparison traits.

☝️ You may have a closer look at the `std::ops` via link : https://doc.rust-lang.org/std/ops/index.html

## Comparison 

1. Rust provides traits such as `Eq`, `PartialEq`, `Ord`, `PartialOrd`, and `Hash` to implement comparisons between different instances of the same type and even other types.

2. For more details about sdt::cmp follow the link : https://doc.rust-lang.org/std/cmp/index.html

## Iteration

1. The ability to iterate over collections of values is essential in programming. Rust provides several traits related to iteration. The most important ones to know are `Iterator` and `IntoIterator`. Types that implement `Iterator` are iterators, and types that implement `IntoIterator` can be automatically converted into iterators.

    ```rust 
    struct CountDown(usize);

    impl Iterator for CountDown {
        type Item = usize;

        fn next(&mut self) -> Option<Self::Item> {
            if self.0 == 0 {
                None
            } else {
                self.0 -= 1;
                Some(self.0)
            }
        }
    }

    fn main() {
        let mut iter = CountDown(3);

        assert_eq!(iter.next(), Some(2));
        assert_eq!(iter.next(), Some(1));
        assert_eq!(iter.next(), Some(0));
        assert_eq!(iter.next(), None);
        assert_eq!(iter.next(), None);
    }
    ```

    The `IntoIterator` trait can be implemented like so:

    ```rust 

    struct DoubleRange(usize);

    impl IntoIterator for DoubleRange {
        // The type of the item the iterator will produce.
        type Item = usize;

        // The type of the iterator that we will create.
        // For this example you are reusing `std::ops::Range` which implements `Iterator`.
        type IntoIter = std::ops::Range<usize>;

        fn into_iter(self) -> Self::IntoIter {
            self.0..self.0 * 2
        }
    }

    fn main() {
        let mut iter = DoubleRange(3).into_iter();
        assert_eq!(iter.next(), Some(3));
        assert_eq!(iter.next(), Some(4));
        assert_eq!(iter.next(), Some(5));
        assert_eq!(iter.next(), None);
        assert_eq!(iter.next(), None);
    }

2. There are additional traits, like `FusedIterator` and `DoubleEndedIterator`, which you can consider implementing if you're writing a library and need extra capabilities or performance.

    You may dive into the topic via the link: https://doc.rust-lang.org/std/iter/index.html

## IO

1. The `std::io` module contains the `Read` and `Write` traits which abstract synchronous reading and writing of bytes from and to memory, files, network sockets, and anything else that can consume or produce bytes.

    😊 The traits need to be imported into scope because they are not part of the prelude.

    ```rust 
    // The essence of `Read`, there are more methods available with default implementations.
    pub trait Read {
        fn read(&mut self, buf: &mut [u8]) -> Result<usize>;
    }

    // The essence of `Write`, there are more methods available with default implementations.
    pub trait Write {
        fn write(&mut self, buf: &[u8]) -> Result<usize>;
    }
    ```

    As you can see, the `read` function takes a byte slice where the read bytes can be placed and returns how many bytes were read or an IO error if that occurred.

    If you know how many bytes you want to read up-front, you can call `read_exact` instead, which will call `read` in a loop.

    The `write` function takes a byte slice that you want to be written and returns how many bytes were written or an IO error if an error occurred.

    😊 If you want to write all the bytes in the provided buffer, you can use `write_all`, which calls `write` in a loop.

## Error

1. When implementing your error types, it is considered good practice also to implement the standard library's `std::error::Error` trait. Doing so will allow the standard library and other libraries to report errors to the user.

    ```rust 
    #[derive(Debug)]
    enum Error {
        A,
        B,
    }

    impl std::fmt::Display for Error {
        fn fmt(&self, f: &mut std::fmt::Formatter<'_>) -> std::fmt::Result {
            f.write_str(match self {
                Self::A => "a happened",
                Self::B => "b happened",
            })
        }
    }

    // The `Error` trait requires `Debug` and `Display` and it's trait methods are optional.
    impl std::error::Error for Error {}

    fn main() -> Result<(), Box<dyn std::error::Error>> {
        Err(Error::B)?; // Error: B
        Ok(())
    }

2. The `Error`  trait allows specifying a more specific error that caused the current error and a back trace. Doing so manually is error-prone and time-consuming. 😊 Community-maintained crates like `thiserr` and `eyre` can greatly reduce the burden of writing custom error types.


## Lesson Summary 

In this lesson, you got acquainted with Traits. Traits are an abstract definition of shared behavior among different types. You have looked through a few traits that provide some conversion.

// impl From for B 

// impl TryFrom for B 

// if it is impossible to implement...

// FromStr

// Display should be implemented for types that have ...

// AsRef and AsMut

// Borrow and BorrowMut 

// Deref and DerefMut

// The program will not compile 

// These traits provide an interface for reading from, writting to anything...