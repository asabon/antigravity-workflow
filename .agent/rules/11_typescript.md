# TypeScript 開発プロトコル

TypeScript 開発において、AI エージェントが自律的にプロジェクト構成（型定義、Lint ルール等）を理解し、適切な品質管理を行うための手順です。

## 1. プロジェクト構成の自律探索
作業を開始する前に、必ず以下のファイルを確認し、プロジェクトの構造を特定してください。
- **パッケージ管理**: `package.json`, `package-lock.json`, `yarn.lock`, `pnpm-lock.yaml`, `bun.lockb`
- **TypeScript 構成**: `tsconfig.json`, `tsconfig.*.json`
- **使用されているランタイム/フレームワーク**: Node.js (Express, Next.js等), Bun, Deno, Vite 等。

## 2. ツール設定の尊重
プロジェクト内に以下の設定ファイルが存在する場合、そこに記述されたルールを最優先してください。
- **Lint / Format**: `.eslintrc.*`, `eslint.config.js`, `.prettierrc`, `prettier.config.js`
- **型安全性**: `tsconfig.json` 内の `strict` モード設定やパスエイリアス設定。

## 3. 実装後の検証プロトコル
コーディング完了後、ユーザーに進捗を報告または Pull Request を作成する前に、プロジェクト固有の検証スクリプトを実行してください。

1. **スクリプトの特定**: `package.json` の `scripts` 項目を確認し、ビルド (`build`, `tsc`), テスト (`test`), Lint/Format (`lint`, `format`, `fix`) に相当するスクリプトを特定する。
2. **実行と修正**: 特定したスクリプト（例: `npm run lint`）を実行し、エラーや型エラーをすべて解消する。
3. **報告**: PR 本文の「確認事項 (Verification)」には、**実際に実行したプロジェクト固有のコマンド**とその結果を記載する。

---

## 補足
- **実行権限**: `npm`, `yarn`, `pnpm`, `bun` 等の実行コマンドの許可については `auto-commands.yaml` に従ってください。
