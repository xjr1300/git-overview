# Gitの概要

- [Gitの概要](#gitの概要)
  - [Gitとは](#gitとは)
  - [Gitのインストール](#gitのインストール)
  - [Gitの設定](#gitの設定)
  - [Gitコマンドのヘルプ表示](#gitコマンドのヘルプ表示)
  - [Gitの3つのエリア](#gitの3つのエリア)
  - [ファイルのライフサイクル](#ファイルのライフサイクル)
  - [ローカルリポジトリの作成](#ローカルリポジトリの作成)
    - [新規にローカルリポジトリを作成する場合](#新規にローカルリポジトリを作成する場合)
    - [既存のリモートリポジトリをローカルリポジトリに複製（クローン）する場合](#既存のリモートリポジトリをローカルリポジトリに複製クローンする場合)
  - [追跡対象外設定](#追跡対象外設定)
  - [Gitの基本](#gitの基本)
    - [プロジェクトの作成](#プロジェクトの作成)
    - [リポジトリ作成後の変更状態の確認](#リポジトリ作成後の変更状態の確認)
    - [リポジトリの状態の確認と最初のコミット](#リポジトリの状態の確認と最初のコミット)
      - [コミットメッセージの入力](#コミットメッセージの入力)
    - [フィボナッチ関数の実装とコミット](#フィボナッチ関数の実装とコミット)
    - [フィボナッチ関数の単体テストの実装とコミット](#フィボナッチ関数の単体テストの実装とコミット)
    - [フィボナッチ数をキャッシュして同じ項のフィボナッチ数を計算しないように改善](#フィボナッチ数をキャッシュして同じ項のフィボナッチ数を計算しないように改善)
    - [変更履歴の確認](#変更履歴の確認)

## Gitとは

Gitは、プログラムのソースコード、設定ファイル、またはREADMEなど、主にテキストファイルの変更履歴を追跡し、チームでの共同開発やソースコードなどのバージョン管理（履歴管理）を支援します。
Gitの主な特徴を次に示します。

- 分散型:
  Gitは、全ての作業者がリポジトリ（プロジェクトのデータとその履歴が保存される場所）の完全なコピーを持ちます。
  よって、ネットワークに接続されていなくても、プログラムの**個人的な実装**ができます。
- ブランチ:
  ブランチは、リポジトリにおける独立した作業スペースを表します。
  基本的に変更は新たに作成したブランチに切り替えて行います。
  複数のブランチを作成し、それぞれで別々の作業を進めた後、必要に応じて実施した作業を統合できます。
- コミット:
  ファイルやディレクトリの変更をリポジトリに記録することをコミットと呼びます。
  各コミットには、コミットID、変更の要約や説明、変更した人物などの情報が含まれます。
- HEAD:
  現在のブランチの最新のコミットを示します。
  `checkout`コマンドでコミットIDを指定して過去のコミット（各ブランチの最新のコミット以外）に戻した場合、HEADは`detached HEAD`状態（HEADが最新のブランチを示していない状態）になります。
  `deatached HEAD`状態から復帰するには、`checkout`コマンドでブランチ名を指定します。
- リモートリポジトリ:
  リモートリポジトリは、チームメンバーや他の開発者とコードを共有するための中央のリポジトリです。
  GitHub、GitBucket及びGitLabなどのサービスにリポジトリを作成して、それをリモートリポジトリにできます。
- マージとリベース:
  マージは、ブランチで説明した異なるブランチの変更を統合するプロセスです。
  リベースは、ブランチの履歴を直線的にするために使用されます。

## Gitのインストール

[ここ](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)を参照してGitをインストールしてください。

## Gitの設定

あなたの名前（John Doe）とEメールアドレス（<johndoe@example.com>）を次の通りGitに登録します。
ここで登録した名前とEメールアドレスは、Gitで変更をコミットした人として、それぞれのコミットに記録されます。

```sh
git config --global user.name "John Doe"
git config --global user.email johndoe@example.com
```

`commit`コマンドなどを実行したとき、テキストに何らかの入力が求められることがあります。
このとき、テキストを編集するエディタを次の通り設定します（ここではテキストエディタとして`vim`を指定しています）。

```sh
git config --global core.editor vim
```

## Gitコマンドのヘルプ表示

Gitのコマンドのヘルプを表示したい場合は、次の通り実行します。
ここで`<verb>`はGitのコマンドを示します。

```sh
git help <verb>
```

Gitのコマンドのヘルプは１ページずつ表示されます。
ページの末尾に`:`が表示されている場合、スペースキーを押すことで次のページが表示されます。
ページの末尾に`(END)`が表示されている場合、現在表示しているページが最後のページです。
Gitコマンドのヘルプの表示を終了したい場合は、`q`を押します。

例として、`commit`コマンドのヘルプを表示する場合は、次を実行します。

```sh
git help commit
```

## Gitの3つのエリア

Gitでは変更内容（ファイル全体やファイルの一部）を次の3つの仮想的なエリアに移動させながら、変更を管理します。

- ワークツリー
- ステージ
- ローカルリポジトリ

ワークツリーはソースコードを変更する場所です。

ステージは、ワークツリーで行った変更（ファイル全体やファイルの一部）をリポジトリにコミットしたい場合に、その変更を登録する場所です。
ワークツリーにある変更をステージに移動する場合は、`add`コマンドを使用します。

ローカルリポジトリは、ステージにある変更を保存して、履歴管理する場所です。
ローカルリポジトリにある履歴は、履歴を改変するコマンドを実行しない限り、永遠に保存されます。
ステージにある変更をローカルリポジトリにコミットする場合は、`commit`コマンドを使用します。

<https://pengi-n.co.jp/blog/github-ja/>から借用した図

![Gitの3つのエリア](https://pengi-n.co.jp/blog/wp-content/uploads/2021/05/github1-1024x233.png)

Gitコマンドを実行する場合、コマンドがこれら3つのエリアにどのような影響を与えるかを理解する必要があります。

- `checkout`、`restore`: ワークツリーで行った変更を最後にコミットされた時点に戻す
- `add`: ワークツリーの変更をステージに移動
- `restore --cashed`: ステージに移動した変更を、ワークツリーに移動（ステージングされていない状態にする）
- `commit`: ステージにある変更をローカルリポジトリに保存する
- `reset`: 指定したコミットまでリポジトリの状態を戻し、指定したコミット以降の変更をステージに移動（ワークツリーの変更はそのまま）
- `reset --mixed`: 指定したコミットまでリポジトリの状態を戻し、指定したコミット以降の変更をワークツリーに移動
- `reset --hard`: 指定したコミットまでリポジトリの状態を戻し、ステージされた変更やワークツリーの変更を取り消す

## ファイルのライフサイクル

リポジトリ内のファイルは次のライフサイクルを持ちます。

- `Untracked（追跡されていない）`: リポジトリに追跡されていないファイル
- `Unmodified（変更されていない）`: リポジトリに追跡されているが、変更されていないファイル
- `Modified（変更されている）`: リポジトリに追跡されており、変更されたファイル
- `Staged（ステージされている）`: リポジトリに追跡されており、変更されたファイルがステージされたファイル

![ファイルのファイフサイクル](https://git-scm.com/book/en/v2/images/lifecycle.png)

## ローカルリポジトリの作成

### 新規にローカルリポジトリを作成する場合

新規にローカルリポジトリを作成する場合、次の手順で作成します。

1. ローカルリポジトリとなるディレクトリを作成します。
2. 上記で作成したディレクトリをカレントディレクトリに変更します。
3. `git init`コマンドを実行します。

```sh
# ディレクトリを作成
mkdir my-repo
# 上記で作成したディレクトリをカレントディレクトリに変更
cd my-repo
# ローカルリポジトリを作成
git init
```

ローカルリポジトリを作成すると、`.git`ディレクトリが作成されます。
このディレクトリには、リポジトリの設定やコミット履歴などが保存されます。

### 既存のリモートリポジトリをローカルリポジトリに複製（クローン）する場合

既存のリモートリポジトリをローカルリポジトリに複製（クローン）する場合、次の手順で複製します。

1. リモートリポジトリのURIを確認します。
2. ローカルリポジトリとなるディレクトリを作成します。
3. 上記で作成したディレクトリをカレントディレクトリに変更します。
4. `git clone`コマンドを実行します。

```sh
# GitHubに作成したリモートリポジトリのURIは次のような形式になっています。
# ssh: git@github.com:xjr1300/python-project-preparation.git
# https: https://github.com/xjr1300/python-project-preparation.git
#
# ディレクトリを作成
mkdir my-repo
# 上記で作成したディレクトリをカレントディレクトリに変更
cd my-repo
# カレントディレクトリ（下記コマンドの末尾の`.`に注意）にリモートリポジトリを複製
git clone it@github.com:xjr1300/python-project-preparation.git .
```

リモートリポジトリをクローンすると、その時点でリモートリポジトリにあるすべてのデータの完全なコピーがローカルリポジトリに複製されます。

## 追跡対象外設定

プロジェクトには、秘匿情報を記録したファイルや一時ファイなど、リポジトリで履歴管理するべきではないファイルやディレクトリが存在する場合があります。
リポジトリに含めたくない（追跡対象外）ファイルやディレクトリは、リポジトリのルートディレクトリに`.gitignore`ファイルを作成し、その中に記述します。

`.gitignore`ファイルには、次のような記述ができます。

- ワイルドカード（`*`）を使用して、複数のファイルやディレクトリを指定できます。
- ディレクトリを指定する場合は、ディレクトリ名の末尾に`/`を付けます。

`python`でコードを記述する場合の`.gitignore`ファイルの例を次に示します。

```text
# Byte-compiled / optimized / DLL files
__pycache__/
*.py[cod]
*$py.class

# Unit test / coverage reports
.coverage
.coverage.*
.cache

# Environments
.env

# Virtual Environments
.venv/
venv/

# ruff
.ruff_cache/

# mypy
.mypy_cache/

# IDE or temporary files
.idea/
*.swp
*.tmp
```

## Gitの基本

フィボナッチ数を計算するPythonプロジェクト作成し、そのプロジェクトのリポジトリをGitで管理する方法を説明します。

### プロジェクトの作成

`poetry`を使用してプロジェクトを作成すると共に、`pyenv`でプロジェクトで使用するPythonのバージョンを固定します。
また、`.gitignore`ファイルを指定して、追跡対象外とするファイルやディレクトリを指定します。
最後に、`init`コマンドでローカルリポジトリを初期化（作成）します。

```sh
# プロジェクトの作成
poetry new fibonacci
# プロジェクトのディレクトリに移動
cd fibonacci
# プロジェクトで使用するPythonのバージョンを固定
pyenv local 3.12.0
# .gitignoreファイルを作成
# ファイルの内容は`追跡対象外設定`を参照
vi .gitignore
# リポジトリを初期化（作成)
git init
```

### リポジトリ作成後の変更状態の確認

リポジトリを作成した後の変更状態を`status`コマンドで確認します。

```sh
% git status
On branch main

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        .gitignore
        .python-version
        README.md
        fibonacci/
        pyproject.toml
        tests/

nothing added to commit but untracked files present (use "git add" to track)
```

`status`コマンドの出力結果は次の通りです。

- `On branch main`: 現在のブランチが`main`ブランチであることを示しています。
- `No commits yet`: まだコミットがないことを示しています。
- `Untracked files`: 追跡されていないファイルがあることを示しています。
- `nothing added to commit but untracked files present`: コミットする変更がなく、追跡されていないファイルがあることを示しています。

### リポジトリの状態の確認と最初のコミット

プロジェクトを構築したため、変更をリポジトリにコミットします。

コミットするためには、変更されたファイルを`add`コマンドでステージに移動します。
そして、ステージした後のリポジトリの状態を`status`コマンドで確認します。

```sh
# `Untracked`または`Modified`なファイルをステージに移動します。
# `Untracked`なファイルは、コミットした後、`Unmodified`なファイルになります。
# `.`を指定することで、`Untracked`または`Modified`なファイルをすべてステージに移動します。
% git add .

# リポジトリの状態を確認します。
% git status
On branch main

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   .gitignore
        new file:   .python-version
        new file:   README.md
        new file:   fibonacci/__init__.py
        new file:   pyproject.toml
        new file:   tests/__init__.py
```

`status`コマンドの出力結果は次の通りです。

- `Changes to be committed`: コミットする変更があることを示しています。
- `use "git rm --cached <file>..." to unstage`: ステージに移動したファイルをワークツリーに移動する（ステージングを取り消す）方法を示しています。
- `new file`: ステージングされた新しいファイルであることを示しています。

ファイルが正しくステージングされたことを確認できたら、`commit`コマンドでコミットを作成します。

次の例ではコミットする3つの方法を示しています。

上2つの方法は、`commit`コマンド実行後、コミットメッセージを入力するテキストエディタが表示されるため、コミットメッセージを入力して保存及びエディタを終了します。
Gitの設定で、テキストエディタに`vim`を指定している場合、`vim`が起動されます。

`-v`オプションは、コミットメッセージを入力するテキストエディタに変更内容を表示するオプションです。
`-m`オプションは、コマンド内にコミットメッセージの簡潔な説明を入力するオプションで、テキストエディタが起動せずにコミットします。

```sh
# コミットする。
git commit
git commit -v
# コミットメッセージを入力してコミットする。
git commit -m "初期コミット"
```

`git commit -v`でコミットメッセージを入力した様子を次に示します。

![初期コミット](./images/initial-commit.png)

コミットすると次のようなメッセージが表示されます。

```text
[main (root-commit) 4763611] 初期コミット
 6 files changed, 42 insertions(+)
 create mode 100644 .gitignore
 create mode 100644 .python-version
 create mode 100644 README.md
 create mode 100644 fibonacci/__init__.py
 create mode 100644 pyproject.toml
 create mode 100644 tests/__init__.py
```

`main (root-commit) 4763611`の`4763611`は、そのコミットの**コミットID**の先頭からの一部を示しています。
実際のコミットIDは、`47636119b6c1e664db4daf6bdf3fe7d564e38d07`です。
例えば、そのコミットをした時の状態にリポジトリを戻す場合など、このコミットIDを使用しますが、GitはコミットIDの先頭の一部を指定するだけで、そのコミットを特定できます。

コミットした後、`status`コマンドでリポジトリの状態を確認して、`Untracked`または`Modified`なファイルがないことを確認します。

```sh
% git status
On branch main
nothing to commit, working tree clean
```

`status`コマンドの出力結果は次の通りです。

- `nothing to commit`: コミットする変更がないことを示しています。

#### コミットメッセージの入力

コミットメッセージは次のルールに従って入力します。

- コミットメッセージは、他の人が把握できるように、簡潔かつ明確に入力します。
- コミットメッセージの1行目は**件名**で、変更の簡潔な説明を**72文字まで**で入力します。
- コミットメッセージの2行目は、空白にします。
- コミットメッセージの3行目以降は**本文**で、変更の詳細な説明を入力します。
  テキストエディタに表示されたGitのコメントの改行位置を参考に適宜改行を挿入してください。

**件名**は、変更する元となった**要因**と、その**解決方法**を簡潔に記述してください。
件名を72文字までに収めることができなかった場合、**本文**に**要因**と**解決方法**を記述してください。

件名で要因と解決方法を表現できた場合、本文を入力する必要はありません。

コミットメッセージの例を次に示します。

- 本文あり

  ```text
  初期コミット

  poetryでプロジェクトを作成して、リポジトリを初期化した。
  その後、pyenvでプロジェクトで使用するPythonのバージョンを3.12.0に
  固定すると共に、.gitignoreファイルを作成して、Pythonで一般的に追跡
  対象外にするファイルやディレクトリを指定した。
  ```

- 本文なし

  ```te3xt
  初期コミット
  ```

### フィボナッチ関数の実装とコミット

`fibonacci/__init__.py`ファイルにフィボナッチ数を計算する関数を実装します。

```python
# fibonacci/__init__.py
def fibonacci(n: int) -> int:
    """フィボナッチ数を返す。

    Args:
        n (int): フィボナッチ数の項

    Returns:
        int: 引数で指定された項のフィボナッチ数

    Raises:
        ValueError: フィボナッチ数の項が0未満の場合
    """

    if n == 0:
        return 0
    elif n == 1:
        return 1
    elif 2 <= n:
        return fibonacci(n - 1) + fibonacci(n - 2)
    else:
        raise ValueError("fibonacci terms should be 0 or more numbers.")
```

リポジトリの状態を確認します。

```sh
% git status
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   fibonacci/__init__.py
```

`status`コマンドの出力結果は次の通りです。

- `Changes not staged for commit`: コミットするためにステージングされていない変更があることを示しています。
- `use "git add <file>..." to update what will be committed`: コミットするためにステージングされていない変更をステージングする方法を示しています。
- `use "git restore <file>..." to discard changes in working directory`: ワークツリーの変更を取り消す方法を示しています。
- `modified`: `Modified`なファイルを示しています。

変更をステージングして、コミットします。

```sh
# 変更をステージング
git add fibonacci/__init__.py
# ステージングされた変更をコミット
git commit -m "フィボナッチ数を計算する関数を実装"
```

### フィボナッチ関数の単体テストの実装とコミット

`fibonacci`関数の単体テストを`tests/test_fibonacci.py`ファイルに実装します。

```python
# tests/test_fibonacci.py
import unittest

from fibonacci import fibonacci


class TestFibonacci(unittest.TestCase):
    def test_fibonacci_number_are_correct(self) -> None:
        """フィボナッチ数を正しく計算できることを確認"""
        inputs = [
            (0, 0),
            (1, 1),
            (2, 1),
            (3, 2),
            (4, 3),
            (5, 5),
            (6, 8),
            (7, 13),
            (8, 21),
            (9, 34),
            (10, 55),
            (30, 832_040),
        ]
        for term, expected in inputs:
            actual = fibonacci(term)
            self.assertEqual(
                expected,
                actual,
                f"fibonacci number is {expected}, but {actual} was returned",
            )

    def test_fibonacci_raise_exception_when_teams_are_invalid(self) -> None:
        """フィボナッチ関数に0未満の項を指定した場合にValueError例外が発生することを確認"""
        for term in range(-2, 0):
            with self.assertRaises(ValueError):
                _ = fibonacci(term)
```

単体テストを実行して、パスすることを確認します。

```sh
% python -m unittest -vv
test_fibonacci_number_are_correct (tests.test_fibonacci.TestFibonacci.test_fibonacci_number_are_correct)
フィボナッチ数を正しく計算できることを確認 ... ok
test_fibonacci_raise_exception_when_teams_are_invalid (tests.test_fibonacci.TestFibonacci.test_fibonacci_raise_exception_when_teams_are_invalid)
フィボナッチ関数に0未満の項を指定した場合にValueError例外が発生することを確認 ... ok

----------------------------------------------------------------------
Ran 2 tests in 0.102s

OK
```

リポジトリに変更をコミットします。

```sh
git add tests/test_fibonacci.py
git commit -m "フィボナッチ数を計算する関数の単体テストを実装"
```

### フィボナッチ数をキャッシュして同じ項のフィボナッチ数を計算しないように改善

上記で実装した`fibonacci`関数は、同じ項のフィボナッチ数を何回も計算するため、項が大きくなると指数関数的に計算量が増加します。

よって、`functools.cache`デコレーターで`fibonacci`関数を修飾して、`fibonacci`関数に渡された引数とその戻り値をキャッシュすることで、計算量を低減します。

`fibonacci`関数を次の通り修正します。

```python
 # fibonacci/__init__.py
+import functools
+
+
+@functools.cache
 def fibonacci(n: int) -> int:
     """フィボナッチ数を返す。
```

単体テストを実行して、テストをパスするか確認します。

```sh
python -m unittest -vv
```

単体テストをパスすることを確認できたら、単体テストに項100のフィボナッチ数を追加して、再度、単体テストをパスするか確認します。

```python
 # tests/test_fibonacci.py
 class TestFibonacci(unittest.TestCase):
     def test_fibonacci_number_are_correct(self) -> None:
         """フィボナッチ数を正しく計算できることを確認"""
         inputs = [
             (0, 0),
             (1, 1),
             (2, 1),
             (3, 2),
             (4, 3),
             (5, 5),
             (6, 8),
             (7, 13),
             (8, 21),
             (9, 34),
             (10, 55),
             (30, 832_040),
+            (100, 354_224_848_179_261_915_075),
         ]
```

```sh
python -m unittest -vv
```

単体テストをパスすることを確認できたら、変更をリポジトリにコミットします。

```sh
git add .
git commit -m "fibonacci関数の結果をキャッシュして計算量を低減"
```

### 変更履歴の確認

最後に`log`コマンドを使用して、リポジトリの変更履歴を確認します。
`log`コマンドのオプションを指定しない場合、コミットのコミットID、著者、日時、コミットメッセージの件名と本文が表示されます。

`log`コマンドは、最新のコミットから表示されることに注意してください。

```sh
% git log
commit e04e98eecc0b6108a4fde4ea3f27519414bcc4f0 (HEAD -> main)
Author: xjr1300.04 <xjr1300.04@gmail.com>
Date:   Wed May 8 11:50:13 2024 +0900

    fibonacci関数の結果をキャッシュして計算量を低減

commit 018f495ff9e0667df5030649664b8faa4688c48d
Author: xjr1300.04 <xjr1300.04@gmail.com>
Date:   Wed May 8 11:35:20 2024 +0900

    フィボナッチ数を計算する関数の単体テストを実装

commit 2e78105af06f7034975cab672f8478b2d044b01e
Author: xjr1300.04 <xjr1300.04@gmail.com>
Date:   Wed May 8 11:02:21 2024 +0900

    フィボナッチ数を計算する関数を実装

commit 47636119b6c1e664db4daf6bdf3fe7d564e38d07
Author: xjr1300.04 <xjr1300.04@gmail.com>
Date:   Wed May 8 10:20:24 2024 +0900

    初期コミット

    poetryでプロジェクトを作成して、リポジトリを初期化した。
    その後、pyenvでプロジェクトで使用するPythonのバージョンを3.12.0に
    固定すると共に、.gitignoreファイルを作成して、Pythonで一般的に追跡
    対象外にするファイルやディレクトリを指定した。
```

`log`コマンドに`--oneline`オプションを指定すると、コミットIDの先頭の一部とコミットメッセージの件名が、1行で表示されます。

```sh
% git log --oneline
e04e98e (HEAD -> main) fibonacci関数の結果をキャッシュして計算量を低減
018f495 フィボナッチ数を計算する関数の単体テストを実装
2e78105 フィボナッチ数を計算する関数を実装
4763611 初期コミット
```

今回は、ブランチを作成しなかったため、コミットのツリー構造（枝分かれ）は直線的ですが、次を実行すると、コミットのツリー構造を表示できます。

```sh
% git log --oneline --decorate=full --graph
* e04e98e (HEAD -> refs/heads/main) fibonacci関数の結果をキャッシュして計算量を低減
* 018f495 フィボナッチ数を計算する関数の単体テストを実装
* 2e78105 フィボナッチ数を計算する関数を実装
* 4763611 初期コミット
```
