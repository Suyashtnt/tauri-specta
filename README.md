<div align="center">
    <img height="150" src=".github/logo.png" alt="Specta Logo"></img>
    <h1>Tauri Specta</h1>
    <p><b>Typesafe Tauri commands</b></p>
    <a href="https://discord.gg/4V9M5sksw8"><img src="https://img.shields.io/discord/1011665225809924136?style=flat-square" alt="Discord"></a>
    <a href="https://crates.io/crates/tauri-specta"><img src="https://img.shields.io/crates/d/tauri-specta?style=flat-square" alt="Crates.io"></a>
    <a href="https://crates.io/crates/tauri-specta"><img src="https://img.shields.io/crates/v/tauri-specta.svg?style=flat-square"
    alt="crates.io" /></a>
    <a href="https://docs.rs/tauri-specta"><img src="https://img.shields.io/badge/docs-latest-blue.svg?style=flat-square" alt="docs.rs" /></a>
    <a href="/LICENSE.md"><img src="https://img.shields.io/crates/l/tauri-specta?style=flat-square" alt="License"></a>
</div>

<br>

## Install

```bash
cargo add specta
cargo add tauri-specta --features javascript,typescript
```

## Adding Specta to custom types

```rust
use specta::Type;
use serde::{Deserialize, Serialize};

// The `specta::Type` macro allows us to understand your types
// We implement `specta::Type` on primitive types for you.
// If you want to use a type from an external crate you may need to enable the feature on Specta.
#[derive(Serialize, Type)]
pub struct MyCustomReturnType {
    pub some_field: String,
}

#[derive(Deserialize, Type)]
pub struct MyCustomArgumentType {
    pub foo: String,
    pub bar: i32,
}
```

## Annotate your Tauri commands with Specta

```rust
#[tauri::command]
#[specta::specta] // <-- This bit here
fn greet3() -> MyCustomReturnType {
    MyCustomReturnType {
        some_field: "Hello World".into(),
    }
}

#[tauri::command]
#[specta::specta] // <-- This bit here
fn greet(name: String) -> String {
  format!("Hello {name}!")
}
```

## Export your bindings

```rust
use specta::collect_types;
use tauri_specta::{ts, js};

// this example exports your types on startup when in debug mode or in a unit test. You can do whatever.

fn main() {
    #[cfg(debug_assertions)]
    ts::export(collect_types![greet, greet2, greet3], "../src/bindings.ts").unwrap();

    // or export to JS with JSDoc
    #[cfg(debug_assertions)]
    js::export(collect_types![greet, greet2, greet3], "../src/bindings.js").unwrap();
}

#[test]
fn export_bindings() {
    ts::export(collect_types![greet, greet2, greet3], "../src/bindings.ts").unwrap();
    js::export(collect_types![greet, greet2, greet3], "../src/bindings.js").unwrap();
}
```

## Usage on frontend

```ts
import * as commands from "./bindings"; // This should point to the file we export from Rust

await commands.greet("Brendan");
```

## Known limitations

 - Your command can only take up to 10 arguments. Any more and you'll get a compile error. If you need more just use a struct.
 - Exporting your schema within a directory tracked by Tauri's hot reload will cause an infinite reload loop.

## Development

Run the example:

```bash
pnpm i
cd example/
pnpm tauri dev
```

## Credit

Created by [oscartbeaumont](https://github.com/oscartbeaumont) and [Brendonovich](https://github.com/brendonovich).
