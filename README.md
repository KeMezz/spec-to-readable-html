# spec-to-readable-html

Markdown / テキストの仕様書を、**要約・構造再編・図解・トレーサビリティ付きの「読みやすい HTML ドキュメント」**に変換する [Agent Skill](https://github.com/vercel-labs/skills) です。

単なる Markdown → HTML 変換ではありません。内容を解析して要約・再構成し、フローチャート / シーケンス図 / ER 図やカード・表などの視覚補助を加え、元仕様へのトレーサビリティを保ったまま 1 枚のポータブルな HTML を生成します。CSS は埋め込み、Mermaid 図を使う場合は既定で CDN に依存します。完全な自己完結 HTML が必要な場合は inline SVG を使用してください。

## 特長

- **要約と再構成** — 長い仕様を読み手向けに整理（要件 / API / データ / リスク / 未決事項）
- **視覚化** — Mermaid 図（フロー・シーケンス・状態・ER）、サマリーカード、バッジ、表
- **トレーサビリティ** — 出力の各セクションを元仕様にマッピング（Preserved / Summarized / Inferred）
- **ポータブル HTML** — 埋め込み CSS、目次、図のズーム表示、印刷対応。Mermaid 使用時は CDN 依存あり（完全自己完結が必要なら inline SVG）
- **多言語** — 既定は日本語。`lang` 引数で切り替え

## インストール

[GitHub CLI](https://cli.github.com/)（v2.90.0+）を使う場合:

```bash
gh skill install KeMezz/spec-to-readable-html
```

[`npx skills`](https://github.com/vercel-labs/skills) を使う場合:

```bash
npx skills add KeMezz/spec-to-readable-html
```

または、`skills/spec-to-readable-html/` 配下の `SKILL.md` と `references/` を、エージェントのスキルディレクトリ（`~/.claude/skills/spec-to-readable-html/` など）に手動で配置してください。

## 使い方

Claude Code などで仕様書（Markdown / テキスト）を渡し、本スキルを呼び出します。

```
/spec-to-readable-html ja
```

`lang` を省略すると日本語で出力します。

## 出力テンプレートのカスタマイズ

`references/template.html` をベース HTML として使用します。ロゴと配色はプレースホルダ／中立色にしてあるので、自分のブランドに合わせて変更できます。

- **ロゴ** — `{{LOGO}}` に `<svg>` / `<img>` を差し込み（不要なら削除可）
- **配色** — `:root` の `--color-*` トークンを変更

## ライセンス

[MIT](./LICENSE) © 2026 KeMezz
