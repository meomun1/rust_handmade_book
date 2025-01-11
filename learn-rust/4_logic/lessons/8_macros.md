# Macros 

## Basic Concepts 

1. This lesson will cover everything you need to know about Rust macros, including the kinds of macros in Rust, and a demonstration of how to use Rust macros with examples.

2. Macros are a powerful utility that lets you write less repetitive code. There are multiple kinds of macros: `declarative macros` and `procedural macros`

## Declarative Macros 

1. `Declarative macros` strongly resemble functions because they can take parameters and produce some output. Unlike functions, declarative macros match patterns against pieces of code provided to the macro, and substitute the input code with new code.

    ```rust 
    you are familiar with the println! macro, the ! mark at the end signals that you are invoking a declarative macro.

    Declarative macros can be defined with the macro_rules!

    // Exmple create macro with macro_rules! 
    macro_rules! create_vec {
        ( $( $item:expr ), * ) => {
            let mut result = Vec::new();
            $(
                result.push($item);
            )*
            result 
        }
    }

    let items = create_vec!(1, 2, 3);
    printl!("{item:?}");

    // [1, 2, 3]

    the standard library provides the vec! macro, which has the same objective but a better implementation. you should have isntead of create_vec! above
    ```

## Procedural Macros

1. Procedural macros allow transforming input code into any new code. You can define your procedural macros, but this topic will not be treated in this course, as you will likely not need to know how to do this to write good Rust programs. Procedural macro definitions must reside in their crate with a particular crate type.

2. Custom Derive Macros 

    ```rust 
    // exmaple of a custome derive macro is Default
    // For any type, you can derive a Default trait implementation if all of its components also implement 

    #[derive(Default)]
    struct MyType {
        name: String,
        items: Vec<i32>,
    }
    // Create a default instance of MyType
    let v = MyType::default();
    // Check that the default instance has empty fields
    assert!(v.name.is_empty());
    assert!(v.items.is_empty());

    // Manual implementation of the Default trait (redundant due to #[derive(Default)])
    impl Default for MyType {
        fn default() -> Self {
            Self {
                name: Default::default(),
                items: Default::default(),
            }
        }
    }

3. Derive multiple traits implementations 

    ```rust 
    #[derive(Debug, Clone, Default, Eq, PartialEq)]
    struct MyType {
        name: String,
        items: Vec<i32>,
    }

    let v1 = MyType::default(); // Uses Default to instantiate a default value.
    let v2 = v1.clone(); // Uses Clone to create a clone of v1.
    assert_eq!(&v1, &v2); // Uses Eq to compare the two values for equality.
    println!("{v1:#?}"); // Uses Debug to print the value.

4. Attribute-Like Macros 

    ```rust
    // Attribute-like macros can be applied to any item.
    #[my_attr_macro]
    fn x() {}

    #[my_attr_macro]
    const Y: u32 = 1;

    #[my_attr_marco]
    struct Z;

5. Function-Like Macros

    ```rust
    "function-like macros" can generate any code from its input code. they are invoked in exactly the same way as declarative macros.

    my_fn_macro!(some stuff);

## Lesson Summary 

To learn more about macros, you may check the

https://doc.rust-lang.org/book/ch19-06-macros.html#macros

https://doc.rust-lang.org/reference/macros.html

https://danielkeep.github.io/tlborm/book/index.html

In this lesson, you become familiar with two classes of macros: declarative and procedural. You have seen what declarative macros implementations look like.