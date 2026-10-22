---
name: gh-rust-extension
description: "Rust で GitHub CLI 拡張コマンドを作るためのテンプレート。gh-animal を参考に、簡単に gh extension を生成する。"
---

# gh-rust-extension

You are creating a new Rust-based GitHub CLI extension by starting from the template repository at https://github.com/kouji-sasaya/gh-animal.

## Required workflow

When the user asks for a new `gh` extension, follow this exact template process:

1. Use the current directory name as the GitHub CLI extension base name.
   - The extension repository/binary name must always be `gh-<current-dir-name>`.
   - Example: if the directory is `gh-docker`, the project/package/binary name must be `gh-docker`.
   - The user-facing GitHub CLI command is the suffix after `gh-`, so `gh-docker` is invoked as `gh docker` after installation.
   - Do not hardcode `docker` or any other fixed name; the naming is derived from the current folder.

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

4. Rename the extension to the current directory name in the `gh-<name>` form.
   - Example: if the current folder is `gh-docker`, then change the package name, binary name, README examples, and command references to `gh-docker`.
   - The installed command is still `gh docker`, but the project/binary name is `gh-docker`.
   - Do not keep the original `gh-animal` naming in the final result.
   - The directory name is the canonical base for the extension name, but the actual binary is always `gh-<name>`.

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

## Critical anti-pattern guardrails

Before making any code changes, enforce these rules:

- Do not invent new commands or files unless explicitly requested by the user.
- Do not add `src/command/docker.rs` or any other command file that was not requested.
- Do not rename modules from `animal` to `docker` unless the user explicitly asked for that transformation.
- Do not translate `gh animal` into `gh docker` by ad hoc search-and-replace without checking the exact call sites and module declarations.
- Do not create extra subcommands, aliases, wrappers, or helper modules just because the project name changed.
- Do not perform repeated rename-and-build loops. One rename pass, validate once, then stop.

When renaming from gh-animal to a new project name, use this exact sequence:

1. Identify the real names that must change.
2. Change only the names that are directly tied to the project name.
3. Validate with `cargo build` once.
4. If there are compile errors, fix the root cause directly instead of creating new aliases or duplicate modules.

## Required rename discipline

For project `gh-docker`, the following must be consistent:

- project/package name: `gh-docker`
- binary command name: `gh-docker`
- README usage examples: `gh docker` or `gh-docker` only if consistent with the actual binary name
- command module names: only change when the user explicitly requests a new command structure
- root command references: do not create unrelated extra modules like `src/command/docker.rs` if the user did not ask for it

For a specific rename such as `gh-myapp`:

- the Rust file must be renamed to reflect the actual command name, e.g. `myapp.rs`
- the command type must match the file name, e.g. `MyappCommand`
- all imports and module declarations must match that name exactly
- if the original code had `animal.rs` or `AnimalCommand`, those names must be replaced with `myapp.rs` and `MyappCommand` only when the user has explicitly requested the new command name
- never keep old names like `AnimalCommand` or `animal.rs` when the project name has changed to `gh-myapp`

When the user requests `gh-myapp`, do not assume `gh animal` and `src/command/animal.rs` are still valid. Rename the actual file and symbol names to match the new project command, and update all module paths and references accordingly.

## Failure-prevention rules

- If the current project is based on gh-animal, the rename should be a controlled, minimal edit.
- If the build starts failing repeatedly after renames, stop and inspect the actual compile errors instead of continuing to rename more symbols.
- Avoid creating new command files in response to a naming change unless the user specifically requested a new command.
- Avoid changing `animal` to `docker` by replacing every occurrence blindly; update only the file names and references that are required by the actual extension contract.
- When the project is renamed to `gh-myapp`, the command module must become `myapp.rs`, and the associated type must become `MyappCommand`.
- The rename must be reflected in the file path, module declaration, import statement, and struct/type name in the same edit pass.
- Do not leave stale references such as `src/command/animal.rs`, `AnimalCommand`, or `gh animal` in a `gh-myapp` project.

## Final rules

- The primary template is `https://github.com/kouji-sasaya/gh-animal`.
- Always start from an empty directory.
- Check that `.git` does not exist before cloning.
- The extension package/binary name is always `gh-<current-dir-name>`.
- The installed user command is the suffix after `gh-`.
  - Example: directory `gh-docker` => project/binary name `gh-docker`, installed command `gh docker`.
- Do not hardcode a fixed name like `docker` or `animal` unless the user explicitly requests it.
- Do not invent a custom structure that differs from the gh-animal template.
- Do not add unintended command modules, aliases, or side features.
- Adapt the contents to the current directory name, subcommands, and behavior, but keep the same repository architecture and release workflow.
- If the user says "make a gh extension like gh-animal", clone the template repository and build on top of it.

This skill is specifically designed to scaffold a Rust `gh` extension by cloning the gh-animal repository as the starting template while preventing naming drift and build-loop mistakes.
