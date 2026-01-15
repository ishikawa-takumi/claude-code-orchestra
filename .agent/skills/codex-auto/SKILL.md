---
name: codex-auto
description: "[OPTIONAL] Automatically evaluate task complexity and delegate to Codex CLI when beneficial. Activates when detecting large-scale tasks (10+ files), complex refactoring requests, or deep codebase analysis needs. Also responds to explicit requests like \"Codexに任せて\", \"Codex使って\". Gracefully skips if Codex CLI is not installed."
---

# Codex Auto-Delegation Skill

## Purpose

タスクの複雑さを自動評価し、必要に応じて Codex CLI に委譲します。
Codex CLI がインストールされていない場合は、スキップして通常処理を続行します。

## Auto-Activation Triggers

以下を検出したときに自動発動：

| トリガー | 例 |
|---------|-----|
| 大規模リファクタリング | 「全ファイルの型ヒントを追加」 |
| コードベース全体の分析 | 「リポジトリの構造を理解して」 |
| 多数ファイルへの変更 | 「src/以下を全部修正」 |
| 明示的リクエスト | 「Codexに任せて」「Codex使って」 |

## Workflow

```
┌─────────────────────────────────────────────┐
│ 1. Codex CLI 存在確認                        │
│    command -v codex                          │
│    └─ 未インストール → スキップ（通常処理へ）  │
└─────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────┐
│ 2. タスク規模の評価                          │
│    - 影響ファイル数をカウント                 │
│    - リファクタリング範囲を分析               │
│    - 複雑度を判定                            │
└─────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────┐
│ 3. 委譲判断                                  │
│    ├─ 10+ファイル or 複雑 → Codex に委譲     │
│    └─ それ以外 → Claude で対応               │
└─────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────┐
│ 4. 実行 & 報告                               │
│    - Codex exec でタスク実行                 │
│    - 結果をユーザーに報告                     │
└─────────────────────────────────────────────┘
```

## Step 1: Check Codex Availability

```bash
if ! command -v codex &> /dev/null; then
    echo "CODEX_NOT_AVAILABLE"
    # Continue with Claude - don't fail
fi
```

**Codex が利用不可の場合**: このスキルは何もせず、Claude が通常通り処理を続行します。

## Step 2: Evaluate Task Complexity

タスクを分析して以下をチェック：

```bash
# 影響ファイル数のカウント例
file_count=$(find src/ -name "*.py" | wc -l)

# 10ファイル以上なら大規模と判断
if [ "$file_count" -ge 10 ]; then
    echo "LARGE_SCALE_TASK"
fi
```

### 委譲基準

| 指標 | Claude | Codex |
|------|--------|-------|
| 影響ファイル数 | 1-9 | 10+ |
| 変更の種類 | 局所的 | 横断的 |
| 分析の深さ | 特定機能 | 全体構造 |
| ユーザー指示 | - | 「Codex」言及 |

## Step 3: Execute Delegation

### Codex に委譲する場合

```bash
# 現在の状態を保存
git stash push -m "before-codex-delegation"

# Codex で実行（分析のみの場合）
codex exec --suggest "タスクの説明"

# Codex で実行（変更を加える場合）
codex exec --auto-edit "タスクの説明"

# 結果を確認
git diff
```

### 実行モードの選択

| タスク種別 | モード | フラグ |
|-----------|--------|--------|
| 分析・調査 | Suggest | `--suggest` |
| コード変更 | Auto-Edit | `--auto-edit` |
| 完全自動 | Full-Auto | `--full-auto` |

## Step 4: Report Results

### ユーザーへの報告形式

**Codex に委譲した場合:**
```
🤖 Codex に委譲しました

**理由**: [影響ファイル数: 15, 横断的リファクタリング]
**実行モード**: auto-edit
**結果**: [変更の概要]

変更されたファイル:
- src/module_a.py: 型ヒント追加
- src/module_b.py: 型ヒント追加
...
```

**Claude で対応した場合:**
```
✅ Claude で対応します（小規模タスクのため）
```

**Codex 未インストールの場合:**
```
ℹ️ Codex CLI が未インストールのため、Claude で対応します
```

## Language Settings

- **思考・推論**: 英語
- **Codexへの指示**: 英語
- **ユーザー報告**: 日本語

## Error Handling

| エラー | 対応 |
|--------|------|
| Codex 未インストール | スキップ、Claude で続行 |
| 認証エラー | 警告を表示、Claude で続行 |
| 実行エラー | ロールバック、エラー報告 |

## Notes

- このスキルは**オプション**です。Codex がなくても全機能が動作します
- 大規模タスクでも、ユーザーが希望すれば Claude で対応可能
- `git stash` で変更前の状態を保存するため、ロールバック可能
