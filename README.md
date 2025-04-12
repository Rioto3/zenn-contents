# Zenn Git ワークフロー

このリポジトリは[Zenn](https://zenn.dev)との連携用リポジトリです。効率的なコンテンツ作成と管理のためのGitワークフローを定義しています。

## ブランチ戦略

シンプルかつ効率的なブランチ戦略を採用しています：

```
main (デフォルトブランチ・安定版・Zenn連携)
└── feature/article-name (記事作成ブランチ)
```

## コンテンツガイドライン 

- 記事ファイル名は日付またはスラッグを含む命名規則に従う
  - 例: `20250408-git-workflow.md`
- 画像ファイル名は記事名と関連付ける
  - 例: `git-workflow-diagram.png`


## 参考リンク

- [Zenn CLIの使い方](https://zenn.dev/zenn/articles/zenn-cli-guide)
- [Zennのマークダウン記法](https://zenn.dev/zenn/articles/markdown-guide)
