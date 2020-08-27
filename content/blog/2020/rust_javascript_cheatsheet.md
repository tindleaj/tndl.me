+++
title = "A JavaScript Developer's Cheatsheet for Rust"
description = "A cheatsheet for JavaScripters looking to ramp up quickly on Rust"
date = 2020-08-23
draft = false
+++

Learning a new programming language is a great opportunity to learn new universal concepts and patterns that apply to all languages, not just the one you're learning. However, before you can get a handle on all the new stuff a language provides, first you have to figure out how to write the new language like you would write whatever old language(s) you know.

For the JavaScript developer, Rust offers a plethora of new and sometimes brain-bending concepts that exist in Rust but not in JavaScript. But in order to appreciate those concepts, first you have to get a handle on the basic syntax of the language. To speed up that process, you can use the JavaScript you already know to draw parallels to the Rust equivalents.

This cheatsheet provides some basic comparisons between JavaScript syntax and their parallels in Rust. It purposefully sticks to the basics that have decent parallels, to get you comfortable writing some simple programs in Rust.

Don't let the label of "Systems Programming Language" discourage you. Rust is an incredibly accessible language, in more ways than one. Use what you already know and [learn some Rust](https://tndl.me/blog/2020/introduction-to-rust/)!

## Variables (bindings)

Rust variables are immutable by default. This is sort of like having all variables be `const` in JavaScript. JavaScript `const` is shallow, but Rust variables cannot be mutated at all unless you declare that variable `mut`.

```javascript
// JavaScript

let value = 10;
let greeting = "Hello!";
let counter = 0;

counter += 1;
```

```rust
// Rust

let value = 10; // Cannot be changed
let greeting = "Hello!"; // Also immutable
let mut counter = 0; // This can be changed

counter += 1;

```

## Printing Output

Rust's `println!` takes a string argument, which sort of acts like a JavaScript template string.

```javascript
// JavaScript

let name = "Austin Tindle";
console.log(`Hello ${name}!`);
```

```rust
// Rust

let name = "Austin Tindle";
println!("Hello {}!", name);

```

## Functions

The `main` function in Rust is the entrypoint to the program, and other functions need to be called from `main`. In JavaScript there is no special entrypoint function.

```javascript
// JavaScript

function weather() {
  console.log("Sunny!");
}

weather();
```

```rust
// Rust

fn weather() {
  println!("Sunny!");
}

fn main() {
  weather();
}

```

## Conditionals

### If/Else

```javascript
// JavaScript

if (true === false) {
  console.log("Never happens.");
} else if (false === true) {
  console.log("Also never happens.");
} else {
  console.log("Perfection.");
}
```

```rust
// Rust

if true == false {
  println!("Impossible!");
} else if false == true {
  println!("Still impossible!");
} else {
  println!("This works.");
}

```

Unlike in JavaScript, Rust does not have "truthy" values. It's strict static typing means that conditional expressions need to evaluate to a `bool`.

```rust
// Rust

let not_a_boolean = "I'm a String";

if not_a_boolean {
  // Error: mismatched types expected `bool`, found `&str`
}

```

### Switch & Match

Switch statements aren't as widely used in JavaScript as if/else, but match statements in Rust are very popular. They aren't exactly the same, and match statements have a lot of powerful uses not available to JavaScript switch statements.

```javascript
// JavaScript

let stone = "Thunder Stone";

switch (stone) {
  case "Thunder Stone":
    console.log("Jolteon!");
    break;
  case "Water Stone":
    console.log("Vaporeon!");
    break;
  case "Fire Stone":
    console.log("Flareon!");
    break;
  default:
    console.log("Eevee!");
}
```

```rust
// Rust

let stone = "Thunder Stone";

match stone {
  "Thunder Stone" => println!("Jolteon!"),
  "Water Stone" => println!("Vaporeon!"),
  "Fire Stone" => println!("Flareon!"),
  _ => println!("Eevee!")
}

```

## Importing Other Code Using Modules

In Rust, any function marked `pub` can be imported into another file. Any file that is not `main.rs` or `lib.rs` automatically gets a namespace based on its source file name. The `mod` keyword pulls in source files with equivalent filenames.

The `use` keyword brings nested items into the current scope, sort of like the `import {x} from 'y'` syntax in JavaScript.

```javascript
// JavaScript

// houston.js
export default function launch() {
  console.log("Liftoff!");
}

export function abort() {
  console.log("Aborting!");
}
```

```javascript
// JavaScript

// main.js
import launch, { abort } from "./houston";

launch();
abort();
```

```rust
// Rust

// houston.rs
pub fn launch() {
  println!("Liftoff!");
}

pub fn abort() {
  println!("Aborting!");
}

```

```rust
// Rust

// main.rs
mod houston;

use houston::{ launch };

fn main() {
  launch();
  houston::abort();
}

```

## Arrays & Vectors

Rust has a data type called 'array', but it's not the type of array we're used to in JavaScript. A growable list is called a Vector in Rust, and is available via

```javascript
// JavaScript

let languages = ["JavaScript", "TypeScript", "Rust", "HTML"];
languages.pop();

console.log(languages[0]);
```

```rust
// Rust

// A shorthand macro syntax
let mut languages = vec!["JavaScript", "TypeScript"];
languages.push("Rust");

// Full syntax
let mut alphabets = Vec::new();
alphabets.push("Greek");
alphabets.push("Roman");


println!("{} {}", languages[2], alphabets[0]);

```

## Iterating

```javascript
// JavaScript

let utensils = ["Fork", "Spoon", "Spork", "Knife"];

for (let utensil of utensils) {
  console.log(`Eating with a ${utensil}.`);
}
```

```rust
// Rust

let utensils = vec!["Fork", "Spoon", "Spork", "Knife"];

for utensil in utensils {
  println!("Eating with a {}.", utensil);
}

```

## Other Resources

- [Introduction to Rust for Node Developers](https://tndl.me/blog/introduction_to_rust) A project-based intro to Rust.
- [RustConf 2020 - Rust for Non-Systems Programmers by Rebecca Turner](https://www.youtube.com/watch?v=BBvcK_nXUEg&list=PL85XCvVPmGQijqvMcMBfYAwExx1eBu1Ei&index=10&t=0s) Fantastic talk and an inspiration for this resource.
- [Rust for JavaScript Developers Blog Series by Sheshbabu Chinnakonda](http://www.sheshbabu.com/posts/rust-for-javascript-developers-tooling-ecosystem-overview/) A great intro series on Rust for JavaScripters.

_Are you a JavaScript developer trying to learn Rust? Send me an email at [tindleaj@gmail.com](mailto:tindleaj@gmail.com). I'm working on stuff you'll be interested in._
