# コンテンツ発信パイプライン

## パイプライン概要
コンテンツは4つの公開状態で管理します：

| 状態 | Zenn | 個人サイト | 用途 |
|------|------|------------|------|
| 完全非公開 | `false` | `false` | 下書き・初期構想 |
| Zennのみ公開 | `true` | `false` | Zennコミュニティ向け |
| 個人サイトのみ公開 | `false` | `true` | 個人的/実験的コンテンツ |
| 両方に公開 | `true` | `true` | 広く共有したいコンテンツ |

## フロントマター例
```yaml
---
title: "コンテンツ発信パイプラインの構築"
emoji: "🚀"
type: "tech"
topics: ["zenn", "portfolio", "content-strategy"]
published: false       # Zenn公開状態
published-self: false  # 個人サイト公開状態
---
```

## パイプラインの目的
- コンテンツ作成の柔軟性向上
- 異なるプラットフォーム間の効率的な管理
- 執筆プロセスの最適化

## ワークフロー
1. コンテンツ作成（Zenn編集画面）
2. メタデータ（フロントマター）の設定
3. 公開状態の選択
4. 各プラットフォームへの展開

## ベストプラクティス
- 下書き段階では両方`false`
- 内容に応じて適切な公開範囲を選択
- クロスポスト時は内容の最適化を意識

## 今後の改善点
- 自動化スクリプトの検討
- クロスプラットフォーム連携の強化
- コンテンツ分析と最適化

## テクニカルスタック
- プラットフォーム: Zenn, Personal Portfolio
- 技術: Markdown, GitHub Actions（将来的）
- ツール: Astro, Notion

## 注意事項
- 各プラットフォームの利用規約を遵守
- コンテンツの品質と独自性を重視
- 継続的な学習と改善