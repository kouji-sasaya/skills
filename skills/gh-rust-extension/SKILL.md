---
name: gh-rust-extension
description: "Rust で GitHub CLI 拡張コマンドを作るためのテンプレート。gh-animal を参考に、簡単に gh extension を生成する。"
---

# gh-rust-extension

You are creating a GitHub CLI extension in Rust. Use the repository at https://github.com/kouji-sasaya/gh-animal as the reference pattern.

## Goal

Generate a project that can be installed as a `gh` extension and behaves like:

- `gh <extension-name> <subcommand>`
- a single Rust binary compiled as a GitHub CLI extension
- a release pipeline that publishes platform binaries
- local data files bundled next to the binary

## Reference pattern

Use the following ideas from gh-animal:

- `gh extension create` is the starting point
- choose `Other Precompiled (C++, Rust, etc)` when asked which kind of extension
- create a Rust binary whose package name is `gh-<extension-name>`
- define commands with subcommands such as `dog`, `cat`, `fox`, `rat`
- package the binary alongside a `data/` directory in release artifacts
- build scripts create `dist/...` output for each platform

## Default approach

If the user asks to create a new extension, do the following:

1. Confirm the extension name.
   - The final binary command should be `gh-<name>`.
   - The command path used by `gh` will be `gh <name>`.

2. Confirm the subcommands and arguments.
   - Example: `gh animal dog`, `gh animal cat`, `gh animal fox`
   - Keep the CLI simple and easy to understand.

3. Create a Rust project scaffold with:
   - `Cargo.toml`
   - `src/main.rs`
   - `src/cli.rs` or `src/commands.rs`
   - `data/` directory if the extension needs bundled content
   - `build.sh`
   - `.github/workflows/release.yml`
   - `README.md`

4. Use `clap` for subcommand parsing.
   - `use clap::{Parser, Subcommand};`
   - Define `enum Command { ... }`
   - Implement `match` for each command.

5. Follow the gh-animal pattern:
   - the root binary handles subcommands
   - print help when no subcommand is passed
   - keep output concise and human-friendly
   - support quick local execution with `cargo run -- <subcommand>`

6. For packaged runtime data:
   - prefer reading files from `data/` next to the executable
   - support local development and packaged release builds
   - do not assume the current working directory is the repo root

7. Make the project release-ready:
   - compile the binary with `cargo build`
   - run `cargo run -- --help`
   - run `cargo test` if tests are present
   - run `./build.sh` to generate release artifacts
   - package the built binary and `data/` directory together

## Suggested Cargo.toml

Use a simple structure like this:

```toml
[package]
name = "gh-<extension-name>"
version = "0.1.0"
edition = "2021"

[dependencies]
clap = { version = "4", features = ["derive"] }
```

## Suggested command pattern

```rust
use clap::{Parser, Subcommand};

#[derive(Parser)]
#[command(name = "gh-<extension-name>")]
struct Cli {
    #[command(subcommand)]
    command: Commands,
}

#[derive(Subcommand)]
enum Commands {
    Dog,
    Cat,
    Fox,
    Rat,
}

fn main() {
    let cli = Cli::parse();
    match cli.command {
        Commands::Dog => println!("dog"),
        Commands::Cat => println!("cat"),
        Commands::Fox => println!("fox"),
        Commands::Rat => println!("rat"),
    }
}
```

## Build script pattern

Use a `build.sh` script similar to the gh-animal reference:

- build the binary for the target platform
- output artifacts to `dist/`
- copy `data/` into the release output
- ensure the artifact can be installed as a `gh` extension

Example shell flow:

```bash
#!/usr/bin/env bash
set -eu

mkdir -p dist
cargo build --release
cp target/release/gh-<extension-name> dist/
cp -R data dist/
```

## Release workflow

Add a GitHub Actions workflow that:

- runs on tag pushes like `v*`
- builds the binary for the target OS/architecture
- uploads release assets
- keeps `data/` bundled so runtime resources are available

## README template

Create a README with these sections:

- project overview
- installation instructions
- usage examples
- development steps
- build and release steps
- note that it is a Rust-based `gh` extension

## Preferred user-facing response

When the user asks for a new `gh` extension, provide:

1. a short explanation of what will be created
2. the directory structure to generate
3. the Rust code skeleton
4. the install command
5. a validation checklist (`cargo build`, `cargo run -- --help`, `./build.sh`)

## Install guidance

Tell the user to install the extension with:

```bash
gh extension install <owner>/<repo>
```

For a local extension during development:

```bash
gh extension install .
```

## Final rules

- Prefer small, clear command names.
- Keep the CLI to one binary with subcommands.
- Use the gh-animal repository structure as the template.
- Do not over-engineer; the goal is a simple and easy-to-release gh extension.
- If the user says "create a Rust gh extension", generate a ready-to-use starter project, not just a description.


This skill is intentionally modeled after the architecture and workflow in the gh-animal project, which is a practical Rust example of a GitHub CLI extension.
