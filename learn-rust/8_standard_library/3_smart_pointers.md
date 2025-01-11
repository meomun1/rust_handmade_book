# Smart Pointers

## Smart Pointer Definition

1. `A Pointer` is a general concept for a variable that contains an address in memory. This address refers to, or "points at," some other data.

2. `Smart Pointers` are pointers with additional capabilities and, to implement those capabilities, may store additional data. Smart pointers are a concept used not only in Rust, but also in other programming languages.

3. Rust has a variety of smart pointers defined in the standard library that provide functionality beyond that provided by references. To explore the general concept, 😀 let's look at several examples of smart pointers.

    The reference counting smart pointer type allows data to have multiple owners by keeping track of the number of owners and, when no owners remain, cleaning up the data. The `String` and `Vec<T>` types are technically smart pointers: they own some memory and allow you to manipulate it. They also have metadata and extra capabilities or guarantees. `String` stores its capacity as metadata and has the extra ability to ensure its data will always be valid UTF-8. Smart pointers are usually implemented using structs. Unlike an ordinary struct, smart pointers implement the `Deref` and `Drop` traits

    `Deref` trait allows an instance of the smart pointer struct to behave like a reference so the code can be written to work with either references or smart pointers.

    `Drop` trait allows customizing the code that's run when an instance of the smart pointer goes out of scope.

4. ☝️ Considering that the smart pointer pattern is a general design pattern used frequently in Rust, this lesson will not cover every existing smart pointer. Many libraries have smart pointers, and you can write your own.

    The most common smart pointers in the standard library are:

    `Box<T>` : for allocating values on the heap.

    `Rc<T>` : a reference counting type that enables multiple ownership.

    `Ref<T>` and `RefMut<T>` accessed through `RefCell<T>` , a type that enforces the borrowing rules at runtime instead of compile time.

    In addition, you’ll look at the interior mutability pattern, where an immutable type exposes an API for mutating an interior value. Discuss reference cycles: how they can leak memory, and how to prevent them.

## Box<T>

1. The most straightforward smart pointer is a box whose type is written `Box<T>`. Boxes allow storing data on the heap rather than the stack. What remains on the stack is the pointer to the heap data.

    Here is how to create an integer on the heap 👉

    ```rust
    let b = Box::new(10);
    println!("{}", b); // 10
    ```

    ☝️ Access the data in the box as similar, as if this data were on the stack. Like any owned value, when a box goes out of scope, as `b` does at the end of the main, it will be deallocated. The deallocation happens for the box (stored on the stack) and the data it points to (stored on the heap).

2. Storing Unsized Types

    Previously Compound Types, slices [T]  were introduced, mentioning that they were "unsized" and could not be stored on the stack. Indeed, the following function will not compile:

    ```rust
    fn create_data(small: bool) -> [u8] {
        if small {
            [1, 2, 3][..]
        } else {
            [1, 2, 3, 4, 5][..]
        }
    }
    // fn create_data(small: bool) -> [u8] {
    // ^^^^ doesn't have a size known at compile-time
    ```

    Put unsized type behind the reference, which stores both the address and the number of elements in the slice. ❗️ To return the owned slice - use Box<T>

    ```rust 
    fn create_data(small: bool) -> Box<[u8]> {
        if small {
            Box::new([1, 2, 3])
        } else {
            Box::new([1, 2, 3, 4, 5])
        }
    }
    fn main() {
        let data = create_data(false);
        println!("{data:?}"); // [1, 2, 3, 4, 5]
    }
    ```

    The `Box<T>` type is a smart pointer because it implements the `Deref` trait, which allows `Box<T>` values to be treated like references 

    When a `Box<T>` value goes out of scope, the heap data that the box points to is also cleaned up because of the `Drop`  trait implementation. These two traits will be even more critical to the functionality provided by the other smart pointer types will be discussed in the rest of this chapter.

