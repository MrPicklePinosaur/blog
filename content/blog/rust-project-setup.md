+++
title = "cargo-generateでrustプロジェクトを作り方"
date = 2023-08-28
+++

Rust開発者たちは新しいプロジェクトを作成するたびに、同じ設定を手動で何度も行う必要があるかもしてません。これは大変ですから、今日は[cargo-generate](https://github.com/cargo-generate/cargo-generate)でテンプレートレポジトリの作り方を紹介したいと思います。完成したテンプレートプロジェクトに興味がある方は、[こちら](https://github.com/MrPicklePinosaur/pino_template)をご覧ください。

☆では、始めましょう☆。

まず`cargo`で新しいプロジェクトを作成します。
```sh
mkdir rust_template
cd rust_template
cargo init
```

テンプレートから新しいプロジェクトを作成する時に、リモートgitリポジトリを持っていると便利です。そのため、リモートリポジトリ（githubやgitlabなど)にプッシュすることをおすすめします。

## フォーマッター

コッドフォーマッターとは、ホワイトスペースやインポット順序を自動的に修正するプログラムです。
rustの場合はrustfmtを使用ます。

rustfmt.tomlファイルでrustfmtを設定することができます。
自分のお好みに合わせて設定してください。全ての設定については[こちら](https://rust-lang.github.io/rustfmt/?version=v1.4.36&search=)をご覧ください。

私が使用した設定を説明します。`group_imports`とは同じクレートからのインポットをぐループ化するものことです。

次は`use_field_init_shorthand`をtrueに設定すると、ストラクトを初期化する時に、ストラクトのフィールドとバリアバル名が同じ場合、フィールド名を略します。
```toml
group_imports = "StdExternalCrate"
imports_layout = "Mixed"
imports_granularity = "Crate"
match_block_trailing_comma = true
trailing_semicolon = true
use_field_init_shorthand = true
```

## リンター

次、リンターの機能についてを説明します。プログラムが問題なくコンパイルできても、バッグがなくても、コッドがより習慣的に記述されることがあります。この場合、リンターは通知だけではなく、コッドは自動的に修正することもできます。rustエコシステムにはclippyというリンターを使用します。

rustfmtと同じように、コンフィグファイルで設定することができます。個人的にはデフォルトのままにします。


## コマンドランナー　justfile

現在、開発者たちは`makefile`がコマンドランナーとして使っています。たとえば
```makefile

.PHONY: debug check fmt lint

debug:
    cargo run

check:
    cargo check --workspace

fmt:
    cargo +nightly fmt --all

lint:
    cargo clippy -- -W clippy::unwrap_used -W clippy::cargo

```

`makefile`は非常に複雑なビルドシステムです。そのため全く使わない機能やルールが数多く存在します。さらに、システムによっては`makefile`のルールが異なるため、コマンドの実行が誤って行われることがあります（`gmake`と`make`の違いについてははこちらをご覧ください）。これはかなり大変ですね。

`justfile`を紹介したいと思います。`justfile`のシンタックスは`makefile`に似てるけど、あくまでも`justfile`はただのコマンドランナーです。
このまえの`makefile`の内容を`justfile`で書かれたらこうなります。
```makefile

debug:
    cargo run

check:
    cargo check --workspace

fmt:
    cargo +nightly fmt --all

lint:
    cargo clippy -- -W clippy::unwrap_used -W clippy::cargo

```

## gitフック

この前紹介したフォーマッターとリンターは便利ですが、通常、コマンドラインから手動で呼び出す必要があります。実際、フォーマッタとリンターを呼び出す一番適当なタイミングははコミットする際です。ファイルを保存するときにもそうですが、これが少し邪魔かもしれません。保存ときにコマンドを自動的に実行する方法は、使用しているテキストエディタによって変わりますから自分のエディタに合った方法を検索してみてください。

それでは、gitフックについてご紹介しましょう。gitリポジトリでは、特定のエベントが発生した場合（例えば、コミットやプッシュときなど）、スクリプトを実行することの機能が提供されています。`.git/hooks`ディレクトリには、利用可能なフックが格納されていますので、自分で確認してみてください。このディレクトリには`.sample`という拡張子のついた実行可能ファイルが含まれていますが、現状では実行されません。ファイル拡張子を取り除いた場合に実行できるようになります。

カスタムフックを作成する前に、プロジェクトのルートで`dev/scripts`ディレクトリを作成しましょう。これは`.git/hook`ディレクトリがバージョン管理に含まらないため、他の開発者がフックを手動でコピーする必要があります。`justfile`にはこのコマンドを追加しましょう。
```makefile
devsetup:
    cp dev/hooks/* .git/hooks
```
これで、プロジェクトをクローンしたあと、このコマンドを実行するこどでフックをインストールできます。

さて、カスタムコミットフックを紹介します。
```sh
#!/bin/sh

BOLD='\033[1;36m'
NC='\033[0m'

# フォーマッター
printf "${BOLD}[PRE-COMMIT]${NC} formatting code...\n"
cargo +nightly fmt --all
git add -u

# リンター
printf "${BOLD}[PRE-COMMIT]${NC} linting...\n"
cargo clippy
```
`cargo fmt`コマンドには`+nightly`フラグが付けている理由は、この前`rustfmt.toml`にベータフォーマッター機能が設定されたためです。また、フォーマッターコマンドの後に、`git add -u`が実行しない場合、現在のコミットにはフォーマッターによる変更が含まれないことに注意してください。

## cargo-generate

まず`cargo-generate`をインストールします。おそらくすこし時間がかかります。
```sh
cargo install cargo-generate
```

`cargo-generate`はテクストテンプレート化の機能があります。プロジェクトがテンプレートから作成する時にプレースホルダは[組み込みの変数](https://cargo-generate.github.io/cargo-generate/templates/builtin_placeholders.html)の値で置き換えられます。以下はクレート名によって`Cargo.toml`で設定する例です。
```toml
[package]
name = "{{crate_name}}"
version = "0.1.0"
edition = "2021"
license = "MIT OR Apache-2.0"
authors = ["{{authors}}"]
description = ""
repository = ""
```
`Cargo.toml`に限らわず、どこでもこのプレースホルダ文法が使用できます。ぜひ自分に合わせる設定してみてください！

新しいプロジェクトを作成したい場合、このコマンドを実行します。
```sh
cargo generate --git <git-url> -f --name <プロジェクト名>
````
<git-url>`はテンプレートのリモートレポジトリのURLです。代わりに`--git`フラグではなく`--path`フラグでローカルファイルシステムからテンプレートレポジトリを使用できます（詳細について`cargo-generate`のマニュアルを読んでみてください）。

では、以上になります！読んでいただきありがとうございました！

