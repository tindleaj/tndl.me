+++
title = "Writing a simple command line program in Rust"
description = "A JavaScript developer's guide to the Rust programming language"
date = 2020-01-07
draft = true
+++

In this article we will build a simple command line program that returns the word count of a file. This will essentially be a simpler version of the Unix utility `wc`, written in Rust. The goal of this article is to give an introduction to some core Rust concepts for readers who might be more familiar with web languages such as JavaScript and Typescript. Therefore, the Rust code examples will be compared to similar code and concepts in JavaScrip or TypeScript. This guide also assumes no prior knowledge of Rust or related tools, but it does assume you have `node` and `npm` installed on your machine already.

<!-- more -->

- [Notes](#notes)
- [Setting up](#setting-up)
  - [Project structure](#project-structure)
  - [Running the project](#running-the-project)
  - [Tour of a &quot;Hello World&quot; program in Rust](#tour-of-a-quothello-worldquot-program-in-rust)
- [The miniwc program](#the-miniwc-program)
  - [Building a foundation](#building-a-foundation)
    - [Types](#types)
    - [Structures (struct)](#structures-struct)
    - [Implementations (impl)](#implementations-impl)
  - [Handling arguments](#handling-arguments)
    - [The Iterator trait](#the-iterator-trait)
    - [Storing things in Vectors](#storing-things-in-vectors)
    - [The panic! macro](#the-panic-macro)
  - [The filesystem](#the-filesystem)
    - [Handling possible failure with expect](#handling-possible-failure-with-expect)

## Notes

A couple of notes and assumptions:

- No previous knowledge of Rust is assumed. We'll go over all of the necessary concepts as they come up, and I'll link to relevant content where I think more detail or rigor is needed.
- Roughly intermediate-level experience with JavaScript is assumed. If you're just getting started with JavaScript or haven't built anything non-trivial with it, you might want to save this resource for later.

## Setting up

In order to get started, first we need to set up a new Rust project. If you haven't yet installed Rust on your computer, you can take a look at [the official 'getting started' guide](https://www.rust-lang.org/learn/get-started).

Once you have `cargo` available, go ahead and run `cargo new miniwc --bin` in a suitable directory.

### Project structure

You might now be asking, 'What is `cargo`? For those new to Rust's tooling, `cargo` is a direct parallel to `npm` in the Node ecosystem, in other words Rust's built-in package manager. You can view popular `crates` (packages) available at [crates.io](https://crates.io).

The `cargo new miniwc --bin` command tells `cargo` to create a new _binary_ (able to run on our machine) Rust project named `miniwc` in the directory `./miniwc` and set up the basic boilerplate project structure: `Cargo.toml`, `src/main.rs`, and a `.gitignore`.

- `Cargo.toml`: Analogous to Node's `package.json`. This is where you put project information and declare project dependencies
- `Cargo.lock`: This is a manifest managed by `cargo`, that tracks exact dependency versions. It is analogous to Node's `package-lock.json`.
- `src/main.rs`: Our project is a _binary_ project, meaning we can compile and run it on our machine. `cargo` creates a `main.rs` file as the default entry point for compiling our source code.
- `.gitignore`: A standard `git` artifact, tells `git` what files to ignore from source control.

### Running the project

That's it for the project structure, but what about actually running the code? In `node`, we have `npm` which allows us to define scripts such as `start` and `test`, and then run those commands via `npm run start` or `npm run test`. `cargo` gives us similar functionality. Running `cargo run` in our project directory will run our boilerplate project. Try it out, and you should see `Hello, world!` printed to your console.

> You may have noticed a new `target/` directory appear after you ran `cargo run`. This is a folder managed by `cargo` to store build artifacts and other dependencies of the compilation process. For a more detailed guide to `cargo` and an overview of concepts like the `target/` directory, check out [The Cargo Book](https://doc.rust-lang.org/cargo/index.html).

### Tour of a "Hello World" program in Rust

Let's take a moment to take a look at the auto-generated code within `main.rs` and draw some basic parallels from the JavaScript world to that of Rust:

Filename: src/main.rs

```rust
fn main() {
    println!("Hello, world!");
}
```

If we ported the above Rust program to JavaScript it would look like:

```javascript
function main() {
  console.log('Hello, world!')
}

// Since `main()` isn't a special function in JavaScript,
// we have to invoke it if we want our code to run:
main()
```

_If the distinction between compiled and interpreted languages is a bit hazy for you, take a look at [this article](https://guide.freecodecamp.org/computer-science/compiled-versus-interpreted-languages/) for a more in-depth treatment._

`fn` is the _function_ keyword in Rust, and `main` denotes to the name of the function. `main` is a special function name in Rust (as it is in other compiled languages like C) and it lets the Rust _compiler_ know that this is the entry point of an executable program. `()` is the list of _arguments_. In this case there are no arguments, so the parentheses are empty.

The _body_ of the `main` function is declared with `{ }`, and represents its _scope_. Inside the body of `main`, we have `println!("Hello, world!");`. This looks like a function, but in fact is a _macro_. In Rust _macros_ are denoted by the `!` at the end of a keyword.

There is no great parallel for _macros_ in JavaScript, but a simple definition is that _macros_ are code that generate other code when the program is compiled. Rust will replace `println!` with code for printing to _standard out_ that works for whatever computer architecture you're compiling the Rust code for. In my case this would be code for printing in macOS, but it might be different for you.

With the basic setup and syntax tour out of the way, we can move on to an overview of our `miniwc` program.

_`cargo` isn't strictly necessary to create Rust binaries, it just provides some convenient tools and a bit of boilerplate to get you started. All you need to compile Rust projects is the Rust Compiler (`rustc`). Running `rustc foobar.rs` on any valid and correct Rust program will output an executable binary. Don't believe me? Try it with the code above!_

## The `miniwc` program

At the end of this article, we will have an executable program that takes a filename as an argument and returns the word count of that document. We will explore the following concepts:

- Functions
- Conditionals
- Loops
- `struct`
- Traits and `impl`
- The `std` library
- `std::env`
- `std::fs`

Let's get into it.

### Building a foundation

Before we can begin tackling the program requirements we've outlined above, there are several Rust concepts that we need to anchor to their counterparts in JavaScript. [I'm a big advocate for understanding bedrock concepts](https://blog.usejournal.com/you-probably-shouldt-be-using-react-2f45ca487c8e?source=friends_link&sk=b88d7e3f0c715965bbc859b5ce8053e1), especially as you move past the beginner stage where you know how to get things done, but maybe not why you're doing them that way. I feel that Rust is a great tool to put the effort in and _really_ learn, so before we go ahead and actually write the code for our program, we're going to explore a prelude of necessary concepts, step by step. These include:

- The type system in Rust, and how it relates to types in JavaScript
- Rust `struct`s, their similarity to JavaScript `Objects`, and an overview on how to use them to provide _structure_ to our code
- Rust `impl`s, the JavaScript _Prototypal Inheritance_ model, and how we can create reusable functionality in our Rust code

There are some concepts here that may seem very foreign, but they all map to JavaScript concepts you probably already know and use regularly. If you have a good grasp on the above topics already, feel free to skip the next few sections. Otherwise, let's unpack them one at a time.

#### Types

Rust is a _statically typed language_, and therefore it expects explicit _type_ annotations in the places in your code where it isn't obvious what the type of a value is. If you have experience with TypeScript, this concept should be familiar.

Two common ways you'll interact with _types_ in Rust is through argument types and return types:

```rust
fn example_function(
  integer_arg: i64,
  string_arg: String,
  other_arg: OurCustomType ) -> String {
    // ---snip---
}
```

In the above example, we pass three arguments to our `example_function`, `integer_arg` with the type `i64` (a 64-bit signed integer), `string_arg` with the type `String`, and `other_arg` with the made-up example type `OurCustomType`. These type annotations are denoted by the colon (`:`) following the argument name. After the list of arguments, there's an arrow (`->`) followed by `String` which signifies that this function will return a `String` value.

JavaScript is a dynamically typed language, which means all of the _type_ behavior we have to specifically define in our Rust code is handled under the hood by the JavaScript runtime. JavaScript has primitive types like `Number` and `String`, but it doesn't require the programmer to be explicit about what _types_ correspond to each value. JavaScript also doesn't allow the programmer to come up with their own types, like the `Args` type we saw previously in the `args` function signature. [This is both powerful and limiting](https://hackernoon.com/statically-typed-vs-dynamically-typed-languages-e4778e1ca55), depending on the context and use-case.

#### Structures (`struct`)

With the basics of _types_ in Rust under our belts, let's take a moment to unwrap another fundamental Rust concept that we'll need going forward: `struct`. Rust, unlike modern JavaScript, has no concept of `class` and it doesn't have a catch-all, ubiquitous name/value collection like JavaScript's `Object` type. Instead, Rust allows you to associate fields and related functions using _structures_, via the keyword `struct`. This is somewhat similar to how `objects` are used in JavaScript. Compare the following two examples:

```javascript
let message = {
  title: "Message title"
  body: "This is a message."
}
```

```rust
struct Message {
  title: String,
  body: String
}

let message = Message {
  title: String::from("Message title"),
  body: String::from("This is a message.")
}
```

Since Rust doesn't give you an arbitrary bucket of key/value pairs to work with (like JavaScript does with `Objects`), we first need to define the _structure_ of our `Message` type, via the `struct` keyword. Note how in the JavaScript example, we just assign `String` values to the `message` and `body` keys. This is a very common pattern, and in some cases is extremely powerful and simple. In the Rust example, we have to be explicit about the types of values each _field_ (note that in Rust, [we call these key/value pairs _fields_](https://doc.rust-lang.org/book/ch05-01-defining-structs.html#defining-and-instantiating-structs), while in JavaScript they're called _properties_). Once we've told the Rust compiler what our `Message` _fields_ will contain, we then can create a new `Message` with our specific field values.

#### Implementations (`impl`)

JavaScript uses an inheritance model called _Prototypal Inheritance_ in order to allow for extending and reusing behavior in your code. Another familiar model that accomplishes something similar is the more traditional class-based model you may have come across in other languages like Java and TypeScript (JavaScript has `class` syntax, but it's just sugar over its prototypal inheritance model).

For the purposes of this project, you don't need to be super familiar with the ins and outs of _Prototypal Inheritance_ or _Object Oriented Programming_, but if you're interested in diving in, Mozilla offers an in-depth treatment [here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Inheritance_and_the_prototype_chain). What we're specifically interested in is how JavaScript allows you to implement and reuse behavior, versus how Rust does it. Consider the following JavaScript example:

```javascript
// Using JavaScript's `class` syntax because
// it's simpler for this example
class Message {
  send(content) {
    console.log(content)
  }
}

class PrivateMessage extends Message {
  send(content) {
    super.send('private: ' + content)
  }
}

var message = new Message()
message.send('hello') // hello

var privateMessage = new PrivateMessage()
privateMessage.send('hello') // private: hello
```

Here, we've modeled `PrivateMessage` _as a_ `Message`. It inherits the `send` function we defined on `Message`, but we can change it to be specific for our `PrivateMessage` class. Rust has a different way of doing things. Let's take a look at the same idea, expressed in Rust:

```rust
struct PrivateMessage {}
struct NormalMessage {}

pub trait Message {
    fn send(&self, content: &str) {
        println!("{}", content);
    }
}

impl Message for NormalMessage {} // Use the default `send`

impl Message for PrivateMessage {
    fn send(&self, content: &str) {
        println!("private: {}", content);
    }
}

pub fn main() {
  let message = NormalMessage {};
  message.send("hello"); // hello

  let private_message = PrivateMessage {};
  private_message.send("hello"); // private: hello
}
```

In this version of the program, we've defined `Message` as a _trait_, which can be _implemented_ by our other code. In other words, our `PrivateMessage` and `NormalMessage` structs`NormalMessage` uses the default `send` implementation that we define in the `Message` trait, while `PrivateMessage` implements its own version of `send`.

Hopefully this sheds a bit of light onto the basics of Rust inheritance (via `traits` and `impl`) versus JavaScript (via prototypes). If any of this still feels opaque, take some time to dive-in to the relevant sections in the Rust Book:

### Handling arguments

Now that we've explored the necessary foundational concepts to understand and implement our `miniwc` program, let's get back to our `miniwc` program. As mentioned before, our program should:

- Be executable
- Take a filename as an argument
- Return the word count of that document

Currently, our program does none of the things outlined above. When you execute `cargo run` from the command line, we still just see `Hello, world!` printed out. Let's take it step by step, and first handle taking a filename as an argument.

In `node`, one of the global variables made available to our programs during runtime is the `process.argv` variable. This variable contains all of the arguments passed to your `node` program. Rust does not have a runtime, so how can we get arguments passed to our program?

Although Rust doesn't have a language-specific runtime, the operating system your Rust program runs on _is_ technically a runtime. And luckily for us, the operating system provides a way to inject variables into programs. We won't need to get into the specifics of how that happens (and the potential pitfalls), because the _Rust standard library_ provides an easy way for us to access the arguments passed to our program, via the `std::env` module. Similar to how `process.argv` works in `node`, the `std::env` module will allow us to get a list of arguments we can then use how we'd like.

In order to use the `std::env` module, we'll have to `use` it at the top of our program like so: `use std::env`. The `use` keyword in Rust is sort of analogous to the `import` keyword in ES6, and it allows us to bring a module into scope. With the `env` module in scope, we can now use the public function [`args`](https://doc.rust-lang.org/std/env/fn.args.html), which exists in the `env` module.

This function will return a value with the _type_ of `Args`, which _implements_ the _trait_ `Iterator`. The function signature for `args` looks like so: `fn args() -> Args`.

These are all concepts we've explored in the last few sections, so now let's put them to work. Once you've added the `use` statement for `std::env`, your program should look like this:

Filename: src/main.rs

```rust
use std::env;

fn main() {
    println!("Hello, world!");
}
```

#### The Iterator trait

#### Storing things in Vectors

#### The `panic!` macro

### The filesystem

#### Handling possible failure with `expect`
