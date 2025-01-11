# Threading

## Threads 

1. 👉 Writing multithreaded programs can be a bit daunting. Rust provides threading capabilities and asynchronous communication primitives in the standard library. Rust also helps minimize mistakes through the `Send` and `Sync` traits, which indicate whether instances of a type can be sent to another thread and shared between multiple threads, respectively.

    ```rust
    fn main() {
        let handle = std::thread::spawn(|| {
            for i in 0..100 {
                println!("spawned: {i}");
                std::thread::yield_now();
            }
        });
        for i in 0..100 {
            println!("main: {i}");
            std::thread::yield_now();
        }
        handle.join().unwrap();
    }

    // Output
    main: 0
    main: 1
    ...
    main: 28
    main: 29
    spawned: 0
    main: 30
    spawned: 1
    main: 31
    spawned: 2
    ...
    ```

## Synchronization 

1. One method to communicate between threads is through so-called channels.

    ```rust 

    // Create a simple streaming channel.
    let (tx1, rx) = std::sync::mpsc::channel();

    // Copy the producer.
    let tx2 = tx1.clone();

    std::thread::spawn(move || {
        tx1.send(1).unwrap();
    });

    std::thread::spawn(move || {
        tx2.send(2).unwrap();
    });

    // Wait until you receive two messages on the main thread.
    println!("{}", rx.recv().unwrap());
    println!("{}", rx.recv().unwrap());
    ```

2. 📌 You may find many other synchronization primitives available in the std::sync documentation.
    https://doc.rust-lang.org/stable/std/sync/

## Lesson Summary 

In this lesson, you have got acquainted with Rust Threading. You've also learned how to minimize mistakes through the `Send` and `Sync` trait.