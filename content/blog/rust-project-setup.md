+++
title = "cargo-generateでrustプロジェクトを作り方"
date = 2023-08-28
+++

rust開発者たちは新しいプロジェクトを作成する時に手動で何度でも同じように設定するかもしれません。これは大変ですから、今日は[cargo-generate](https://github.com/cargo-generate/cargo-generate)でテンプレートレポジトリの作り方をご案内します。完成させたテンプレートプロジェクトに興味が持つ方は[こちら](https://github.com/MrPicklePinosaur/pino_template)にご覧ください。

☆では、始めましょう☆。

まず`cargo`で新しいプロジェクトを作成します。
```sh
mkdir rust_template
cd rust_template
cargo init
```

テンプレートから新しいプロジェクトを作成する時にリモートgitリポジトリがあるほうが便利ですので、リモートリポジトリ（githubやgitlabなど)にプッシュすることがおすすめです。

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

`cargo-generate`をインストールします。
```sh
cargo install cargo-generate
```

`cargo-generate`はテクストテンプレート化の機能

読んでいただきありがとうございます。

