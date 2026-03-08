# Antigravity Shared Environment

このリポジトリは、Antigravity（AI エージェント）を活用した開発プロジェクト間で共通して使用する設定、ワークフロー、ルールをまとめたものです。

各プロジェクトの `env/` などのディレクトリに submodule として配置し、AI に `/sync-env` を実行させることで、最適な形で各リポジトリに展開・反映することができます。

## ディレクトリ構成と役割

- **`.antigravityrule`**
  - Antigravity 起動時に自動で読み込まれるベースの挙動ルールです。
- **`.agent/`**
  - **`rules/`**: GitHub運用やAndroid等の特定スタックごとの詳細ルール定義
  - **`workflows/`**: `/save`, `/resume`, `/cleanup`, `/sync-env` などのスラッシュコマンドの定義
- **`.github/`**
  - GitHub ActionsのCI/CDワークフロー（Lint, Build, Releaseなど）、Issueテンプレート、Release Drafter設定
- **`.hooks/`**
  - ローカル開発用の Git フック (`pre-commit`, `pre-push`)
- **`scripts/`**
  - Git フックや GitHub ラベルの自動セットアップスクリプト等

## 使い方（新規プロジェクトの立ち上げ時）

1. 新規プロジェクトのルートでこのリポジトリを Submodule として追加します。
   ```bash
   git submodule add https://github.com/asabon/antigravity-shared-env.git env
   ```
2. AI エージェントに対して以下のように指示し、環境を同期させます。
   ```
   /sync-env または「env/.agent/workflows/sync-env.md を実行して」
   ```
3. AI がプロジェクトの状況を分析し、最適な形でルールや設定の取り込みを提案・実行します。