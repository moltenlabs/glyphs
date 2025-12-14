<p align="center">
  <img src="https://raw.githubusercontent.com/moltenlabs/sigil/main/.github/assets/banner.png" alt="Sigil" width="100%" />
</p>

<h1 align="center">✨ Sigil</h1>

<p align="center">
  <strong>Human-readable ANSI escape sequences for Rust.</strong>
</p>

<p align="center">
  <a href="https://crates.io/crates/sigil"><img src="https://img.shields.io/crates/v/sigil.svg?style=flat-square&logo=rust" alt="Crates.io"></a>
  <a href="https://docs.rs/sigil"><img src="https://img.shields.io/docsrs/sigil?style=flat-square&logo=docs.rs" alt="Documentation"></a>
  <a href="https://github.com/moltenlabs/sigil/actions"><img src="https://img.shields.io/github/actions/workflow/status/moltenlabs/sigil/ci.yml?style=flat-square&logo=github" alt="CI"></a>
  <a href="#license"><img src="https://img.shields.io/badge/license-MIT%2FApache--2.0-blue?style=flat-square" alt="License"></a>
</p>

<p align="center">
  <a href="#features">Features</a> •
  <a href="#installation">Installation</a> •
  <a href="#quick-start">Quick Start</a> •
  <a href="#parsing">Parsing</a> •
  <a href="#ecosystem">Ecosystem</a>
</p>

---

## What is Sigil?

**Sigil** is a Rust library for working with ANSI escape sequences. It lets you:

1. **Style terminal output** with a fluent, type-safe API
2. **Parse existing sequences** into human-readable descriptions
3. **Strip ANSI codes** from strings
4. **Control cursors and screens** with named constants

