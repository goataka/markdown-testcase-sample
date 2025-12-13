# markdown-testcase-sample

テストケースをIssue、PRからMarkdownで作成する流れを検討するリポジトリです。

## 概要

このリポジトリは、IssueやPRの内容から体系的にテストケースを作成する方法を示すサンプルです。
業務目的や機能概要から、シナリオベース、因子分析、デシジョンテーブルを用いたテストケース作成プロセスを、
**勤怠管理システム**を例として一貫した形で説明しています。

## ドキュメント構成

```
docs/
├── README.md                    # 全体ガイド（プロセス説明）
├── samples/                     # サンプルIssue・PR
│   ├── issue-sample.md          # Issue例：打刻機能の追加
│   └── pr-sample.md             # PR例：打刻機能の実装
├── testcases/                   # テストケース作成
│   ├── scenario-overview.md     # シナリオ抽出と整理
│   ├── factor-analysis.md       # 因子分析（設定・データ・環境）
│   ├── decision-table.md        # デシジョンテーブル
│   └── procedures/              # テスト手順（Gherkin形式・日本語）
│       ├── TC001-打刻登録.md
│       ├── TC002-打刻修正.md
│       └── TC003-月次集計.md
└── results/                     # テスト結果記録
    ├── 2024-01-15-TC001-結果.md
    └── 2024-01-16-TC003-結果.md
```

## 使い方

1. **[全体ガイド](docs/README.md)** でプロセス全体を理解
2. **[サンプルIssue](docs/samples/issue-sample.md)** で業務目的・機能概要を確認
3. **[サンプルPR](docs/samples/pr-sample.md)** で変更点を確認
4. **[シナリオ概要](docs/testcases/scenario-overview.md)** でテストシナリオの抽出方法を学ぶ
5. **[因子分析](docs/testcases/factor-analysis.md)** で影響範囲と因子の特定方法を学ぶ
6. **[デシジョンテーブル](docs/testcases/decision-table.md)** でテストパターンの組み合わせを確認
7. **[テスト手順](docs/testcases/procedures/)** でGherkin形式の手順を確認
8. **[テスト結果](docs/results/)** で結果記録の形式を確認

## 特徴

- **シンプル**: 複雑さを避け、理解しやすい構成
- **一貫性**: 勤怠管理システムの例で一貫した説明
- **実践的**: 実際のプロジェクトで使える形式
- **追跡可能**: IssueからPR、テストケース、結果まで追跡可能
- **Gherkin形式**: テスト手順は日本語のGherkin形式で記述

## ライセンス

MIT License
