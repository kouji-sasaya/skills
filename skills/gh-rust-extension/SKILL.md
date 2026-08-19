---
name: gh-rust-extension
description: "Rust で GitHub CLI 拡張コマンドを作るためのテンプレート。gh-animal を参考に、簡単に gh extension を生成する。"
---

# gh-rust-extension

You are creating a new Rust-based GitHub CLI extension by starting from the template repository at https://github.com/kouji-sasaya/gh-animal.

## Required workflow

When the user asks for a new `gh` extension, follow this exact template process:

1. Use the current directory name as the extension command name.
   - If the folder is named `gh-docker`, the extension name must become `gh-docker`.
   - The project name, binary name, package name, README command examples, and usage examples must all use the current directory name.
   - The command used by `gh` will become `gh <current-dir-name>`.
   - Example: `gh-docker` => `gh docker` if the project is named `gh-docker` and the binary is `gh-docker`.

2. Start from a clean empty directory.
   - The target project directory must be empty.
   - Confirm that there is no `.git` directory, no tracked files, and no existing source files.
   - This is required because the template is created by:
     ```bash
     git clone https://github.com/kouji-sasaya/gh-animal .
     ```
   - If `.git` exists or the directory is not empty, stop and ask the user to use an empty folder first.

2. Clone the real template repository into the empty folder.
   ```bash
   git clone https://github.com/kouji-sasaya/gh-animal .
   ```

3. Treat the cloned repository as the base template.
   - Keep the same overall structure and conventions from gh-animal.
   - Preserve the layout for:
     - `src/`
     - `data/`
     - `build.sh`
     - `.github/workflows/`
     - `README.md`
   - Reuse the same pattern for a single Rust binary with subcommands.

4. Rename the extension to the current directory name.
   - Example: if the current folder is `gh-docker`, then change the package name, binary name, README examples, and any command references to `gh-docker`.
   - Do not keep the original `gh-animal` naming in the final result.
   - The directory name is the canonical name for the extension.

5. Keep the behavior and structure aligned with gh-animal.
   - Provide subcommands such as `dog`, `cat`, `fox`, `rat` when appropriate.
   - Use `clap` for parsing.
   - Keep command output simple and friendly.
   - If runtime data is required, load it from a `data/` directory next to the executable, not from the project root.

6. Prepare the project for release.
   - Make sure `cargo build` works.
   - Make sure `cargo run -- --help` works.
   - Make sure `./build.sh` generates release artifacts.
   - Package the binary and `data/` directory together.

## Template source

The project must be modeled directly on the repository:

https://github.com/kouji-sasaya/gh-animal

This is not a conceptual example only. It is the actual template to copy from.

## Required empty-directory check

Before running the clone, verify that:

```bash
ls -la
```

shows an empty directory with no `.git` directory and no tracked files.

If any of the following are present, do not proceed:

- `.git/`
- existing source files
- an uncommitted project already in the directory

The correct action is:

```bash
mkdir -p <target-dir>
cd <target-dir>
ls -la
# confirm empty
git clone https://github.com/kouji-sasaya/gh-animal .
```

## Suggested project structure

After cloning, keep a structure like this:

```text
.
├── Cargo.toml
├── build.sh
├── README.md
├── data/
├── src/
│   └── main.rs
├── .github/
│   └── workflows/
│       └── release.yml
└── .git/
```

## Example command pattern

If the current directory is `gh-docker`, use the project name and command name as `gh-docker` throughout:

```rust
use clap::{Parser, Subcommand};

#[derive(Parser)]
#[command(name = "gh-docker")]
struct Cli {
    #[command(subcommand)]
    command: Commands,
}

#[derive(Subcommand)]
enum Commands {
    Run,
    Status,
    Logs,
}

fn main() {
    let cli = Cli::parse();
    match cli.command {
        Commands::Run => println!("run"),
        Commands::Status => println!("status"),
        Commands::Logs => println!("logs"),
    }
}
```

## Final rules

- The primary template is `https://github.com/kouji-sasaya/gh-animal`.
- Always start from an empty directory.
- Check that `.git` does not exist before cloning.
- Use the current directory name as the extension name.
- Example: `gh-docker` => all relevant file names, package names, and CLI references should be `gh-docker`.
- Do not invent a custom structure that differs from the gh-animal template.
- Adapt the contents to the current directory name, subcommands, and behavior, but keep the same repository architecture and release workflow.
- If the user says "make a gh extension like gh-animal", clone the template repository and build on top of it.

This skill is specifically designed to scaffold a Rust `gh` extension by cloning the gh-animal repository as the starting template.
