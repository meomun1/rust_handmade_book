# Variables and Mutability


## Variables and Mutability

1. By default, variables are immutable in Rust. But you still have the option to make them mutable.

    ```rust
    fn main() {
        // Declare a variable without initializing it
        let x;
        x = 2;

        // Declare and initialize a variable in one line
        let x = 2;

        // Declare a variable with type ascription and initialize it
        let x: i16 = 2;
    }
    ```

## Mutability

1. To declare a mutable variable, you can use let mut
    ```rust
    fn main(){
        let mut x = 2; 
        x = 3;
    }
    ```


## Scoping

1. scope to refer to a piece of code in which variables live. Rust allows you to use variables declared in outer scopes, but variables do not leak out of their scope

    ```rust
    fn main() {
    let x = 2;
    println!("{}", x); // 2
    // Create a new scope
    {
        let y = 3;
        // We can use x here
        println!("{}", x); // 2
        println!("{}", y); // 3
    }
    println!("{}", x); // 2
    // println!("{}", y); // won't compile because y is "not in scope"
    }
    ```

## Shadowing

1. It is allowed to redefine a variable with the same name

    ```rust
    let x = 2;
    let x = 3;
    ```

2. This is also allowed in a nested scope.

    ```rust
    let x = 2;
    {
        let x = 3;
    }
    println!("{}", x); // 2
    ```

## Patterns 

1. The let statement is quite powerful. ☝️ You can provide a pattern instead of just the variable name.

    ```rust
    let (x, y) = (2, 3);
    ```

2. The parenthesis in the above statement is not a particular language construct. You are just destructuring a tuple. You can also destructure structs.

    ```rust
    struct Person {
        name: &'static str,
        age: u32,
        likes_brownies: bool,
    }
    // Create a `Person` from its parts.
    let p = Person {
        name: "Mick",
        age: 30,
        likes_brownies: true,
    };
    // Deconstruct the `Person` back into its parts,
    // omitting fields other than `name` and `age`.
    let Person {
        name, age, ..
    } = p;
    ```

## Summary 

In this lesson, you’ve learned that variables in Rust can be declared through the let statement. Got acquainted with different variables and looked through their syntax. Learned how to provide a pattern instead of just the variable name.