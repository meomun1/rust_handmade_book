# Control Flow

## Control flow 

1. To add behavior to programs, you need the ability to run code conditionally and repeatedly.

2. Conditional execution

    ```rust
    // the if statement does not require parenthesis around the condition 
    let value = 10;
    if value == 0 {
        println!("Zero!");
    } else if value > -10 && value < 10 {
        println!("Single digit!");
    } else {
        println!("Multiple digits!");
    }
    ```

3. Looping 

    ```rust
    // Loop
    let mut i = 10;
    loop {
        if i == 0 {
            break;
        }
        println!("{i}...");
        i -= 1;
    }

    // While 
    let mut i = 10;
    while i != 0 {
        println!("{i}...");
        i -= 1;
    }

    // For 
    for i in (1..=10).rev(){
        println("{i}...");
    }


    // continue
    for i in (1..=10).rev(){
        if i % 2 == 0 {
            continue;
        }
        println("{i}..");
    }
    ```

## Lesson summary 
You’ve learned how to run code conditionally and repeatedly in this lesson. Now you understand how to write the above loop with different statements. Via examples met the implementation of the statement.