Think of it as the Rust equivalent of [sequin](https://github.com/charmbracelet/sequin) from Charmbracelet.

```rust
use sigil::{style, Color, parse};

// Style text beautifully
let output = style("Hello, Terminal!")
    .fg(Color::rgb(249, 115, 22))  // Molten Orange
    .bold()
    .to_string();

println!("{}", output);

// Parse mystery sequences
for segment in parse("\x1b[1;31mError\x1b[0m") {
    println!("{:?}", segment);
    // Escape: [SGR (style)] bold, red fg
    // Text: "Error"
    // Escape: [SGR (style)] reset
}
```

---

## Features

<table>
<tr>
<td width="50%">

### 🎨 Fluent Styling API
```rust
style("text")
    .fg(Color::Red)
    .bg(Color::Black)
    .bold()
    .italic()
    .underline()
```

</td>
<td width="50%">

### 🔍 Sequence Parsing
```rust
parse("\x1b[31mred\x1b[0m")
// → [Escape(red fg), Text("red"), Escape(reset)]
```

</td>
</tr>
<tr>
<td width="50%">

### 🌈 Full Color Support
- Standard 16 colors
- 256-color palette
- True color (24-bit RGB)
- Hex color parsing

</td>
<td width="50%">

### 🧹 String Utilities
```rust
strip_ansi("\x1b[31mHello\x1b[0m")  // "Hello"
visible_len("\x1b[31mHello\x1b[0m") // 5
```

</td>
</tr>
</table>

---

## Installation

```bash
cargo add sigil
```

Or add to your `Cargo.toml`:

```toml
[dependencies]
sigil = "0.1"
```

### With Molten Brand Colors

```toml
[dependencies]
sigil = { version = "0.1", features = ["brand"] }
```

---

## Quick Start

### Basic Styling

```rust
use sigil::{style, Color};

// Simple colors
let red = style("Error!").fg(Color::Red).to_string();
let warning = style("Warning").fg(Color::Yellow).bold().to_string();

// RGB colors
let custom = style("Custom")
    .fg(Color::rgb(255, 128, 0))
    .to_string();

// Hex colors
let hex = style("Hex")
    .fg(Color::from_hex("#F97316"))
    .to_string();

// 256-color palette
let palette = style("Palette")
    .fg(Color::ansi256(208))
    .to_string();
```

### Text Modifiers

```rust
use sigil::{style, Color};

let styled = style("Important")
    .bold()           // Bold text
    .italic()         // Italic
    .underline()      // Underlined
    .strikethrough()  // Strikethrough
    .dim()            // Dimmed
    .reverse()        // Inverted colors
    .blink()          // Blinking (if supported)
    .to_string();
```

### Combining Styles

```rust
use sigil::{style, Color};

let fancy = style("🔥 Molten Labs")
    .fg(Color::rgb(249, 115, 22))
    .bold()
    .underline()
    .to_string();

println!("{}", fancy);
```

---

## Parsing

### Parse ANSI Sequences

Turn cryptic escape codes into readable descriptions:

```rust
use sigil::{parse, ParsedSequence};

let input = "\x1b[1;38;2;249;115;22mMolten\x1b[0m";

for segment in parse(input) {
    match segment {
        ParsedSequence::Text(text) => {
            println!("Text: {}", text);
        }
        ParsedSequence::Escape(escape) => {
            println!("Escape: {}", escape.human_readable());
            // "bold, fg: rgb(249, 115, 22)"
        }
    }
}
```

### Strip ANSI Codes

```rust
use sigil::strip_ansi;

let styled = "\x1b[1;31mBold Red\x1b[0m Normal";
let plain = strip_ansi(styled);
assert_eq!(plain, "Bold Red Normal");
```

### Get Visible Length

```rust
use sigil::visible_len;

let styled = "\x1b[31mHello\x1b[0m";
assert_eq!(visible_len(styled), 5);  // Not 14!
```

---

## Cursor & Screen Control

### Cursor Movement

```rust
use sigil::cursor;

// Move cursor
print!("{}", cursor::up(5));      // Move up 5 lines
print!("{}", cursor::down(3));    // Move down 3 lines
print!("{}", cursor::left(10));   // Move left 10 columns
print!("{}", cursor::right(10));  // Move right 10 columns

// Position cursor
print!("{}", cursor::goto(10, 20));  // Row 10, Column 20
print!("{}", cursor::column(1));      // Start of line
```

### Screen Control

```rust
use sigil::sequences;

// Clear screen
print!("{}", sequences::CLEAR_SCREEN);
print!("{}", sequences::CLEAR_LINE);

// Cursor visibility
print!("{}", sequences::CURSOR_HIDE);
// ... do stuff ...
print!("{}", sequences::CURSOR_SHOW);

// Alternate screen buffer (for TUIs)
print!("{}", sequences::ALT_SCREEN_ENTER);
// ... your TUI ...
print!("{}", sequences::ALT_SCREEN_EXIT);
```

---

## With Molten Brand

Enable the `brand` feature for pre-defined brand colors:

```rust
use sigil::{style, brand};

// Molten Labs brand colors
let molten = style("Molten").fg(brand::MOLTEN).bold();
let goblin = style("Goblin").fg(brand::GOBLIN);
let success = style("Success").fg(brand::SUCCESS);
let error = style("Error").fg(brand::ERROR);

println!("{} {} {} {}", molten, goblin, success, error);
```

---

## Color Reference

### Standard Colors

| Color | Code | Bright Variant |
|-------|------|----------------|
| `Color::Black` | 30 | `Color::BrightBlack` |
| `Color::Red` | 31 | `Color::BrightRed` |
| `Color::Green` | 32 | `Color::BrightGreen` |
| `Color::Yellow` | 33 | `Color::BrightYellow` |
| `Color::Blue` | 34 | `Color::BrightBlue` |
| `Color::Magenta` | 35 | `Color::BrightMagenta` |
| `Color::Cyan` | 36 | `Color::BrightCyan` |
| `Color::White` | 37 | `Color::BrightWhite` |

### RGB & Hex

```rust
Color::rgb(255, 128, 0)      // Orange
Color::from_hex("#F97316")   // Molten Orange
Color::from_hex("7C3AED")    // Goblin Purple (# optional)
```

### 256-Color Palette

```rust
Color::ansi256(0)     // Black
Color::ansi256(196)   // Bright red
Color::ansi256(208)   // Orange
Color::ansi256(255)   // White
```

---

## Modifier Reference

| Modifier | ANSI Code | Description |
|----------|-----------|-------------|
| `.bold()` | 1 | **Bold text** |
| `.dim()` | 2 | Dimmed text |
| `.italic()` | 3 | *Italic text* |
| `.underline()` | 4 | <u>Underlined</u> |
| `.blink()` | 5 | Blinking text |
| `.reverse()` | 7 | Inverted colors |
| `.hidden()` | 8 | Hidden text |
| `.strikethrough()` | 9 | ~~Strikethrough~~ |

---

## Ecosystem

Sigil is part of the **Molten Labs** open source ecosystem:

| Crate | Description | Status |
|-------|-------------|--------|
| **[molten-brand](https://github.com/moltenlabs/molten-brand)** | Design tokens & colors | ✅ Released |
| **[sigil](https://github.com/moltenlabs/sigil)** | ANSI sequences (you are here) | ✅ Released |
| **[lacquer](https://github.com/moltenlabs/lacquer)** | Terminal styling (like lipgloss) | 🚧 Coming Soon |
| **[cauldron](https://github.com/moltenlabs/cauldron)** | TUI framework (like bubbletea) | 📋 Planned |

---

## Why "Sigil"?

In fantasy lore, a **sigil** is a magical symbol or mark. In Sigil, ANSI escape sequences are the magical marks that transform plain text into beautiful terminal output. ✨

---

## Performance

- **Zero allocations** for static styling
- **Compile-time** color constants
- **Efficient parsing** with minimal copies
- Suitable for hot paths and TUI rendering loops

---

## Contributing

We welcome contributions! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

```bash
git clone https://github.com/moltenlabs/sigil
cd sigil
cargo test
```

---

## License

Licensed under either of:

- Apache License, Version 2.0 ([LICENSE-APACHE](LICENSE-APACHE))
- MIT license ([LICENSE-MIT](LICENSE-MIT))

at your option.

---

<p align="center">
  <sub>Built with ✨ by <a href="https://github.com/moltenlabs">Molten Labs</a></sub>
</p>

<p align="center">
  <sub><i>"Let them cook."</i></sub>
</p>
