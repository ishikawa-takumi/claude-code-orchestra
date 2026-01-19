# セキュリティルール

コードを書く際に常に確認すべきセキュリティチェックリスト．

## 機密情報の管理

### 絶対禁止

- APIキー・パスワードのハードコード
- 機密情報のログ出力
- `.env` ファイルのコミット

### 必須

```python
# ✅ 環境変数から取得
import os
API_KEY = os.environ["API_KEY"]

# ✅ 存在チェック付き
API_KEY = os.environ.get("API_KEY")
if not API_KEY:
    raise ValueError("API_KEY environment variable is required")
```

## 入力バリデーション

外部からの入力は常に検証:

```python
from pydantic import BaseModel, EmailStr, Field

class UserInput(BaseModel):
    email: EmailStr
    age: int = Field(ge=0, le=150)
    name: str = Field(min_length=1, max_length=100)
```

## SQL インジェクション防止

```python
# ❌ Bad: 文字列結合
cursor.execute(f"SELECT * FROM users WHERE id = {user_id}")

# ✅ Good: パラメータ化クエリ
cursor.execute("SELECT * FROM users WHERE id = ?", (user_id,))
```

## XSS 防止

- ユーザー入力をHTMLに埋め込む前にエスケープ
- テンプレートエンジンの自動エスケープを有効に

## エラーメッセージ

```python
# ❌ Bad: 詳細すぎる（攻撃者に情報を与える）
raise Exception(f"Database connection failed: {connection_string}")

# ✅ Good: 必要最小限
raise Exception("Database connection failed")
# 詳細はログに（ログは非公開）
logger.error(f"Database connection failed: {connection_string}")
```

## 依存関係

- 定期的に脆弱性チェック: `pip-audit`, `safety`
- 不要な依存は削除
- バージョンを固定（`>=` より `==`）

## チェックリスト

コードレビュー時に確認:

- [ ] ハードコードされた機密情報がない
- [ ] 外部入力がバリデーションされている
- [ ] SQLクエリがパラメータ化されている
- [ ] エラーメッセージが詳細すぎない
- [ ] ログに機密情報が含まれていない
