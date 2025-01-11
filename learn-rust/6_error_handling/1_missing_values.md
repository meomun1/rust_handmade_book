# Missing Values

## Basic Concepts

One of the most frequent kinds of error handling that you will encounter in a program is the one where a value may or may not be available. ☝️ In some programming languages, the possibility of a value missing is allowed implicitly. In Rust, this possibility is encoded through the type system. This makes it so that a programmer will know when to handle a possibly missing value at compile time.

## Missing values

1. To illustrate what a missing value is, let's try to write a function first_element

    ```rust
    fn first_element<T>(array : &[T]) -> &T {
        if array.len > 0 {
            &array[0]
        } else {
            unimplemented!("What should be return here?")
        }
    }

    // The unimplemented! (message), the macro will abort the program at run-time and print the message
    ```

2. How to deal with it. You need to return a value to make your first_element function work when the input array contains no elements. However, what should you return when the input array contains no elements?

    You could do multiple things:

    `Require T to have a Default implementation and return the default value of T`

    `Write the result back through a mutable reference passed into the function and return a bool to indicate whether a value was written `

    `Or abort the program`

    The second one is closest in spirit to what you are going to do, except you will not accept any extra parameters. 👉 Instead, wrap the return type in an Option<T>

    ```rust
    #[derive(Debug)]
    enum Option<T> {
        Some(T),
        None
    }

    fn first_element<T>(array: &[T]) -> Option<&T> {
        if array.len > 0 {
            Option:Some(&array[0])
        } else {
            Option:None
        }
    }

    fn main() {
        let a = [1, 2, 3];
        let first_from_a = first_element(&a);
        println!("{first_from_a:?}");

        let b: [i32; 0] = [];
        let first_from_b = first_element(&b);
        println!("{first_from_b:?}");
    }

    // Some(1)
    // None
    // The defined above Option<T> is already specified in the standard library.
    // It is also readily available because Option<T> and its variants Some(T) and None  are included in the prelude. The prelude is a Rust module automatically imported for every Rust source file. The types and traits in the prelude are used so often that it is convenient for them to be available without having to import them explicitly.
    ```

## Examples for Option -> Some | None

    ```rust
    /// The book type provided by an external API.
    #[derive(Debug)]
    struct APIBook {
        title: String,
        description: Option<String>,
    }

    /// The book type you need in the rest of your program.
    #[derive(Debug)]
    struct Book {
        title: String,
        description: String,
    }

    fn main(){
        // The book objects you "received" from an API.
        let api_books: Vec<APIBook> = vec![
            APIBook {
                title: "Samson and Rik".to_string(),
                description: Some("Samson and Rik go on many adventures.".to_string()),
            },
            APIBook {
                title: "De Kameleon".to_string(),
                description: None,
            },
        ];

        println!("api_books: {api_books:#?}");

        // The book objects you would like to use throughout the rest of your program.
        let books : Vec<Book> = api_books
            .into_iter()
            .fiter_map(|api_book|{
                // Deconstruct the APIBook into its parts.
                let APIBook { title, description } = api_book;

                // Return None if description is None, otherwise take the String out of the `Option<String>`.
                let description = match description {
                    Some(description) => description,
                    None => return None,
                };

                // Create Book from the parts.
                Some(Book { title, description })       
            })
            .collect::<Vec<_>>();
        
        println!("books: {books:#?}");


        // This propram will output 

        api_books: [
            APIBook {
                title: "Samson and Rik",
                description: Some(
                    "Samson and Rik go on many adventures.",
                ),
            },
            APIBook {
                title: "De Kameleon",
                description: None,
            },
        ]
        books: [
            Book {
                title: "Samson and Rik",
                description: "Samson and Rik go on many adventures.",
            },
        ]

        // The filter_map iterator adapter takes an iterator with items T and a closure that maps T to Option<U> The resulting iterator will contain all the None items U in this case, you have iterator of APIBook. The closure takes these APIBook items and turns them into Option<Book>. The resulting iterator yields all Some(Book) as Book and leaves out all the None that the closure returned 

    }
    ```

## Lesson Summary

The idea of encoding errors in the return types stem from functional programming languages like Haskell. The Option<T> and Result<T E> types are inspired by the Maybe<T> and Either<E> monads. There is a danger hidden in learning about this method of error handling: once you know it, you may start to miss it in other programming languages!