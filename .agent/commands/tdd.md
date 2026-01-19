$ARGUMENTS をテスト駆動開発（TDD）で実装してください．

## TDD サイクル

```
Red → Green → Refactor の繰り返し

1. Red:    失敗するテストを書く
2. Green:  テストを通す最小限のコードを書く
3. Refactor: コードを整理（テストは通ったまま）
```

## 実装手順

### Phase 1: テスト設計

1. **要件を確認**
   - 入力は何か
   - 出力は何か
   - エッジケースは何か

2. **テストケースをリストアップ**
   ```
   - [ ] 正常系: 基本的な動作
   - [ ] 正常系: 境界値
   - [ ] 異常系: 無効な入力
   - [ ] 異常系: エラーハンドリング
   ```

### Phase 2: Red-Green-Refactor

#### Step 1: 最初のテストを書く（Red）

```python
# tests/test_{module}.py
def test_{function}_basic():
    """最も基本的なケースをテスト"""
    result = function(input)
    assert result == expected
```

テストを実行して **失敗を確認**:
```bash
uv run pytest tests/test_{module}.py -v
```

#### Step 2: 実装（Green）

テストを通す **最小限** のコードを書く:
- 完璧を目指さない
- ハードコードでも OK
- とにかくテストを通す

テストを実行して **成功を確認**:
```bash
uv run pytest tests/test_{module}.py -v
```

#### Step 3: リファクタリング（Refactor）

テストが通ったまま改善:
- 重複を排除
- 命名を改善
- 構造を整理

```bash
uv run pytest tests/test_{module}.py -v  # まだ通ることを確認
```

#### Step 4: 次のテストへ

リストの次のテストケースで Step 1 に戻る．

### Phase 3: 完了確認

```bash
# 全テスト実行
uv run pytest -v

# カバレッジ確認（80%以上が目標）
uv run pytest --cov={module} --cov-report=term-missing
```

## レポート形式

```markdown
## TDD 実装完了: {機能名}

### テストケース
- [x] {テスト1}: {説明}
- [x] {テスト2}: {説明}
...

### カバレッジ
{カバレッジレポート}

### 実装ファイル
- `src/{module}.py`: {説明}
- `tests/test_{module}.py`: {テスト数}件
```

## 注意事項

- テストは **先に** 書く（後から書かない）
- 1サイクルは **小さく** 保つ
- リファクタリングは **テストが通ってから**
- 完璧より **動くコード** を優先
