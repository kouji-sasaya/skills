# gh extension skill template

このリポジトリは、Rust で GitHub CLI 拡張コマンドを作るためのスキルです。

## 目的

`gh skill install` でインストールしたあと、Copilot に対して次のような依頼をすると、
Rust の `gh` extension をすぐに作れるようにします。

- 「Rust で `gh-animal` のような gh extension を作って」
- 「`gh animal` のようなサブコマンド付きの extension を作って」
- 「`gh` 拡張コマンドのテンプレートを生成して」

## スキルの配置

GitHub CLI の Agent Skills 仕様では、スキルは次の形式で配置します。

```text
skills/gh-rust-extension/SKILL.md
```

`gh skill install` では、リポジトリ名とスキル名を分けて指定します。

## インストールのイメージ

```bash
gh skill install <owner>/<repo> gh-rust-extension
```

または、正確なパスを指定する場合:

```bash
gh skill install <owner>/<repo> skills/gh-rust-extension
```

> 注意: `OWNER/REPO/skill` のような形ではなく、`OWNER/REPO` と `skill` を分けて指定します。

## 参考にしたテンプレート

- https://github.com/kouji-sasaya/gh-animal

このテンプレートは、Rust で `gh` extension を作る際の実践例として使いやすく、
`gh extension create` で選ぶ `Other Precompiled (C++, Rust, etc)` と同じ方向性を持ちます。

## 生成される典型構成

```text
.
├── Cargo.toml
├── build.sh
├── README.md
├── data/
├── src/
│   └── main.rs
└── .github/
    └── workflows/
        └── release.yml
```

## 典型的な使用例

```text
gh extension create
? Extension name: gh-animal
? What kind of extension? Other Precompiled (C++, Rust, etc)
```

その後、Copilot / agent に対して以下を指示すると、Rust ベースの extension を素早く作成できます。

```text
`gh-animal` のような Rust の GH extension をテンプレートにして、`gh foo` というコマンドを作成して。
サブコマンドは `dog`, `cat`, `fox` を持つようにして。
build.sh と README も含めて作成して。
```

## 期待される効果

- スキルを入れるだけで、Rust での `gh` extension 開発のテンプレートが使える
- `gh extension create` の手動作業を省略できる
- `build.sh` と release workflow を含む実用的な雛形をすぐ生成できる
