# A JavaScripter's Cheatsheet for Rust
Or, Learn How to Write Rust Like You Would JavaScript

### Variables (bindings)
```javascript
let value = 10
let greeting = "Hello!"
let counter = 0

counter += 1
```

```rust
let value = 10;
let greeting = "Hello!";
let mut counter = 0;

counter += 1;
```

### Printing Output

```javascript
let name = "Austin Tindle"
console.log(`Hello ${name}`)
```

```rust
let name = "Austin Tindle";
println!("Hello {}", name);
```

### Functions

### Conditionals

### Importing Other Code (modules)
```javascript
// houston.js
export default function launch() {
  console.log("Liftoff!")
}

export function abort() {
  console.log("Aborting!")
}
```

```javascript
// main.js
import launch, { abort } from "houston.js"

launch()
abort()
```

```rust
// houston.rs
pub fn launch() {
  println!("Liftoff!");
}

pub fn abort() {
  println!("Aborting!");
}
```

```rust
// main.rs
mod houston;

use houston::{ launch };

fn main() {
  launch();
  houston::abort();
}
```

### Arrays (Vectors)

```javascript
let languages = ["JavaScript", "TypeScript", "Rust", "HTML"]
languages.pop()

console.log(languages[0])
```

```rust
let mut languages = vec!["JavaScript", "TypeScript"];
languages.push("Rust");

println!("{}", languages[2]);
```

### Iterating