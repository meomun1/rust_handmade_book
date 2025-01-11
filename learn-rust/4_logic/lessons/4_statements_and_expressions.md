# Statements and Expressions

## Basic concepts 

1. Rust programs are composed of sequences of statements. Statements are terminated by a semicolon `;`

2. Statements exist so that you can cause side effects. Examples of side effects are printing to the terminal through `println!`

## If and match as Expressions

1. In previous lessons, you have directed to `if statements`. While not incorrect, it is slightly inaccurate. An `if statement` is an expression statement containing an `if expression`

2. ternary if 

    ```rust
    a ? b : c 
    // if a true => b, if a false => c 

3. If and match 

    ```rust 
    // Need to reduce 
    let quantifier;
    if brownies_eaten == 0 {
        quantifier = "no brownies";
    } else {
        quantifier = "at least one brownie";
    };

    // Reduce with if where `if` can be used as an expression
    let quantifier = if brownies_eaten == 0 {
        "no brownies"s
    } else {
        "at least one brownie"
    };

    // reduce with match where `match` can be used as an expression
    let quantifier = match brownies_eaten {
        0 => "no brownies",
        1 => "a brownie"
        _ => "multiple brownies"
    };

## Scope 

1. Have you wondered what would happen if you put multiple statements in an `if` body? 

    ```rust
    let x = if some_condition {
        println!("It was true!");
        1
    } else {
        2
    };
    // It was true!
    ```

2.  This is something you can do for pretty much any scope. A scope is a block of code enclosed between `{ and }`

## Loop as Expressionss

1. There is one loop construct, namely loop, that can be written as an expression. Can you guess what the following code outputs?

    ```rust
    let mut i = 0;
    let x = loop {
        if i > 7 {
            break i;
        }
        i += i*2 + 1;
    };
    println!("{x}");
    // 13
    ```