3. Preventing Large Copies on Moves => Let’s explore these two traits in more detail.

    ```rust
    // This value is 4000 bytes large.
    let a = [0f32; 1000];
    // Imagine you move it into some function
    pass_it_to_me(a);

    // By moving the value into the heap, the pointer will be copied.
    let a = Box::new(0f32; 1000);
    pass_it_to_me_boxed(a);

4. Storing Trait Objects 

    In Rust, behavior is abstracted through traits. It is possible to make the parameter's type generic or accept a trait object when taking parameters that implement some behavior you want to use. The former will be resolved at compile time, whereas the latter does not know the actual type that will be passed in. Here is an example illustrating the two methods 👇

    ```rust
    trait Doubler {
        fn double(&self, value : i32) -> i32;
    }

    struct Simple;

    impl Doubler for Simple {
        fn double(&self, value : i32) -> i32 {
            value * 2
        }
    }

    struct Logged;

    impl Doubler for Logged {
        fn double (&self, input : i32) -> i32 {
            let output = input * 2;
            println!("Doubling {input} gives {output}");
            output
        }
    }

    // The compiler will generate a new function for each type D.
    // That also means that it can perform optimizations taking the implementation of D into account.

    fn static_double<D: Doubler>(doubler: D, value: i32) -> i32 {
        doubler.double(value)
    }

    // The compiler will only generate a single implementation.
    // The function to be invoked is be stored in a virtual method table.
    // The reference `&dyn Doubler` contains a pointer to the instance and to the vtable.
    fn dynamic_double(doubler: &dyn Doubler, value: i32) -> i32 {
        doubler.double(value)
    }

    fn main() {
        println!("{}", static_double(Simple, 1));
        println!("{}", static_double(Logged, 3));
        println!("{}", dynamic_double(&Simple, 5));
        println!("{}", dynamic_double(&Logged, 7));
    }
    ```

    You cannot store an owned value of a `dyn Doubler`.  because you do not know the size of values of the actual type that will be used at run-time. The compiler will complain if you try to do this 👇

    ```rust
    trait Doubler {}
    struct Simple;
    impl Doubler for Simple {}
    let x = Simple as dyn Doubler;

    // error[E0620]: cast to unsized type: `Simple` as `dyn Doubler`
    // help: consider using a box or reference as appropriate

    trait Doubler {}
    struct Simple;
    impl Doubler for Simple {}
    let x: Box<dyn Doubler> = Box::new(Simple);
    ```

## Rc<T> and Arc<T>

1. ☝️ Sometimes it is helpful to share ownership of a value. Most of the time, this can be achieved with immutable references. If cyclic references, like in a graph, are desired, then references cannot even be used.

2. Even if cyclic references are not required, using references for sharing data can make the code much less ergonomic to work with. Rust provides reference shared pointers through `Rc<T>` and `Arc<T>` for multithreaded scenarios. These shared pointers can model shared ownership. 👉 Because ownership is shared, the value held by an `Rc<T>`  is immutable. If mutability is required -use other types that implement internal mutability, like `RefCell<T>`. The reference will not be discussed counting or how `Rcv<T>` is implemented. Imagine modeling a room with a light and a person trying to read a book. The light must be on for the person to be able to read the book.

    ```rust
    struct Light {
        on: bool,
    }

    impl Light {
        fn new() -> Self {
            Light { on: false }
        }

        fn turn_on(&mut self) {
            if !self.on {
                self.on = true;
                println!("Turned on the light.");
            }
        }

        fn turn_off(&mut self) {
            if self.on {
                self.on = false;
                println!("Turned off the light.");
            }
        }
    }

    impl Drop for Light {
        fn drop(&mut self) {
            self.turn_off();
        }
    }

    struct Person {
        light: Light,
    }

    impl Person {
        fn read_book(&self) {
            if self.light.on {
                println!("What a fantastic book!");
            } else {
                println!("It is hard to read without light...");
            }
        }
    }

    fn main() {
        let mut light = Light::new();
        // light.turn_on();
        let mick = Person { light };
        mick.read_book();
    }

    ```

    🤔 Consider now having multiple people trying to read books in the same room. Each person needs light to read their book. You could change Person to use a reference to a Light instead

    ```rust
    struct Person<'a> {
        light: &'a Light,
    }

    impl<'a> Person<'a> {
        fn read_book(&self) {
            if self.light.on {
                println!("What a fantastic book!");
            } else {
                println!("It is hard to read without light...");
            }
        }
    }

    fn main() {
        let mut light = Light::new();
        light.turn_on();
        let mick = Person { light: &light };
        let anna = Person { light: &light };
        mick.read_book();
        anna.read_book();
    }
    ```

    While this works, it does mean that everywhere you use a Person<'a> , you need to make sure that there is a lifetime 'a available. Depending on your situation, it may be better to model this shared ownership through Rc<T>

    ```rust 
    use std::rc::Rc;

    struct Person {
        light: Rc<Light>,
    }

    impl Person {
        fn read_book(&self) {
            if self.light.on {
                println!("What a fantastic book!");
            } else {
                println!("It is hard to read without light...");
            }
        }
    }

    fn main() {
        let light = {
            let mut light = Light::new();
            light.turn_on();
            // Place the light in an `Rc<T>`
            Rc::new(light)
        };

        let mick = Person {
            // Note that `Light` does not implement `Clone`. We are cloning the
            // smart pointer here, not the value contained within. It is considered
            // good practice to call the clone implementation `Rc::clone` explicitly
            // because it encodes the intent to clone the smart pointer, not the
            // value itself.
            light: Rc::clone(&light),
        };
        let anna = Person { light };
        mick.read_book();
        anna.read_book();
    }
    ```

    When the last `Rc<T> ` goes out of scope, the `Drop` implementation of `Light` gets run and the light gets turned off. For further reading, check out the book and the standard library documentation.

## RefCell 

1. In the book reading example, switching the light on or off was impossible while people held a shared pointer to the light. At the same time, you cannot just hand out mutable references `&mut T` to values contained in an `Rc<T>` and let the compiler verify that only one mutable reference exists. 👉 Try to implement the same borrowing rules to be evaluated at run-time and encapsulate this in a generic type.

    ```rust
    use std::{cell::RefCell, rc::Rc};

    struct Person {
        // You have wrapped `Light` in a `RefCell` to provide internal mutability.
        light: Rc<RefCell<Light>>,
    }

    impl Person {
        fn read_book(&self) {
            // You have to call `RefCell::borrow` here to obtain an immutable reference `&Light`.
            if self.light.borrow().on {
                println!("What a fantastic book!");
            } else {
                println!("It is hard to read without light...");
            }
        }
    }

    fn main() {
        let light = {
            let mut light = Light::new();
            light.turn_on();
            // Place the light in an `Rc<T>`
            Rc::new(RefCell::new(light))
        };

        let mick = Person {
            // Note that `Light` does not implement `Clone`. You are cloning the
            // smart pointer here, not the value contained within. It is considered
            // good practice to call the clone implementation `Rc::clone` explicitly
            // because it encodes the intent to clone the smart pointer, not the
            // value itself.
            light: Rc::clone(&light),
        };
        let anna = Person {
            light: Rc::clone(&light),
        };

        // The light is on so mick can read.
        mick.read_book();

        // In order to turn off the light, you need a mutable reference `&mut Light`.
        // If any other references were handed out at this point, the program would panic.
        light.borrow_mut().turn_off();

        // The light is off so anna will have trouble reading.
        anna.read_book();
    }

    // Turned on the light.
    //What a fantastic book!
    //Turned off the light.
    //It is hard to read without light...
    ```

## Lesson Summary


In this lesson, you have learned about Rust smart pointers, including the most straightforward smart pointer - Box<T>. Consider how to share ownership of a value through the reference shared pointers.