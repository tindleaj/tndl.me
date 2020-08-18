+++
title = "Introduction to Rust for Node Developers"
description = "A JavaScript developer's guide to writing a command line program using the Rust programming language"
date = 2020-01-12
draft = false
+++

_Are you a JavaScript developer trying to learn Rust? Send me an email at [tindleaj@gmail.com](mailto:tindleaj@gmail.com). I'm working on stuff you'll be interested in._

In this article we will build a simple command line program that returns the word count of a file. This will essentially be a simpler version of the Unix utility `wc`, written in Rust. The goal of this article is to give an introduction to some core Rust concepts for readers who might be more familiar with web-focused languages such as JavaScript and Typescript. Therefore, the Rust code examples will be compared to similar code and concepts in JavaScrip or TypeScript. This guide also assumes no prior knowledge of Rust or related tools, but it does assume you have `node` installed on your machine already.

<!-- more -->

- [Notes](#notes)
- [Setting up](#setting-up)
  - [Project structure](#project-structure)
  - [Running the project](#running-the-project)
  - [Tour of a "Hello World" program in Rust](#tour-of-a-hello-world-program-in-rust)
- [The `miniwc` program](#the-miniwc-program)
  - [Building a foundation](#building-a-foundation)
    - [Types](#types)
    - [Structures (`struct`)](#structures-struct)
    - [Implementations (`impl`)](#implementations-impl)
    - [Enumerations (`enum`)](#enumerations-enum)
  - [Handling arguments](#handling-arguments)
    - [Using Iterators](#using-iterators)
    - [Handling all `Option`s](#handling-all-options)
  - [Reading file contents](#reading-file-contents)
    - [`Result` and `expect()`](#result-and-expect)
  - [Counting words](#counting-words)
- [Conclusion](#conclusion)
  - [Additional resources](#additional-resources)
    - [Books](#books)
    - [Projects](#projects)
    - [Other](#other)

## Notes

A couple of notes and assumptions:

- No previous knowledge of Rust is assumed. We'll go over all of the necessary concepts as they come up, and I'll link to relevant content where I think more detail or rigor is needed. I think that knowing how the fundamentals of things work is important, and I think you should as well.
- Roughly intermediate-level experience with JavaScript is assumed. If you're just getting started with JavaScript or haven't built anything non-trivial with it, you might want to save this resource for later.

## Setting up

In order to get started, first we need to set up a new Rust project. If you haven't yet installed Rust on your computer, you can take a look at [the official 'getting started' guide](https://www.rust-lang.org/learn/get-started), or the [first chapter of The Rust Book](https://doc.rust-lang.org/book/ch01-01-installation.html).

Once you have `cargo` available, go ahead and run `cargo new miniwc --bin` in a suitable directory.

### Project structure

The logical next question is "What is `cargo`?". `cargo` is a direct parallel to `npm` in the Node ecosystem, in other words Rust's built-in package manager. You can view popular `crates` (packages) available at [crates.io](https://crates.io).

The `cargo new miniwc --bin` command tells `cargo` to create a new _binary_ (able to run on our machine) Rust project named `miniwc` in the directory `./miniwc` and set up the basic boilerplate project structure: `Cargo.toml`, `src/main.rs`, and a `.gitignore`.

- `Cargo.toml`: Analogous to Node's `package.json`. This is where you put project information and declare project dependencies
- `Cargo.lock`: This is a manifest managed by `cargo`, that tracks exact dependency versions. It is analogous to Node's `package-lock.json`.
- `src/main.rs`: Our project is a _binary_ project, meaning we can compile and run it on our machine. `cargo` creates a `main.rs` file as the default entry point for compiling our source code.
- `.gitignore`: A standard `git` artifact, tells `git` what files to ignore from source control.

### Running the project

That's it for the project structure, but what about actually running the code? In `node`, we have `npm` which allows us to define scripts such as `start` and `test`, and then run those commands via `npm run start` or `npm run test`. `cargo` gives us similar functionality. Running `cargo run` in our project directory will run our boilerplate project. Try it out, and you should see `Hello, world!` printed to your console.

_You may have noticed a new `target/` directory appear after you ran `cargo run`. This is a folder managed by `cargo` to store build artifacts and other dependencies of the compilation process. For a more detailed guide to `cargo` and an overview of concepts like the `target/` directory, check out [The Cargo Book](https://doc.rust-lang.org/cargo/index.html)._

### Tour of a "Hello World" program in Rust

Let's take a moment to take a look at the auto-generated code within `main.rs` and draw some basic parallels from the JavaScript world to that of Rust:

File: src/main.rs

```rust
fn main() {
    println!("Hello, world!");
}
```

If we ported the above Rust program to JavaScript it would look like:

```javascript
function main() {
  console.log("Hello, world!");
}

// Since `main()` isn't a special function in JavaScript,
// we have to invoke it if we want our code to run:
main();
```

_If the distinction between compiled and interpreted languages is a bit hazy for you, take a look at [this article](https://guide.freecodecamp.org/computer-science/compiled-versus-interpreted-languages/) for a more in-depth treatment._

`fn` is the _function_ keyword in Rust, and `main` denotes to the name of the function. `main` is a special function name in Rust (as it is in other compiled languages like C) and it lets the Rust _compiler_ know that this is the entry point of an executable program. `()` is the list of _arguments_. In this case there are no arguments, so the parentheses are empty.

The _body_ of the `main` function is declared with `{ }`, and represents its _scope_. Inside the body of `main`, we have `println!("Hello, world!");`. This looks like a function, but in fact is a _macro_. In Rust _macros_ are denoted by the `!` at the end of a keyword.

There is no great parallel for _macros_ in JavaScript, but a simple definition is that _macros_ are code that generate other code when the program is compiled. Rust will replace `println!` with code for printing to _standard out_ that works for whatever computer architecture you're compiling the Rust code for. In my case this would be code for printing in macOS, but it might be different for you.

With the basic setup and syntax tour out of the way, we can move on to an overview of our `miniwc` program.

_`cargo` isn't strictly necessary to create Rust binaries, it just provides some convenient tools and a bit of boilerplate to get you started. All you need to compile Rust projects is the Rust Compiler (`rustc`). Running `rustc foobar.rs` on any valid and correct Rust program will output an executable binary. Don't believe me? Try it with the code above!_

## The `miniwc` program

At the end of this article, we will have an executable program that takes a filename as an argument and returns the word count of that document.

Let's get into it.

### Building a foundation

Before we can begin tackling the program requirements we've outlined above, there are several Rust concepts that we need to anchor to their counterparts in JavaScript. [I'm a big advocate for understanding bedrock concepts](https://blog.usejournal.com/you-probably-shouldt-be-using-react-2f45ca487c8e?source=friends_link&sk=b88d7e3f0c715965bbc859b5ce8053e1), especially as you move past the beginner stage where you know how to get things done, but maybe not why you're doing them that way. I feel that Rust is a great tool to put the effort in and _really_ learn, so before we go ahead and actually write the code for our program, we're going to explore a prelude of necessary concepts, step by step. These include:

- The type system in Rust, and how it relates to types in JavaScript
- Rust `struct`s, their similarity to JavaScript `Objects`, and an overview on how to use them to provide _structure_ to our code
- Rust `impl`s, the JavaScript _Prototypal Inheritance_ model, and how we can create reusable functionality in our Rust code
- A quick note on _enumerations_ (`enum`s)

There are some concepts here that may seem very foreign, but they all map to JavaScript concepts you probably already know and use regularly. If you have a good grasp on the above topics already, feel free to [skip the next few sections](#handling-arguments). Otherwise, let's unpack them one at a time.

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
    console.log(content);
  }
}

class PrivateMessage extends Message {
  send(content) {
    super.send("private: " + content);
  }
}

var message = new Message();
message.send("hello"); // hello

var privateMessage = new PrivateMessage();
privateMessage.send("hello"); // private: hello
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

#### Enumerations (`enum`)

If you're familiar with TypeScript, then Rust's `enum` _type_ is a close parallel. If not, _enumerations_ are relatively straightforward: they define a _type_ that can be one of several _variants_. For example, we can create an _enum_ that represents the different types of common U.S. coinage like so:

```rust
enum Coin {
  Penny,
  Nickel,
  Dime,
  Quarter
}
```

And we can reference any single variant via:

```rust
let penny: Coin  = Coin::Penny;
let dime: Coin = Coin::Dime;
```

As you can see, both `penny` and `dime` are `Coin`s (they have the `Coin` type), but we can get more specific and state the _variant_ of `Coin` that each variable holds. In JavaScript

### Handling arguments

Now that we've explored the necessary foundational concepts to understand and implement our `miniwc` program, let's get back to our `miniwc` program. As mentioned before, our program should:

- Be executable
- Take a filename as an argument
- Return the word count of that document

Currently, our program does none of the things outlined above. When you execute `cargo run` from the command line, we still just see `Hello, world!` printed out. Let's take it step by step, and first handle taking a filename as an argument.

In `node`, one of the global variables made available to our programs during runtime is the `process.argv` variable. This variable contains all of the arguments passed to your `node` program. To take command line arguments and print them out using `node`, we could do the following:

File: main.js

```javascript
for (let arg of process.argv) {
  console.log(arg);
}
```

If you save and run that program in the root of the project using `node main.js hello`, you should get three outputs. The first output is the program running our JavaScript code (in this case `node`). The second is the filename of the program being run, and the third is the argument we passed in.

Rust does not have a runtime environment like `node`, so how can we get arguments passed to our program?

Although Rust doesn't have a language-specific runtime environment, the operating system your Rust program runs on _is_ technically a runtime. And luckily for us, the operating system provides a way to inject variables into programs. We won't need to get into the specifics of how that happens (and the potential pitfalls), because the _Rust standard library_ provides an easy way for us to access the arguments passed to our program, via the `std::env` module. Similar to how `process.argv` works in `node`, the `std::env` module will allow us to get a list of arguments we can then use how we'd like.

In order to make the `std::env` module more ergonomic to use, we can `use` it at the top of our program like so: `use std::env`. The `use` keyword allows us to bring a module into scope. The `std` library is already available to our program, so we could just type `std::env::foo_function` every time we wanted to use something from the `env` module, but with `use` we can bring the `env` module directly into scope. A loose parallel between `use` to an equivalent in JavaScript would be taking a globally available function like `global.console.log` and setting it to its own variable for easier use, for example `let log = global.console.log`. With the `env` module in scope, we can now use the public function [`args`](https://doc.rust-lang.org/std/env/fn.args.html), which exists in the `env` module.

This function will return a value with the _type_ of `Args`. `Args` _implements_ the _trait_ `Iterator`, which allows us to _iterate_ over the returned arguments. The function signature for `args` looks like so: `fn args() -> Args`.

Except for `Iterator` and the idea of _iterating_, these are all concepts we've explored in the last few sections, so now let's put them to work. Once you've added the `use` statement for `std::env`, your program should look like this:

File: src/main.rs

```rust
use std::env;

fn main() {
    println!("Hello, world!");
}
```

Let's enhance our program and print out all of the arguments that we pass in from the command line:

File: src/main.rs

```rust
use std::env;

fn main() {
  for arg in env::args() {
    println!("{}", arg);
  }
}
```

_If the `println!` macro call seems a bit strange, you can [dive deeper here](https://doc.rust-lang.org/book/ch19-06-macros.html), but you can also simply think of `println!` as similar to JavaScript template literals: anything between `{}` will be replaced with the variable you pass as subsequent arguments. Play around with it a bit to get a more intuitive feel for how it works._

Now let's run the program and pass it some arguments via `cargo run -- hello world` (we separate the commands passed to `cargo` and the commands passed to our program with `--`). You should get the following output:

```txt
target/debug/miniwc
hello
world
```

The first line of our output is actually the name of the program running, by convention. It's `target/debug/miniwc` because that's the binary created for us by `cargo`. If you compiled this project for release, or used `rustc` to compile, then the first item in the `args()` value would just be `miniwc`. On the next two lines we see the two arguments we passed in.

Our program now nominally supports passing in arguments via the command line. Now we're ready to do something with them.

#### Using Iterators

Let's start by binding the value of the first argument passed in by the user (ignoring the program path argument, which comes first) using the `nth` method on the `Args` _type_. `Args` is the type of the value returned from `std::env::args()`, and it _implements_ the `Iterator` type, thereby inheriting all of the methods on `Iterator`. As per [the `Args` documentation](https://doc.rust-lang.org/std/env/struct.Args.html), `Args` specifically gives us an `Iterator` whose values are `String`s.

One of the [methods we get by inheriting from `Iterator` is `nth`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.nth), which returns the value of the `Iterator` item at the index given to `nth`. For example, `env::args().nth(1)` should give us the value at index `1` of the `args_list`. You can think of `Iterator` as sort of giving the properties of a JavaScript `Array` to any type that _implements_ `Iterator`. Like `Array`s, `Iterators` come with all sorts of useful [methods](https://doc.rust-lang.org/std/iter/trait.Iterator.html#provided-methods).

With `nth`, we should now be able to grab the first argument passed to our program. Let's set that value to a variable, and try to print it out with the following code:

File: src/main.rs

```rust
use std::env;

pub fn main() {
    let filename = env::args().nth(1);
    println!("{}", filename)
}
```

After a `cargo run -- hello`, we see:

```txt
error[E0277]: `std::option::Option<std::string::String>` doesn't implement `std::fmt::Display`
 --> src/main.rs:5:20
  |
5 |     println!("{}", filename)
  |                    ^^^^^^^^ `std::option::Option<std::string::String>` cannot be formatted with the default formatter
  |
  = help: the trait `std::fmt::Display` is not implemented for `std::option::Option<std::string::String>`
  = note: in format strings you may be able to use `{:?}` (or {:#?} for pretty-print) instead
  = note: required by `std::fmt::Display::fmt`

error: aborting due to previous error
```

An error! What happened?

#### Handling all `Option`s

The issue with our code is that `nth` doesn't return a `String` directly, but instead returns a type called `Option`. `Option` is part of an interesting feature of Rust: it has no `null` primitive type. Unlike most languages which have a `null` type (and very much unlike JavaScript which has `null` and `undefined`), Rust forces you to account for all possible values when working with operations that are influenced by things outside of the program's control, like accepting command line arguments or doing file I/O. To do this, Rust makes use of the `Option` _enum_, which can either be `Some(value)` or `None`. If the value is `None`, Rust makes you explicitly handle it, otherwise it will be a compile time error like we saw above. While this may seem overly rigid, this is one of the features of Rust that leads to less error-prone programs.

Let's look at a JavaScript example that illustrates this point:

```javascript
// Get the first argument passed in by the user
let arg = process.argv[2];

// Do really important stuff
console.log(arg.split(""));
```

There's a subtle error that will only happen sometimes in this code. Can you spot it? If we pass an argument to our program -- `node main.js hello` -- then it behaves as expected. However, if we don't pass an argument, we'll get an error that's probably very familiar if you use JavaScript a lot:

```txt
console.log(arg.split(''))
                  ^

TypeError: Cannot read property 'split' of undefined
```

In this case, it's easy to see what went wrong: if we don't pass an argument to our program, we end up setting our `arg` variable to the value at an array index that doesn't exist. JavaScript defaults that value to `undefined`, which then causes an error later on in our `handleArg` function when we try to `split()` the undefined value.

While this example is trivial to fix, it's very easy to introduce this kind of bug into a larger JavaScript program, where it's potentially much harder to find the original cause of the `undefined` value. A typical fix would have us check that the value exists before trying to use it, but that requires more code and more diligent programmers.

In cases where we're dealing with input to our program that can be undefined, Rust forces us to handle the potential undefined value with the `Option` type before the program will even compile. We can see the `Option` type in action if we tweak our `println!` call a bit:

File: src/main.rs

```rust
use std::env;

pub fn main() {
    let filename = env::args().nth(1);
    println!("{:?}", filename)
}
```

_This solution was hinted at [in our error message from before](#using-iterators). By adding the `:?` to the curly brackets, we're essentially telling the `println!` macro that we want to be more lenient about the types of values we can print to the console (specifically, we've added the [debug format trait](https://doc.rust-lang.org/rust-by-example/hello/print/print_debug.html))._

_If this doesn't make much sense, don't worry about it for now. In general, the Rust compiler is very helpful, and you can usually rely on its suggestions to fix your code if you've gotten stuck. In this case, let's follow its advice and see what we get._

After a `cargo run -- hello`, you should see:

```txt
Some("hello")
```

There it is! Since we passed in an argument to our program, `env::args.nth(1)` contains `Some` value. Now, try running the program without an argument. This time you should've gotten the `None` variant, just as we expected.

Now that we understand a bit about what's going on with Rust's `Option` type, how do we actually get to the value inside `Some`? Conveniently, Rust offers us a shortcut for grabbing values we are pretty sure are going to exist in our program:

File: src/main.rs

```rust
use std::env;

pub fn main() {
    let filename = env::args().nth(1).unwrap();
    println!("{}", filename) // we no longer need the ':?'
}
```

`unwrap()` is a method available on `Option`, and it's pretty straightforward. If there is `Some(value)`, then return the value. If not, then _panic_ (error out). `unwrap()` also serves as a sort of "TODO" flag, because it signals that you should replace it before releasing your program into the world.

When we run our program with at least one argument now, we should get that argument printed to the console. If we run it without any arguments, we should get a _panic_ along the lines of:

```txt
thread 'main' panicked at 'called `Option::unwrap()` on a `None` value'
```

With that brief foray into Rust `Option`s out of the way, let's next move on to actually reading text files from the system.

### Reading file contents

The Rust standard library [contains a module for filesystem operations](https://doc.rust-lang.org/std/fs/index.html). This module is very similar in functionality to the `fs` module in the Node standard library. In Node, we could use the contents of a file like so:

```javascript
const fs = require("fs");

fs.readFile("words.txt", "utf8", function (err, data) {
  console.log(data);
});
```

[The `readFile()` function](https://nodejs.org/api/fs.html#fs_fs_readfile_path_options_callback) takes a file, an optional encoding and a callback to handle either an error or the returned contents. The Rust `std::fs::read_to_string` function does something very similar, taking a file path and returning a `Result<String>`.

#### `Result` and `expect()`

`Result` is similar to `Option` in that it can either produce a value or something else (`None` being the 'something else' for `Option`). In the case of `Result`, the results are either:

- `Ok(T)`, where `T` is an arbitrary type, or,
- `Error` if the operation fails.

In the case of `fs::read_to_string`, the `Ok` result is `Ok(String)`, since on a successful "read this file to a string" operation, the value we want back is a `String`.

Let's add a simple text file to our project and test it out. Add the following text to a file called `words.txt` in the root of the project:

File: words.txt

```txt
This is a file containing words
There are several words on this line
This one is short
The end
```

Now let's use `read_to_string` to read `words.txt` to a variable:

File: src/main.rs

```rust
use std::env;
use std::fs;

pub fn main() {
  let filename = env::args().nth(1).unwrap();

  let file_contents = fs::read_to_string(filename).expect("Error reading file to string");

  println!("{}", file_contents)
}
```

Here we use `expect()`, which is very similar to `unwrap` except it allows us to pass a custom panic message. If we run our program and pass it the argument the path of our text file (`cargo run -- words.txt`), we should see our text printed to the console.

Now that we've successfully read our text file and put its contents in a variable, we can complete the final step of counting the words in that file.

### Counting words

Simple text manipulation like counting the number of individual words (separated by whitespace) is a great way to explore the power behind one of Rust's core philosophies, that of _zero cost abstractions_. The gist of this idea is two-fold: first, you shouldn't pay (in performance or size) for any part of the programming language that you don't use, and second, if you do choose to use a language feature then it will be just as fast (or faster) than if you wrote the feature yourself. By following this simple philosophy, Rust places itself as a prime choice for writing programs that need to be mindful of space and speed considerations.

To illustrate this point, let's take another example from JavaScript. A JavaScript implementation (`node`, the browser, etc), has to include a _garbage collector_ in order to manage memory the program uses. Even if all you do is `console.log('Hello World')`, the entirety of the JavaScript runtime, including the _garbage collector_ have to be there. In Rust, when you `println!`, the only code that gets compiled and run is the code specifically needed to print things.

It is worth noting that sometimes we don't really care that much about speed or size of our programs, and in those cases Rust doesn't have much of an advantage over JavaScript or any other language. But, when we do care about those things Rust really comes into it's own. In many cases with Rust you get the flexibility and expressive power of a super high level programming language while also getting near-unmatched performance. Let's look at an example:

```rust
use std::env;
use std::fs;

pub fn main() {
  let filename = env::args().nth(1).unwrap();

  let file_contents = fs::read_to_string(filename).expect("Error retrieving file");

  let number_of_words = file_contents.split_whitespace().count();

  println!("{}", number_of_words)
}
```

Here we've added a single line to our program, changed another, and essentially achieved our desired functionality. Let's take it step-by-step.

Once we have the file contents from our `words.txt` file bound to a variable, we take that`file_contents` `String` and split it up on [any Unicode whitespace via `split_whitespace`](https://doc.rust-lang.org/std/primitive.str.html#method.split_whitespace). This returns an _Iterator_ value. This would be roughly the equivalent of using the `split()` method on a `String` in JavaScript, for example:

```javascript
let exampleString = "This is an example";
console.log(exampleString.split(" ")); // Array(4) [ "This", "is", "an", "example" ]
```

Once we've done that, we can consume the `Iterator` with `count()` to get the number of items in it. A similar approach in JavaScript would be to use the `length` property of the returned `Array` from before.

Finally, we print the resulting count to the console. And that's it! Run `cargo run -- words.txt` to see the number of words in our text file.

## Conclusion

This program is very simple, but it illustrates a plethora of core Rust concepts. It also leaves out some other very important tools and ideas. For example:

- We could handle the `Error` and `None` cases in our argument-handling and I/O functionality [using `match`](https://doc.rust-lang.org/book/ch09-02-recoverable-errors-with-result.html)
- We could have counted the individual words using `Vectors` and `loops`
- We could have opted for a more object-oriented approach and contained our functionality to `struct`s and `impls`
- And lots more

If you've made it this far, thanks so much for reading! Writing this article has been a learning process for me, and I still very much consider myself a Rust beginner. If you spot any mistakes, or see any grievous infractions of best-practices, please reach out at `tindleaj[at]gmail[dot]com` or [@tindleaj](https://twitter.com/tindleaj) If you're interested in learning more Rust, there are a ton of other great, free, and current resources to do so.

### Additional resources

#### Books

- [The Rust Programming Language](https://doc.rust-lang.org/stable/book/) - official, incredibly well written, definitely should be your first stop
- [Fullstack Rust](https://www.newline.co/fullstack-rust) - great project-focused book that teaches practical Rust programming. Emphasis on web applications.
- [Rust in Action](https://www.manning.com/books/rust-in-action) another great project-focused book. Emphasis on systems programming.
- [Rust by Example](https://doc.rust-lang.org/rust-by-example/) - says it right on the tin
- [A Gentle Introduction to Rust](https://stevedonovan.github.io/rust-gentle-intro/readme.html) - a tour through some of the great Rust features
- [Rust for Node developers](https://github.com/Mercateo/rust-for-node-developers) - a big inspiration for this article

#### Projects

- [Writing an OS in Rust](https://os.phil-opp.com/) - incredible project, I aspire to one day be this good
- [IntermezzOS](http://intermezzos.github.io/) - more operating systems
- [Roguelike Tutorial - In Rust](http://bfnightly.bracketproductions.com/rustbook/chapter_0.html) - I haven't gone through this one yet myself, but I've heard really good things

#### Other

- [Rustlings](https://github.com/rust-lang/rustlings) - awesome interactive learning tool
- [Exercism.io](https://exercism.io/tracks/rust) - more small, interactive projects
- [Read Rust](https://readrust.net/) - great source for Rust related news and happenings
