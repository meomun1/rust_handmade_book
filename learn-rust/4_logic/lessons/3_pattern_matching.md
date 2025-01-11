# Pattern matching

## Basic concept 

1. Pattern matching allows you to deconstruct values of complex types into their parts.

2. The pattern does not have to include all possibilities when conditionally matching. This is called a refutable pattern. 

3. The pattern can match any possible value of the type you are matching against. This is called a irrefutable pattern. 


## Let pattern 

1. Let must be a irrefutable pattern

    
    ```rust
    let <pattern> : <type> = <expression>;

    // Example 
    struct Plant {
    flowering: bool,
    mass: f64,
    }

    // construct an object 
    let plant = Plant {
        flowering : true,
        mass : 10.0,
    }

    // deconstruct a struct 
    let Plant { flowering, mass } = plant;
    ```

2. When we tried to do refutable pattern 

    ```rust 
    enum Meal {
        FishAndChips { chip_proportion : u64},
        Hamburger { vegetarian : bool},
    }

    let meal = Meal::Hambuger {
         vegetarian : true,
    };

    // This will create compliler error when try to deconstruct
    let Meal::Hamburger { vegetarian } = meal;
    // error[E0005]: refutable pattern in local binding
    // the pattern is refutable, meaning that there exist values that cannot be matched
    ```

3. If let =>  it allows for refutable patterns. If the pattern matches, a block of code is executed

    ```rust 
    if let Meal::Hambuger { vegetarian } = meal {
        println!("I had a hambuger");
    }

    // the compiler may warn unused variable : vegtarian 
    if let Meal::Hamburger { .. } = meal {
        println!("I had a hamburger!");
    }    
    // .. => to match "any remaining fields" 

    // when we set true for vegetarian 
    if let Meal::Hamburger { vegetarian: true } = meal {
        println!("I had a vegetarian hamburger!");
    }
    ```

## Match 

1. Match statement allows you to specify multiple patterns to be matched against. This is useful because you can write more specific patterns and handle any other values not matched by those patterns later

    ```rust
    for n in 0..=5 {
        match n {
            1 => println!("It was one!");
            2 => println!("It was two!");
            // or-pattern 
            3 | 4 => println!("It was a bit more than two");
            // match guard 
            high if high >= 5 => println("It was a higher numeber!");
            // other cases
            other => println("It was {other}");
        }
    }

    match meal {
        Meal::FishAndChips { chip_proportion } => {
            if chip_proportion > 0.5 {
                println!("I had some fish and plenty of chips!");
            } else if chip_proportion < 0.5 {
                println!("I had plenty of fish and some chips!");
            } else {
                println!("I had fish and chips!");
            }
        }
        Meal::Hamburger { vegetarian } => {
            if vegetarian {
                println!("I had a vegetarian hamburger!");
            } else {
                println!("I had a meaty hamburger!");
            }
        }
    }   

2. If the programmer so desires, the amount of nesting caused by the if statement by matching against values inside the enum variants and through match guards:

    ```rust
    match meal {
        Meal::FishAndChips { chip_proportion } if chip_proportion > 0.5 => {
            println!("I had some fish and plenty of chips!");
        }
        Meal::FishAndChips { chip_proportion } if chip_proportion < 0.5 => {
            println!("I had plenty of fish and some chips!");
        }
        Meal::FishAndChips { chip_proportion } => {
            println!("I had fish and chips!");
        }
        Meal::Hamburger { vegetarian: true } => {
            println!("I had a vegetarian hamburger!");
        }
        Meal::Hamburger { vegetarian: false } => {
            println!("I had a meaty hamburger!");
        }
    }

## While let 

1. The while let statement lets you loop until the pattern does not match

    ```rust 
    let mut meal = Meal::FishAndChips {
        chip_proportion: 0.6,
    };
    while let Meal::FishAndChips { chip_proportion } = meal {
        println!("Having fish and chips with chip proportion {:.2}...", chip_proportion);
        if chip_proportion > 0.3 {
            // Order a meal with less chips.
            meal = Meal::FishAndChips {
                chip_proportion: chip_proportion - 0.2,
            }
        } else {
            // Too fishy, let's get a hamburger next.
            meal = Meal::Hamburger { vegetarian: true }
        }
    }
    println!("I'm so done with fish and chips!");

    //Having fish and chips with chip proportion 0.60...
    //Having fish and chips with chip proportion 0.40...
    //Having fish and chips with chip proportion 0.20...
    //I'm so done with fish and chips!
    ```

## For 

1. Just like `let` accepts an irrefutable pattern 

    ```rust
    for <pattern> in <expression> { <body> } 

    // Example 
    let tuples: [(usize, &'static str); 3] = [
        (1, "red"),
        (2, "white"),
        (3, "blue"),
    ];

    for (numbering, color) in tuples {
        println!("Color #{numbering} is {color}!");
    }
    // Color #1 is red!
    // Color #2 is white!
    // Color #3 is blue!

    let colors = [ "red", "white", "bule"];

    for (index, color) in colors.into_iter().enumerate(){
        let numbering = index + 1; 
        println!("Color #{numbering} is {color}!");
    }