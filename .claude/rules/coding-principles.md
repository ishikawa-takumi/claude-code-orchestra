# コーディング原則

常に従うべきコーディングルール．

## シンプルさを最優先

- 複雑なコードより読みやすいコードを選ぶ
- 過度な抽象化を避ける
- 「動く」より「理解できる」を優先

## 単一責任

- 1つの関数は1つのことだけを行う
- 1つのクラスは1つの責任だけを持つ
- ファイルは200-400行を目安（最大800行）

## 早期リターン

```python
# ❌ Bad: ネストが深い
def process(value):
    if value is not None:
        if value > 0:
            return do_something(value)
    return None

# ✅ Good: 早期リターン
def process(value):
    if value is None:
        return None
    if value <= 0:
        return None
    return do_something(value)
```

## 型ヒント必須

すべての関数に型アノテーションを付ける:

```python
def call_llm(
    prompt: str,
    model: str = "gpt-4",
    max_tokens: int = 1000
) -> str:
    ...
```

## イミュータビリティ

既存のオブジェクトを変更せず，新しいオブジェクトを生成:

```python
# ❌ Bad: 既存を変更
data["new_key"] = value

# ✅ Good: 新しいオブジェクトを生成
new_data = {**data, "new_key": value}
```

## 命名規則

- **変数・関数**: snake_case（英語）
- **クラス**: PascalCase（英語）
- **定数**: UPPER_SNAKE_CASE（英語）
- **意味のある名前**: `x` より `user_count`

## マジックナンバー禁止

```python
# ❌ Bad
if retry_count > 3:
    ...

# ✅ Good
MAX_RETRIES = 3
if retry_count > MAX_RETRIES:
    ...
```
