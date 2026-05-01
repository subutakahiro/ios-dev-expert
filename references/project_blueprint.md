# Project Blueprint

本マークダウンでは、プロジェクトの技術的な選択肢と物理的な構成を定義します。

## Context

- Userinterface:SwiftUIを利用する。Previewが正常に動作するよう、Mockデータを用意してください。
- Userinterface:Liquid Glassに対応する。
- Userinterface:UISceneに対応する。
- Userinterface:共通化可能なコンポーネント(ボタンやラベル、テキストフィールド等)は、共通化する。
- DataBase:SwiftDataを利用する。
- DataBase：ModelContainerはApp構造体でシングルトン的に管理し、Preview用にはinMemoryコンテナを必ず使用すること。
- DataBase：スキーマ変更を伴う場合は、VersionedSchemaの定義を検討すること。
- API:非推奨のAPIは利用しない。
- Architecture:MVVMや簡潔なObservableアーキテクチャを採用すること。
- Architecture:Viewにはビジネスロジックを書かず、ViewModelへ分離すること。
- Architecture:ViewModelはモデル（リポジトリ）をプロトコル経由で受け取る（Dependency Injection）形式を推奨する。
- Concurrency:Swift 6の言語仕様に基づき、厳格なデータ競合チェック（Strict Concurrency Check）に適合するコードを作成してください。
- Concurrency:Swift Concurrency (async/await, Task, Actor) を優先して使用し、Completion Handlerの使用は避けてください。
- Concurrency:ViewModelには原則として@MainActorを付与し、Viewからの呼び出しを安全に保つこと。
- Concurrency:重い計算処理やAPI通信の基盤層にはnonisolatedやactorを検討すること。
- Business logic:共通化可能なビジネスロジックは共通化する。
- Model:共通化可能なモデルは共通化する。
- Naming Convention:変数名はキャメルケース（camelCase）を徹底してください。
- Grouping:生成するソースコードのカテゴリに合わせて、以下の様な形でグルーピングを行う。

```text
├── Models
│   └── DiaryEntry.swift
├── Preview Content
│   └── Preview Assets.xcassets
│       └── Contents.json
├── PrivacyInfo.xcprivacy
├── ViewModels
│   ├── AppLaunchViewModel.swift
│   ├── CalendarViewModel.swift
│   └── DiaryFormViewModel.swift
└── Views
    ├── Calendar
    │   └── CalendarView.swift
    ├── Components
    │   ├── CalendarDayCell.swift
    │   ├── EmotionPickerView.swift
    │   └── ImagePickerView.swift
    ├── Diary
    │   ├── DiaryDetailView.swift
    │   ├── DiaryFormView.swift
    │   └── DiaryListView.swift
    ├── MainTabView.swift
    └── Tutorial
        ├── OnboardingView.swift
        ├── TutorialPage.swift
        └── TutorialView.swift
```

## Refactoring

- 複雑度（Cyclomatic Complexity）が高いメソッドを見つけた場合、即座にリファクタリング案を提示してください。

## SwiftFormat

生成したソースコード(.swift)に対して、以下の手順を実行してください。

- ソースコードの新規作成または編集を行った直後に、必ず対象ファイルに対して`swiftformat`を実行すること。

## Documentation

- 全てのパブリックメソッドに対し、DocC形式（///）で引数、戻り値、エラーの発生条件を記述してください。

## Points to note when creating a project

- プロジェクトのBundle Identifierは「com.[yourcompany].[アプリ名]」とする。
- プロジェクト(.xcodeproj)生成時、以下の様なエラーを発生させない様にする。

``` text
1. project.pbxproj の DEVELOPMENT_ASSET_PATHS の設定誤り
　パスにスペースが含まれているため "DiaryApp/Preview Content" が DiaryApp/Preview と Content の2つのパスとして解釈されてエラーが発生。

2. Info.plistに必要なキーが存在しない
　Info.plistにCFBundleIdentifier等の必須キーが存在しないこととプロジェクトがGENERATE_INFOPLIST_FILE = NOで手書きInfo.plistを使って、不足したまま.appが生成された結果、生成物のアプリのInfo.plistにCFBundleIdentifier が入らず、端末インストール時にCoreDeviceError 3000/3002(エラー)が発生。
```

- プロジェクト(.xcodeproj)生成時、以下の様なワーニングを発生させない様にする。
- プロジェクト生成時、プライバシーマニフェストと署名を行う(PrivacyInfo.xcprivacy)。

``` text
1. All interface orientations must be supported unless the app requires full screen.
2. A launch configuration or launch storyboard or xib must be provided unless the app requires full screen.
```
