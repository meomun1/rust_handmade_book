# Sequences, Maps and Sets

## Data Types

The most commonly used data types are sequences, maps, and sets.

Many different implementations of these concepts have slightly different capabilities and performance characteristics. Choosing the correct implementation depends entirely on the workload.

👉 In this lesson, you will explore each of them in more detail.

## Sequences

Rust offers three types suitable for representing a sequence of values of the same type. All implementations have their uses, but the most commonly used one by a large margin is `Vec <T>`.

You already know how to create a statically sized array, where static means "known at compile-time." A statically sized `array [T; N]` is a primitive type built into the compiler. The Rust standard library defines the type `Vec <T>` , which implements "a continuous growable array type with heap-allocated contents.". Unlike the statically sized array, the `Vec<T>` does not contain information about the number of elements in its type.

The following code creates an array [u8; 4] and a Vec<u8>:

    ```rust
    let statically_sized_array: [u8; 4] = [0, 1, 2, 3];
    let dynamically_sized_array: Vec<u8> = vec![0, 1, 2, 3];
    ```

As you can see, for the V The macro vec![0, 1, 2, 3] is a shorthand for the following:

    ```rust 
    let dynamically_sized_array = {
        let mut a = Vec::new();
        a.push(0);
        a.push(1);
        a.push(2);
        a.push(3);
        a
    };
    ```
Instead of push-ing additional items onto the end of the vector, remove items from the end of an array with 
pop

    ```rust
    let mut a = vec![1, 2];
    println!("{:?}", a.pop()); // Some(2)
    ```

👉 Because a vector's elements are laid out sequentially in memory, its elements can be cheaply accessed through the indexing operator:

    ```rust
    let a = vec![1, 2, 3];
    println!("{}", a[2]); // 3
    ```

☝️ The `Vec<T>`  type implements many more methods.

A `VecDeque<T>` is optimized for adding and removing elements from either side of the sequence. It is most commonly used to realize a first-in-first-out queue.

A `LinkedList<T>` implements a doubly linked list where the nodes contain two references and a value T in line. Linked lists are interesting from a theoretical perspective, but they are not often used in practice as they have a significant memory overhead and are not very cache efficient.


## Maps 

1. A map allows associating one value with another value. Let's say you want to associate a username with a user profile. This can be done by storing the user profiles in a vector and searching through it sequentially 👇 :

    ```rust

    struct UserProfile {
        name : String,
        age : i32,
    }

    let user = vec![
        UserProfile {
            name: "Mick".to_string(),
            age : 30,
        },
        UserProfile {
            name: "Jenny".to_string(),
            age : 28,
        }
    ]

    // Iterate through the array to find "Mick" and print his age.
    println!("{:?}", users.iter().find(|profile| profile.name == "Mick").unwrap().age); // 30

    //☝️ ff there is an extensive collection of profiles, and you need to do many lookups, then iterating over the entire collection may not be very efficient. An array gives no guarantee that the lookup key is unique.

2. HashMap<K, V> and BTreeMap<K, V>

    ```rust

    // ... same as above

    // Build a map from `(String, UserProfile)` tuples.
    let name_to_profile: std::collections::HashMap<String, UserProfile> = users
        .into_iter()
        .map(|profile| (profile.name.clone(), profile))
        .collect();

    // You can quickly lookup "Mick"'s age
    println!("{:?}", name_to_profile["Mick"].age); // 30

    ```

    In the above example, a BTreeMap can be used instead of a HashMap. A HashMap requires that key values can be hashed. A BTreeMap  instead requires that the key values can be ordered.
    For further details on how to use these collections, 👉 consult the HashMap and BTreeMap documentation.
    https://doc.rust-lang.org/stable/std/collections/struct.HashMap.html
    https://doc.rust-lang.org/stable/std/collections/struct.BTreeMap.html

## Sets

1. A set holds a collection of unique values. The standard library provides two implementations: HashSet<T> and BTreeSet<T>

    https://doc.rust-lang.org/std/collections/struct.HashSet.html
    https://doc.rust-lang.org/std/collections/struct.BTreeSet.html

    ```rust
    use std::collections::HashSet;

    let mut cool_numbers = HashSet::from([2, 10, 8]);

    // Inserting an element that is already present will have no effect.
    cool_numbers.insert(8);

    // Sets implement basic set operations like subtraction.
    println!("{:?}", &cool_numbers - &HashSet::from([2])); // {10, 8}

    
## Lesson Summary 

This lesson introduced you to the three most commonly used data types: sequences, maps, and sets. Look at The Vec<T>type and methods it can implement. Through examples, you have learned how to associate one value with another value or how to accelerate lookups via HashMap