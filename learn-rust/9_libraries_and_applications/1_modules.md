# Modules 

## Basic Concepts

Most software projects are composed of many libraries and a few applications which build upon those libraries. This lesson shows how to `organize code on a high level` and `enable the reuse` of pieces of software.

## Exporting items 

1. Example of a declaration and use module 👇

    ```rust 
    // declarate the module
    mod module1 {
        struct Value(i32);
        pub fn get_string() -> String {
            String::from("Hello world!")
        }
        fn get_string_private() -> String {
            String::from("Hello world!")
        }
    }
    fn main() {
        println!("{}", module1::get_string())

        //Non-runable example how of gettings access to private field
        //println!("{}", module1::get_string_private());
    }
    ```

    In this example, one module was declared in a single file and then used. It is possible to move the module to a separate file. In this case, the name of the module is the name of the file. 😊 For example, create a new file, "module1.rs," and put the module from the previous example:

    ```rust 
    struct Value(i32);
    pub fn get_string() -> String {
        String::from("Hello world!")
    }
    fn get_string_private() -> String {
        String::from("Hello world!")
    }
    ```

    👉 Now try to use it in the main.rs, but at first, declare the module and then use it:

    ```rust
    mod module1;
    fn main() {
        println!("{}", module1::get_string())
    }
    ```

    For more significant modules, you can move to a separate directory. Create the directory "module1" and put the file "mod.rs" with the content of "module1.rs" (and remove it). If needed to get referred to the root of the file, use "super":

    ```rust 

    pub fn get_string() -> String {
        String::from("Hello world!")
    }
    mod mod1 {
        pub fn get_string() -> String {
            super::get_string()
        }
        pub mod mod2 {
            pub fn get_string() -> String {
                super::get_string()
            }
        }
    }
    fn main() {
        println!("{}", mod1::get_string());
        println!("{}", mod1::mod2::get_string());
    }

## Submodules 

Add the "submodule.rs" to the directory "module1". To use that, declare this module in the "mod.rs": mod submodule; Submodule can be used in the "mod.rs," but it is available only inside the "mod.rs." It is suitable for the open-closed principle of SOLID, but to open it for users of this module, try to make it pub: Pub mod submodule;

## Importing Items. Crate 

1. The crate is a library or executable with a tree of modules. 😊 For example, the "hello world" of the last example is an executable crate. An executable and library crate can use any count of library crates, but a library crate cannot use an executable crate. Example how to use. The Rust std library has to create "thread." Try to use this crate to get the thread id: std::thread::current().id();

    ```rust 
    use std::thread;
    fn main() {
        println!("{:?}", thread::current().id());
    }

    crate can be renamed 

    use std::thread as my_name;
    fn main() {
        println!("{:?}", my_name::current().id());
    }

## Using External Crates 

1. To use external crates - connect it to the "Cargo. toml" in the section "[dependencies]."

    The most popular ways to connect:

    `way 1`: [dependencies] time = "0.3". You have connected the crate "time" version 0.3. In this case, cargo may use a younger version but less than 1.0.0.

    `way 2`: The second way is using git: [dependencies]
    `time = { git = "https://github.com/time-rs/time.git" }`

    `way 3`: It is possible to connect the crate from the local path: [dependencies]
    `time = { path = "../time" }`

2. ❗️ Take into account then the cargo will cache crates from crates.io and git. To check updates, use the command "cargo update."

3. How does cargo search crate in path or git? It looks for the "Cargo. toml" with: [package] name = "name_of_crate"

## Lesson Summary 

In this lesson, you learned how to declare and use a module. Considered the way of importing items and got acquainted with external crates.