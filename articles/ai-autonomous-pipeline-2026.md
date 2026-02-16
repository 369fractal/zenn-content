---
title: "AIエージェントで完全自律コンテンツパイプラインを構築した話"
emoji: "🤖"
type: "tech"
topics: ["ai", "automation", "claude", "openai"]
published: false
---

## はじめに

2026年、AIエージェントは単なるチャットボットから「自律的に仕事をこなすワーカー」へと進化しました。本記事では、コンテンツの企画→執筆→品質チェック→公開までを完全自動化したパイプラインの設計と実装を紹介します。

## アーキテクチャ概要

```
トレンド調査 (2h間隔)
    ↓
記事生成 (6h間隔)
    ↓
品質チェック (4h間隔)
    ↓
自動公開 (2h間隔)
    ↓
KPI分析 (日次)
```

## 技術スタック

| コンポーネント | 技術 | 役割 |
|---------------|------|------|
| オーケストレーター | OpenClaw | cronジョブ管理・セッション制御 |
| LLM (高品質) | Claude Sonnet | 記事生成 |
| LLM (高速) | Claude Haiku | 調査・QA・分析 |
| 画像生成 | fal.ai (FLUX.1) | サムネイル・図解 |
| 検索 | Brave Search API | トレンド調査 |
| デプロイ | GitHub + Zenn | 自動公開 |

## 1. トレンド調査フェーズ

Brave Search APIを使って、AI・自動化分野のトレンドキーワードを収集します。

```javascript
// Brave Search でトレンド取得
const results = await braveSearch({
  q: "AI agent automation 2026",
  search_lang: "jp",
  count: 10
});
```

ポイントは `search_lang: "jp"` です（`ja`ではなく`jp`がBrave APIの正しいパラメータ）。

## 2. 記事生成フェーズ

Claude Sonnetを使って、調査結果を基に記事を生成します。ここで重要なのは：

- **1回の実行で1記事のみ**生成（品質重視）
- **Zennフォーマット**に準拠したfrontmatter付きMarkdown
- **技術的な正確性**を担保するプロンプト設計

## 3. 品質チェックフェーズ

生成された記事をHaikuモデルでレビューします：

- 技術的正確性
- 文章の読みやすさ
- SEOキーワードの自然な配置
- コードサンプルの動作確認

## 4. 自動公開フェーズ

品質チェックをパスした記事を`git push`でZennに自動デプロイ。

```bash
cd ~/zenn-content
git add articles/
git commit -m "Add: 新規記事"
git push origin main
```

Zenn × GitHub連携により、mainブランチへのpushで自動的に記事が公開されます。

## コスト分析

| 項目 | 月額 |
|------|------|
| Claude API (Sonnet + Haiku) | ~$30-60 |
| Brave Search API | 無料枠内 |
| fal.ai | ~$5 |
| GitHub | 無料 |
| Zenn | 無料 |
| **合計** | **$35-65** |

## まとめ

完全自律パイプラインにより、人間の介入なしで技術記事を継続的に生成・公開できるようになりました。重要なのは：

1. **品質ゲート**を必ず設けること
2. **投稿頻度を制限**すること（1日1-2本まで）
3. **人間のレビュー**を定期的に入れること

AIは道具であり、最終的な品質と方向性の判断は人間が行うべきです。

---

*この記事はAI支援で執筆されています。*
