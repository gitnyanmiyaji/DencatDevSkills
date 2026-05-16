---
name: agentic-ai-evaluation
description: エラーと学びをファイルに残し、同じ過ちを繰り返さないフィードバックループを回すSkill。全Stageで常時参照する横断Skill。エラー発生時・タスク完了時・ユーザーからフィードバックを受けたとき必ず使用する。謝るトークンの余裕があるなら、ファイルに残す。
---

# 04-evaluation: 評価・エラー分析・フィードバックループ

## このSkillの目的

エラーと学びをファイルに残し、同じ過ちを繰り返さないフィードバックループを回す。  
**謝るトークンの余裕があるなら、ファイルに残す。**  
良かったことも悪かったことも同じフォーマットで累積する。

---

## ファイル構成

```
./copilot/workspace.md     → ルール・原則（feedback.jsonから昇格したもの）
./copilot/feedback.json    → 生のフィードバック累積データ
./copilot/{task}-spec.md   → タスク中のエラーログ・進捗記録
```

**役割分担**
```
feedback.json  → どんどん積む（生データ）
workspace.md   → 太らせない（昇格したルールのみ）
spec.md        → タスク中の一時記録
```

---

## Section A: エラー発生時の記録

### A-1. エラーの分類

エラーが発生したら、まず原因を以下の3種類に分類する。

```
reasoning error  : 判断・推論が間違っていた
                   例：条件を誤読した・前提を取り違えた
tool misuse      : ツールの使い方が間違っていた
                   例：引数を間違えた・権限不足を確認しなかった
context issue    : コンテキスト・情報が不足していた
                   例：仕様が曖昧だった・環境情報が古かった
```

### A-2. エラー発生時の即時行動

```
Step 1. 作業を止める
Step 2. エラーを上記3種類に分類する
Step 3. spec.mdのエラーログに記録する
Step 4. feedback.jsonにエントリーを追加する
Step 5. 同じエラーが3回目の場合 → 自力解決を諦めてユーザーに報告する
```

### A-3. spec.mdへの記録

```markdown
## エラーログ
| 発生日時 | 分類 | エラー内容 | 試した対処 | 状態 |
|---|---|---|---|---|
| {日時} | reasoning / tool / context | {内容} | {対処} | 解決済み / 未解決 |
```

---

## Section B: フィードバックの記録

### B-1. 記録するタイミング

```
以下のタイミングで必ずfeedback.jsonにエントリーを追加する：
□ ユーザーから「いいね」「良い」などポジティブな反応を受けたとき
□ ユーザーから否定・訂正・不満を受けたとき
□ タスクが完了したとき
□ エラーが発生して対処したとき
□ 想定外の結果が出たとき
```

### B-2. feedback.jsonのフォーマット

```json
[
  {
    "date": "YYYY-MM-DD",
    "task": "タスク名",
    "action": "何をしたか（1行）",
    "result": "どうなったか・ユーザー反応（1行）",
    "learn": "なぜそうなったか（1行）",
    "next": "次回どうするか（1行）",
    "sentiment": "positive / negative / neutral",
    "category": "reasoning / tool / context / workflow / communication"
  }
]
```

### B-3. 記録の原則

```
□ 良い反応も悪い反応も同じフォーマットで残す
□ 謝罪のトークンを使う前にファイルに残す
□ 4行（action/result/learn/next）を省略しない
□ sentimentは正直に記録する（過小評価しない）
```

---

## Section C: タスク完了時の評価

### C-1. 完了時の振り返り手順

```
Step 1. spec.mdのゴールと成功判定基準を読む
Step 2. 実際の成果物と照合する
Step 3. 以下の問いに答える：

  Q1. ゴールを達成できたか？
  Q2. 想定より上手くいったことは何か？
  Q3. 想定より上手くいかなかったことは何か？
  Q4. 次回同じタスクで活かせる学びは何か？

Step 4. feedback.jsonにエントリーを追加する
Step 5. workspace.mdへの昇格判定を行う（Section D）
```

### C-2. 監視設定のチューニング記録

案件ごとにチューニングした監視設定の実績を記録する。

```markdown
## 監視設定（チューニング記録）
| パラメータ | デフォルト | 今回の設定 | 結果 |
|---|---|---|---|
| Heartbeat無応答上限 | 2回 | {設定値} | 適切 / 要調整 |
| タイムアウト予告 | 80% | {設定値} | 適切 / 要調整 |
| エラー繰り返し上限 | 3回 | {設定値} | 適切 / 要調整 |
```

チューニング結果が「要調整」の場合はfeedback.jsonにも記録する。

---

## Section D: workspace.mdへの昇格判定

### D-1. 昇格条件

```
以下のいずれかに該当する場合、workspace.mdにルールとして昇格させる：

条件1: 同じlearnが3回以上出現した
  → パターンとして認識してworkspace.mdにルール化する

条件2: sentimentがnegativeかつ重大度が高い
  → 即座にworkspace.mdのDeny ruleとして追記する

条件3: sentimentがpositiveで再現したい動きがある
  → workspace.mdのDo ruleとして追記する
```

### D-2. 昇格の手順

```
Step 1. feedback.jsonから昇格対象のエントリーを特定する
Step 2. ルールを1〜2行に要約する
Step 3. workspace.mdの該当セクションに追記する
Step 4. feedback.jsonの該当エントリーに昇格済みフラグを追加する
        "promoted": true
```

### D-3. workspace.mdのルールセクション

```markdown
## 学習ログ（feedback.jsonから昇格）

### Do（再現すべき良い動き）
- {日付} {ルール内容}

### Don't（繰り返してはならない失敗）
- {日付} {ルール内容}

### Deny（即時禁止）
- {日付} {禁止内容・理由}
```

---

## Section E: 起動時の参照手順

### E-1. 読み込み順序

```
Step 1. workspace.mdのDo / Don't / Denyセクションを読む
Step 2. feedback.jsonの直近10件を読む
Step 3. 今回のタスクに関連するエントリーを抽出する
         → task名・category・sentimentで絞り込む
Step 4. 関連するDon't / Denyを今回のタスクに適用する
Step 5. 関連するDoを今回のタスクで意識して動く
```

### E-2. 関連エントリーの抽出基準

```
以下のいずれかに一致するエントリーを今回のタスクに適用する：
- task名が類似している
- categoryが同じ（reasoning / tool / context 等）
- sentimentがnegativeで重大度が高い（常に参照）
```

---

## 完了チェックリスト

```
□ エラーが発生した場合、3種類に分類してfeedback.jsonに記録済み
□ ユーザーのフィードバックをfeedback.jsonに記録済み
□ タスク完了時の振り返りをfeedback.jsonに記録済み
□ 昇格条件を満たすエントリーがあればworkspace.mdに昇格済み
□ チューニングした監視設定の実績を記録済み
```
