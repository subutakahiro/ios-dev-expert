# ios-dev-expert

iOSアプリ開発の設計・実装・テスト・コード品質管理を統括する、Claude Code向けSkillです。

## 概要

このSkillを使用することで、Claude Codeが以下のガイドラインに従ってiOSアプリケーションを開発します。

- **アーキテクチャ**: MVVM / 簡潔なObservableアーキテクチャ
- **UI**: SwiftUI（Liquid Glass対応、UIScene対応）
- **データベース**: SwiftData（VersionedSchema対応）
- **非同期処理**: Swift 6 Strict Concurrency / async・await
- **テスト**: Swift Testingフレームワーク
- **コード品質**: SwiftLint / SwiftFormat

## 動作確認環境

| ツール | バージョン |
|--------|-----------|
| Xcode  | 16.0 以上 |
| Swift  | 6.0 以上  |
| iOS    | 17.0 以上（推奨）|
| SwiftLint | 0.57.0 以上 |
| SwiftFormat | 0.54.0 以上 |

## ファイル構成

```
ios-dev-expert/
├── Skill.md                        # Skillエントリポイント
├── references/
│   ├── role_identity.md            # 役割定義・参照ドキュメント
│   ├── project_blueprint.md        # 技術スタック・ディレクトリ構成
│   ├── coding_standards.md         # MVVM / Observableアーキテクチャ詳細
│   ├── test_standards.md           # テストコード生成ルール
│   └── security_standards.md       # セキュリティ基準
└── scripts/
    └── .swiftlint.yml              # SwiftLint設定ファイル
```

## インストール方法

### 1. リポジトリをクローン

```bash
git clone https://github.com/<your-username>/ios-dev-expert.git
```

### 2. Claude Codeに登録

クローンしたディレクトリを Claude Code の Skill として登録します。

```bash
claude skill add ./ios-dev-expert
```

### 3. SwiftLint設定の配置

`scripts/.swiftlint.yml` をプロジェクトルートにコピーしてください。

```bash
cp ios-dev-expert/scripts/.swiftlint.yml <your-project-root>/.swiftlint.yml
```

## 主な規約

### アーキテクチャ

- View にビジネスロジックを書かず、ViewModel へ分離する
- ViewModel はプロトコル経由で Model を受け取る（Dependency Injection）
- ViewModel には原則 `@MainActor` を付与する

### 非同期処理

- Swift Concurrency（async/await, Task, Actor）を優先使用
- Completion Handler の使用を避ける
- Swift 6 Strict Concurrency Check に適合したコードを生成する

### テスト

- XCTest ではなく **Swift Testing** フレームワークを使用する
- 機能追加・削除時にテストコードを同時に更新する

### コード品質

- 全パブリックメソッドに DocC 形式（`///`）でコメントを記述する
- ファイル生成・編集後に `swiftformat` を実行する
- Cyclomatic Complexity: warning 10 / error 20

## ライセンス

MIT License
