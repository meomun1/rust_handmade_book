# References and Borrowing

## Pass By Value

    ```rust 
    fn length_of_string(value: String) -> usize {
        value.len()
    }
    fn main() {
        let s1 = String::from("Hey there!");
        let len = length_of_string(s1);
        println!("The length is {len}.");
    }
    // The length is 10.
    ```
1. You may wonder: "the length of what is 10?". 😀 Let's try to also print the string of which you computed the length.

    ```rust 
    fn main() {
        let s1 = String::from("Hey there!");
        let len = length_of_string(s1);
        println!("The length of {s1:?} is {len}.");
    }
    // This will not compile. Can you determine why?
    // Because value of s1 is moved to len so now len is the owner of the value 

    fn main() {
        let mut s1 = String::from("Hey there!");
        let s2 = s1.clone();
        s1.push_str(" How are you?");
        let len = length_of_string(s1);
        println!("The length of {s2:?} is {len}.");
    }
    // Output : The length of "Hey there!" is 23. ( 23 is length of "Hey there! How are you? )
    // The output is not right, and you can obviously see from the code why. In larger projects, creating duplicates of data can cause you to have an outdated view.

2. Returning Ownership. 

    Another solution to the same problem could be to return the 
    ```rust
    fn length_of_string(value: String) -> (String, usize) {
        let len = value.len();
        (value, len)
    }
    fn main() {
        let s1 = String::from("Hey there!");
        let (s1, len) = length_of_string(s1);
        println!("The length of {s1:?} is {len}.");
    }
    // Output The length of "Hey there!" is 10.
    // This also works, and you do not have to create a duplicate. 😐 Unfortunately, the code is not very nice to read or write, especially if multiple parameters exist.
    ```

## Pass by Reference 

    You can accept a reference to a String

    ```rust
    // v - Require a reference to a `String`.
    fn length_of_string(value: &String) -> usize {
        value.len()
    }

    fn main() {
        let s1 = String::from("Hey there!");
        // v - Take a reference to `s1`.
        let len = length_of_string(&s1);
        println!("The length of {s1:?} is {len}.");
    }

    // Output The length of "Hey there!" is 10.
    // You have been able to pass a value by reference and query its length. 


    // Another example 
    fn append_world(value: &String) -> usize {
        value.push_str(", World!")
    }

    fn main() {
        let s1 = String::from("Hello");
        append_world(&s1);
        println!("The value is now {s1:?}.");
    }
    // 😐 Unfortunately, this code does not compile:
    // To modify a borrowed value, you need to take a mutable reference. The compile indicates exactly how to fix our program.
    // vvvv - Require a mutable reference.
    fn append_world(value: &mut String) {
        value.push_str(", World!");
    }
    fn main() {
        // vvv - Allow s1 to be mutated.
        let mut s1 = String::from("Hello");
        //           vvvv - Take a mutable reference.
        append_world(&mut s1);
        println!("The value is now {s1:?}.");
    }

    ```

1. Borrowing rules 

    The rules are:

    `There can exist multiple immutable references at one time.`

    `But there may only exist one reference at a time if that reference is mutable.`

    ```rust
    fn main() {
        let mut s1 = String::from("Hello");
        let w1 = &mut s1;
        let w2 = &mut s1;
        // The mutable references `w1` and `w2` can not co-exist here!
        append_world(w1);
        println!("The value is now {w2:?}.");
    }

    fn main() {
        let mut s1 = String::from("Hello");
        let w1 = &s1;
        let w2 = &mut s1;
        // The mutable references `w1` and `w2` can not co-exist here!
        append_world(w1);
        println!("The value is now {w2:?}.");
    }    
    // these examples does not compile

2. Data races 

    The main reason the borrowing rules exist is to prevent data races. Unfortunately, there are many definitions of the term data race, consider the definition from the Rust book. 

    The authors point out that the restriction preventing multiple mutable references to the same data at the same time allows for mutation, but in a very controlled fashion. It’s something that new Rustaceans struggle with because most languages let you mutate whenever you’d like. The benefit of having this restriction is that Rust can prevent data races at compile time.

    Data races cause undefined behavior and can be difficult to diagnose and fix when you’re trying to track them down at runtime; Rust prevents this problem by refusing to compile code with data races!

3. Dangling References 

    😀 In languages with pointers, it’s easy to erroneously create a dangling pointer - a pointer that references a location in memory that may have been given to someone else - by freeing some memory while preserving a pointer to that memory. In Rust, by contrast, the compiler guarantees that references will never be dangling references. If you have a reference to some data, the compiler will ensure that the data will not go out of scope before the reference to the data does.

    ```rust
    fn main() {
        let reference_to_nothing = dangle();
    }
    fn dangle() -> &String {
        let s = String::from("hello");
        &s
    }

    // This won't work
    // We can fix with 
    fn no_dangle() -> String {
        String::from("hello")
    }

4. Non-Lexical Lifetimes

    Note that a reference’s scope starts from where it is introduced and continues through the last time that reference is used. 😀 For instance, this code will compile because the last usage of the immutable references, the println!, occurs before the mutable reference is introduced:

## Lesson Summary 

You have learned how to share data in Rust. If you are not used to thinking about sharing and mutability, it will take effort and more reading before you are comfortable. That is okay because the Rust compiler is helping you become a better software engineer.