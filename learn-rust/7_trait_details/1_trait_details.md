# Trait Details

## Associated Functions

1. For example, let's implement base functions for simply custom type:

    ```rust 

    struct MyType(i32);

    impl MyType {

        fn new () -> MyType{
            MyType(0)
        }

        fn get(&self) -> i32 {
            self.0
        }

        fn set(&mut self, val : i32) {
            self.0 = val
        }

    }

## Associated Types 

1. Associated Types is a helpful feature of Rust for improving the readability of code.

    ```rust

    struct Index(i32);

    trait Joining {
        // Associated types
        type A;
        type B;
        fn join_to_str(&self, _:&Self::A, _:&Self::B) -> String;
    }

    impl Joining for Index {
        // Define type of associated types
        type A = String; 
        type B = String;

        fn join_to_str(&self, name : &Self::A, last_name : &Self::B) -> String {
            format!("{}. {} {}", self.0, name, last_name)
        }
    }

    fn get_join_str<J: Joining>(joining : &J, name : &J::A, last_name : &J::B) -> String {
        format!("Person: {}", joining.join_to_str(name, last_name))
    }

    fn main() {
        let index = Index(10);

        println!("{}", get_join_str(&index, &"John".to_string(), &"Connor".to_string() ) );
    }

    ```

2. Look at the get_joined_str. It uses an associated type. ☝️ For example, how would it look without an associated type:

    ```rust
    fn get_joined_str<A, B, J:Joining<A, B>>(joining: &J, name: &A,
    last_name: &B) -> String {
        format!("Person: {}", joining.join_to_str(name, last_name))
    }
    ```

3. Associated Constants

    ```rust
    trait ConstantValue {
        const VALUE: i32;
    }
    struct MyStruct;

    impl ConstantValue for MyStruct {
        const VALUE: i32 = 10;
    }
    fn main() {
            println!("{}", MyStruct::VALUE);
    }
    ```

## Generic Parameters

1. A few examples of how to set generic parameters 👉 :

    ```rust 

    fn foo<T>(v: &T) {}

    // it can't store references so we need to use a lifetime annotation.
    struct Example<'a T>{
        value : &'a T,
    }

    // like to previous but the T must to implement the DerivedTrait
    struct Example<'a, T: DerivedTrait> {
    value: &'a T,
    }
    
    // T must to implement the DerivedTrait and the DerivedTrait2
    struct Example<'a, T: DerivedTrait + DerivedTrait2> {
    value: &'a T,
    }

    // T must implement the DerivedTrait and the DerivedTrait2.
    struct Example<'a, T>
    where
        T: DerivedTrait + DerivedTrait2,
    {
    value: &'a T,
    }

## Derive macros 

1. You have this structure and would like to print that:

    ```rust 
    fn main() {
        struct Struct  {
            name: String,
            age:i32,
        }

    let v = Struct {
        name: "John".to_string(),
        age: 20,
    };
    println!("{:?}", v);
    }

    // “`Struct` cannot be formatted using `{:?}`”.

    // Because the default formatter doesn’t know how to convert this type to string. Of course, you can implement the Debug for the Struct (impl Debug for Struct{}), but Rust has a simple solution for similar cases - derive macros.

    #[derive(Debug)]
    struct Struct  {
        name: String,
        age: i32,
    }

    // With the derive macro, you can implement many traits. For example: Default, Clone, Copy, Serialize/Deserialize

## Generic Blanket Implementations

Blanket implementations leverage Rust’s ability to use generic parameters. You can use them to define shared behavior using traits. It is a great way to remove redundancy in code by reducing the need to repeat the code for different types with similar functionality.

    ```rust
    use core::fmt::Display;
    // Implementation ToString for generic trait T.
    // In this case, T must have an implementation of the Display trait.
    impl<T: Display> ToString for T {}
    ```

## SuperTraits

Supertraits are something like inheritance in OOP languages. 👇 Example:

    ```rust
    trait Engine {
        fn start(&mut self);
        fn stop(&mut self);
        fn state(&self) -> bool;
    }

    trait Transmission {
        fn set_gear(&mut self, _: i32);
        fn gear(&self) -> i32;
    }

    trait Vehicle: Engine+Transmission {
        fn wheel_count(&self) -> u32;
    }

    trait Car: Vehicle {
        fn fuel_type(&self) -> FuelType;
    }

    as you can see, the Vehicle has all functions of the Engine and the Transmission; the Car has functions of all traits.
    ```

## Creating Trait Objects 

The Rust doesn't have polymorphism, but there is a similar feature - dispatch. Dispatch may be static or dynamic.

    ```rust 

    trait Vehicle {
        fn wheel_count(&self) -> u32;
    }

    #[derive (Default)] 
    struct Car; 
    impl Vehicle for Car {
        fn wheel_count(&self) -> u32 {
            4 
        }
    }

    #[derive (Default)]
    struc Motorcycle;
    impl Vehicle for Motorcycle {
        fn wheel_count(&self) -> u32 {
            2 
        }
    }

    fn wheel_count_static<T: Vehicle>(obj : &T) -> u32 {
        obj.wheel_count()
    }

    fn wheel_count_dynamic(obj: &dyn Vehicle) -> u32 {
        obj.wheel_count()
    }

    here you can see an implementation of the Vehicle trait for two different types and functions with similar behavior, but the "wheel_count_static" is static, and the "wheel_count_dynamic" is dynamic.

    // 👇 Example of using:

    fn main() {
        let car = Car::Default();
        println!("{}", wheel_count_static(&car));
        println!("{}", wheel_count_dynamic(&car));

        let motorcycle =  Motorcycle::default();
        println!("{}", wheel_count_static(&motorcycle));
        println!("{}", wheel_count_dynamic(&motorcycle));
    }
    ```

## Trait Objects versus Enumerations

The second method of polymorphism in Rust is Enum. 👇 Example:

    ```rust
    enum Vehicle {
        Car { wheel_count: u32 },
        Motorcycle { wheel_count: u32 },
    }

    impl Vehicle {
        fn new_car() -> Self {
            Self::Car { wheel_count: 4 }
        } 
        fn new_motorcycle() -> Self {
            Self::Motorcycle { wheel_count: 2 }
        }
        fn wheel_count(&self) -> u32 {
            match self {
                Vehicle::Car { wheel_count, .. } => *wheel_count,
                Vehicle::Motorcycle { wheel_count, .. } => *wheel_count,
            }
        }
    }

    fn main() {
        let car =  Vehicle::new_car();
        println!("{}", car.wheel_count());

        let motorcycle =  Vehicle::new_motorcycle();
        println!("{}", motorcycle.wheel_count());
    }
    ```

## Lesson Summary 

In this lesson, you've discovered how to set generic parameters and implement many traits, considered how to create trait objects, and how to use Blanket implementations.

What are associated items in Rust?  
=> associated items are functions, constants, or types declared in traits or defined in implementations