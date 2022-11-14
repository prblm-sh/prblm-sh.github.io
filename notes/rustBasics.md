Notes on basics of RustLang

# Start New Project
From a terminal, use either `cargo new $PROJECTNAME` to create a new directory for the project, or use `cargo init` if you've already created a directory for the project.

This creates a new `Cargo.toml` file, a `src` directory, and a `main.rs` file in said directory. It also inits the directory as a git repo (by default, optional) for version control of the project. 

# Hello World
This is the source code of the traditional Hello World program.
```rust
// This is a comment, and is ignored by the compiler


// This is the main function
fn main() {
    // Statements here are executed when the compiled binary is called

    // Print text to the console
    println!("Hello World!");
}
```

`println!` is a [_macro_](https://doc.rust-lang.org/rust-by-example/macros.html) that prints text to the console.

A binary can be generated using the Rust compiler: `rustc`.

```bash
$ rustc hello.rs
```

`rustc` will produce a `hello` binary that can be executed.
```bash
$ ./hello
Hello World!
```

(Note: The use of `./hello` above is a bash-builtin that is equivalent to `bash hello` if in the same directory.)
If you get a `permission denied` error when attempting to run the compiled binary, run `chmod +x hello.rs` (use you're filename in place of hello.rs) to add `execute` permission on the binary file. 
# Notes
## Variables
Variables are assigned via the `let` keyword, i.e.
```rust
let NumOfNotes = 47
```

By default, variables in rust are immutable, i.e. cannot be changed once set.

To make a variable mutable/able to be changed, use the `mut` keyword, i.e.
```rust
let mut NumOfNotes = 0
```
Now the value assigned to `NumOfNotes` can be changed later in the program. 