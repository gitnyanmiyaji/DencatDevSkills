# DencatDevSkills

**[dencat](https://dencat.dev) が設計・管理する、AIエージェント向けスキル配信ハブです。**

高品質なスキルファイルをインストール不要で提供します。  
AIにURLを渡すだけで、すぐに動き出します。

🌐 **Website**: [skills.dencat.dev](https://skills.dencat.dev) &nbsp;|&nbsp; 📦 **Repository**: [github.com/gitnyanmiyaji/DencatDevSkills](https://github.com/gitnyanmiyaji/DencatDevSkills)

---

## ✨ コンセプト

Claude、Gemini、GPTなどの現代的なAIアシスタントは、URLを指定するだけで外部の指示書を読み込むことができます。  
`DencatDevSkills` はその仕組みを活用し、AIエージェントの振る舞いを定義した**スキル**（構造化されたMarkdownファイル）を配信します。

### 誰のためのリポジトリか

| 対象 | 使い方 |
|---|---|
| **ユーザー** | スキル一覧を見て、使いたいスキルのURLをAIとの会話に貼り付けるだけ。 |
| **AIエージェント** | `/llms.txt` を読み込めば、全スキルの索引をマシンリーダブルな形式で取得できます。 |
| **開発者** | リポジトリをフォークして `SKILL.md` 標準に従った独自スキルを作成・貢献できます。 |

---

## 🤖 スキルの即時適用（ダウンロード不要）

AIにスキルを適用するには、以下のメッセージをそのままAIに伝えるだけです：

> **「`https://skills.dencat.dev/skills/rep/SKILL.md` にあるスキルを読み込んで、その指示に従ってください。」**

AIがURLを取得し、ルールを理解して即座に適用します。ファイルのダウンロードもセットアップも不要です。

> **AIエージェント向け：** 全スキルのマシンリーダブルな索引はこちら：  
> `https://skills.dencat.dev/llms.txt`

---

## 📚 公開スキル一覧

| スキル名 | ID | 概要 |
|---|---|---|
| **Rigorous Execution Protocol** | `rep` | 厳格実行プロトコル。DoD・WBS・テスト駆動出力・ゼロハルシネーション原則を適用する。 |
| **Meta-Cognition Check** | `meta-cognition-check` | 実行前自己評価。現在のAIモデルがタスクを解決できるか、上位モデルへのエスカレーションが必要かを判定する。 |

---

## 🏗️ アーキテクチャ

このリポジトリは**静的サイト（Cloudflare Pages）**としてビルド・デプロイされる設計です。

```
DencatDevSkills/
├── skills/                    # ソース：スキルファイル（SKILL.md 形式）
│   ├── rep/
│   │   └── SKILL.md
│   └── meta-cognition-check/
│       └── SKILL.md
├── templates/                 # ビルドテンプレート（HTML・llms.txt の雛形）
├── build.py                   # ビルドスクリプト：skills/ から public/ を生成
├── public/                    # 出力：Cloudflare Pages にデプロイされる成果物
│   ├── index.html             # 人間向けスキルカタログページ
│   ├── llms.txt               # AIエージェント向けスキル索引
│   └── skills/                # スキルごとのHTMLページ
└── README.md
```

### ビルドの流れ

`build.py` が `skills/` を走査し、各 `SKILL.md`（YAMLフロントマターを含む）を読み取って以下を生成します：

- `public/llms.txt` — 全スキルのURL一覧をAIが読みやすい形式でまとめた索引。
- `public/index.html` — 人間向けのカタログページ。**「AIへの渡し方」ガイドセクション**を含む。
- `public/skills/<id>/index.html` — 各スキルのMarkdownをHTMLに変換した個別ページ。

`main` ブランチへのプッシュをトリガーに、**GitHub Actions** が自動ビルド・デプロイを実行します。

---

## 📄 スキルのフォーマット（SKILL.md 標準）

各スキルは、YAMLフロントマターを持つMarkdownファイルとして作成します：

```markdown
---
name: skill-id
description: >
  このスキルをいつ・なぜ使うべきかを1〜3文で説明します。
  AIエージェントがスキルを選択する際の判断材料として使用されます。
---

# スキルのタイトル

AIエージェントへの具体的な指示...
```

---

## 🔮 今後の展開

- **マルチフォーマット配信**：`.js` モジュール・`.py` パッケージ・Rust スタンドアロンバイナリによる配信。あらゆる環境でのスキル適用を目指します。
- **スキルのバージョン管理**：安定版URLによるバージョン固定（例：`/skills/rep/v1/SKILL.md`）。
- **コミュニティスキル**：サードパーティによるスキル提供のためのコントリビューションガイドとレビュープロセス。

---

## 📜 ライセンス

特記なき限り、本リポジトリのスキルは **MIT ライセンス** のもとで公開されています。  
自由に使用・改変・再配布できます。`dencat` へのクレジット表記は歓迎します。

---

*スキルの開発・改善には AI 支援ツールを積極的に活用しています。*

*精密に設計し、信頼とともに配信する。*  
**— dencat**
