# The Filesystem

## Basic Concepts

The Rust standard library provides types and functions to interact with the file system under std::fs. This lesson introduces you to the terms of files and directories.

## File

1. 👉 The File type is an abstraction around files. The `File::create` function creates a new file if it doesn't exist, or truncates it if it does.

    ```rust
    use std::fs::File;
    use std::io::prelude::*;

    fn main() -> std::io::Result<()> {
        let mut file = File::create("foo.txt")?;
        file.write_all(b"Hello, world!")?;
        Ok(())
    }
    ```

2. You can read an existing file with `File::open`

    ```rust
    use std::fs::File;
    use std::io::prelude::*;

    fn main() -> std::io::Result<()> {
        let mut file = File::open("foo.txt")?;
        let mut contents = String::new();
        file.read_to_string(&mut contents)?;
        assert_eq!(contents, "Hello, world!");
        Ok(())
    }
    ```

3. The above can also be realized more easily through fs::write and fs::read Consult the File documentation for further details.

https://doc.rust-lang.org/std/fs/struct.File.html

## Directories 

1. 😀 You can list the entries in a directory using `std::fs::read_dir`

    ```rust
    use std::{fs, io};

    fn main() -> io::Result<()> {
        for entry in fs::read_dir(".")? {
            let entry = entry?;
            let file_type = entry.file_type()?;
            let prefix = match file_type {
                t if t.is_dir() => "D",
                t if t.is_file() => "F",
                t if t.is_symlink() => "L",
                _ => "?",
            };
            println!("{prefix} {}", entry.path().display());
        }

        Ok(())
    }
    ```

## Lesson Summary 


Completing this lesson gave you an understanding of the terms of files and directories. You've considered the syntax of the File type and learned how to list the entries in a directory using `std::fs::read_dir`