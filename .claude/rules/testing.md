# テストルール

テストを書く際のガイドライン．

## 基本方針

- **TDD 推奨**: テストを先に書く
- **カバレッジ目標**: 80%以上
- **実行速度**: 単体テストは高速に（1テスト < 100ms）

## テスト構造

### AAA パターン

```python
def test_user_creation():
    # Arrange（準備）
    user_data = {"name": "Alice", "email": "alice@example.com"}

    # Act（実行）
    user = create_user(user_data)

    # Assert（検証）
    assert user.name == "Alice"
    assert user.email == "alice@example.com"
```

### 命名規則

```python
# test_{対象}_{条件}_{期待結果}
def test_create_user_with_valid_data_returns_user():
    ...

def test_create_user_with_invalid_email_raises_error():
    ...
```

## テストケースの網羅

各機能に対して以下を考慮:

1. **正常系**: 基本的な動作
2. **境界値**: 最小値，最大値，空
3. **異常系**: 無効な入力，エラー条件
4. **エッジケース**: None，空文字，特殊文字

## モックの使用

外部依存はモック化:

```python
from unittest.mock import Mock, patch

@patch("module.external_api_call")
def test_with_mocked_api(mock_api):
    mock_api.return_value = {"status": "ok"}
    result = function_under_test()
    assert result == expected
```

## フィクスチャ

共通のセットアップは `conftest.py` に:

```python
# tests/conftest.py
import pytest

@pytest.fixture
def sample_user():
    return User(name="Test", email="test@example.com")

@pytest.fixture
def db_session():
    session = create_session()
    yield session
    session.rollback()
```

## 実行コマンド

```bash
# 全テスト
uv run pytest -v

# 特定ファイル
uv run pytest tests/test_user.py -v

# 特定テスト
uv run pytest tests/test_user.py::test_create_user -v

# カバレッジ付き
uv run pytest --cov=src --cov-report=term-missing

# 失敗時に即停止
uv run pytest -x
```

## チェックリスト

- [ ] 正常系がテストされている
- [ ] 異常系がテストされている
- [ ] 境界値がテストされている
- [ ] テストが独立している（順序依存なし）
- [ ] 外部依存がモック化されている
- [ ] テストが高速に実行される
