## Python 標準

この組織の Python プロジェクトに適用する標準を定める。

### 言語バージョン

- Python >= 3.13

### プロジェクト管理ツール

- [uv](https://docs.astral.sh/uv/) を使用する（Python バージョン管理、仮想環境管理、パッケージ管理、プロジェクト管理を包括するツール）

### ビルドバックエンド

- [hatchling](https://hatch.pypa.io/latest/) を使用する

### フォーマッター / リンター

- [Ruff](https://docs.astral.sh/ruff/) を使用する（フォーマットとリントの両方を担う）
- 行長は 100 文字に設定する

### 型チェック

- [mypy](https://mypy.readthedocs.io/) を厳格モード（`--strict`）で使用する
- 型アノテーションを省略しない
- 型チェックの除外（`type: ignore`）は原則禁止とし、やむを得ない場合は理由をコメントで残す

### pre-commit

- [pre-commit](https://pre-commit.com/) を必須とする
- フック内で Ruff のフォーマット・リントおよび mypy の型チェックを実行する

### テスト

- [pytest](https://docs.pytest.org/) を使用する
- テスト関連の依存は開発依存として管理し、本番依存に含めない

### 開発依存の管理

- `[dependency-groups]` 形式（[PEP 735](https://peps.python.org/pep-0735/)）で定義する
- `[project.optional-dependencies]` は使用しない
