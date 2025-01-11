# Other Recoverable Errors

## Basic Concepts 

Within the class of recoverable errors, you have only handled one kind of error so far, namely the "missing value error." A missing value is encoded through `Option<T>`

To provide information about different kinds of errors, you need a different type that takes two generic parameters: one to describe a successful result and one to describe an error if it occurred. In this lesson, you will learn about the so-called `Result<T, E>` type exactly for this purpose.

## The Definition of Result<T, E>

1. The standard library implementation of `Result<T, E>`looks something like this 👉

    ```rust
    enum Result<T, E> {
        Ok(T),
        Err(E),
    }

    // Just like for Option<T> the Result<T E> type and its variants Ok(T) and Err(E) are imported automatically through the prelude.
    ```

2. Most Rust libraries define one or more error types to represent the possible errors that can occur

    `std::num::ParseIntError`  for integer parsing errors
    `std::io::Error`  for all kinds of errors concerning file and memory reading and writing.

    ```rust 
    pub fn from_str_radix(src: &str, radix: u32) -> Result<i32, ParseIntError>
    ```

3. Implementing Functionality That May Fail

    It is challenging to come up with a meaningful but concise example showing you how you could use the Result<T, E>  type if you were to develop a library yourself.

    The following example is a bit long, but a good exercise in reading Rust. It implements a function `prepare_bacon_and_eggs` which takes a map of ingredients and their availability, the stock, and returns either an instance of BaconAndEggs or an error detailing why the preparation failed.

    The code listing contains a few type definitions, a helper function `ensure_available`, the "library like" function `prepare_bacon_and_eggs` and finally `main` to demonstrate `prepare_bacon_and_eggs`

    ```rust 

    use std::collections::HashMap;

    #[derive(Debug, Copy, Clone, Eq, PartialEq, Hash)]
    enum Ingredient {
        Avocado,
        Bacon,
        Egg,
    }

    #[derive(Debug, Eq, PartialEq)]
    struct BaconAndEggs;

    #[derive(Debug, Eq, PartialEq)]
    enum PreparationError {
        NotEnoughOfIngredient {
            ingredient: Ingredient,
            required: u32,
            available: u32,
        },
        // Other possible errors that could occur would go here.
    }

    /// Helper function to check if stock contains enough of an ingredient.
    fn ensure_available( 
        stock: &HashMap<Ingredient, u32>,
        ingredient: Ingredient,
        required: u32,
    ) -> Result<(), PreparationError> {
        let available = stock.get(&ingredient).copied().unwrap_or_default();

        if available >= required {
            Ok(())
        } else {
            Err(PreparationError::NotEnoughOfIngredient {
                ingredient,
                required,
                available,
            })
        }
    }

    /// Attempts to prepare bacon and eggs with ingredients that are in stock. The
    /// stock is updated depending on the result.
    fn prepare_bacon_and_eggs (
        stock : &mut HashMap<Ingredient, u32>,
    ) -> Result<BaconAndEggs,PreparationError > {
        const REQUIRED: [(Ingredient, u32); 2] = [(Ingredient::Bacon, 1), (Ingredient::Egg, 2)];

            // Ensure everything is available.
        for (ingredient, required) in REQUIRED {
            // Notice the use of the error propagation operator here. You return from
            // `prepare_bacon_and_eggs` early if `ensure_available` returns an
            // error.
            ensure_available(stock, ingredient, required)?;
        }

            // Update the stock.
        for (ingredient, required) in REQUIRED {
            // Since you verified that the hashmap contains all ingredients above you
            // can unwrap here.
            *stock.get_mut(&ingredient).unwrap() -= required;
        }

        Ok(BaconAndEggs)
    }

    fn main() {
        // Create a map from ingredient to a count that represents availability.
        let mut stock: HashMap<Ingredient, u32> = [
            (Ingredient::Avocado, 3),
            (Ingredient::Bacon, 2),
            (Ingredient::Egg, 1),
        ]
        .into_iter()
        .collect();

        // You should expect an error because bacon and eggs requires 2 eggs but you only have 1 in stock.
        assert_eq!(
            prepare_bacon_and_eggs(&mut stock),
            Err(PreparationError::NotEnoughOfIngredient {
                ingredient: Ingredient::Egg,
                required: 2,
                available: 1,
            })
        );

        // Add half a dozen of eggs to the stock.
        *stock.get_mut(&Ingredient::Egg).unwrap() += 6;

        // Verify that you now get bacon and eggs.
        assert_eq!(prepare_bacon_and_eggs(&mut stock), Ok(BaconAndEggs));

        // Make sure that the stock has been updated.
        assert_eq!(
            stock,
            [
                (Ingredient::Avocado, 3),
                (Ingredient::Bacon, 1),
                (Ingredient::Egg, 5),
            ]
            .into_iter()
            .collect::<HashMap<_, _>>()
        )
    }

    ```

4. Why use Result<T, E> ?

    You may have been wondering why you need a complicated generic type like `Result<T, E>` at all. You could very well define our own combined "success or failure" type like so:

    ```rust
    enum PreparationResult {
        BaconAndEggs(BaconAndEggs),
        NotEnoughOfIngredient { /* ... */ },
    }
    ```

    However, if you were to do this, we would have to re-define all the convenience methods that are available for `Result<T, E>` like `map unwrap` and and so forth.

5. How many Error Types should be defined?

     It is a good question, and the answer is, as is often the case with good questions, "it depends." There is no objective answer to this question, only things to consider.

     If you were to create a distinct error type for every function in your library, you might allow consumers of your library only to handle errors that they need to. 👉 However, there are also some downsides. If you wanted to add an error kind to an existing error, that would be a breaking change. Also, as errors bubble up through an application, they often need to be wrapped in more generic errors. It is inconvenient as a consumer of your library to have to deal with all the different error types that functions in your library may return.

     There is a trade-off between preciseness, and future-proofing for API stability, performance, and usability to be made. ☝️ Nothing or no one could prescribe a single solution that is the best in all scenarios.


6. What Is So Great About Returning Errors By Value?

    Rust expects you to return errors by value from your functions through either `Option<T>` or `Result<T,E>`

    This is different from exception-based error handling. In quite a few currently popular programming languages, it is possible to "raise an error," which can then be "caught" by some parent function.

    While exception-based error handling may seem convenient at first, it is often challenging to figure out for a piece of code if and what exceptions could be thrown.

    From the calling side, it needs to be more obvious from the function signature what errors must be handled. The compiler also will not warn you if you forget to handle an error that you should manage. With Rust, you can see what errors you can expect.☝️ You can still decide not to handle them and have the program crash, but at least that is a clear choice.

    The type system will prevent you from using a value without handling an error because success values are wrapped inside an Option::Some(value) or Result::Ok(value)

## Lesson Summary 

Completing this lesson taught you that recoverable errors are encoded in the return types of functions, often using the standard `Option<T>` and `Result<T, E>` types