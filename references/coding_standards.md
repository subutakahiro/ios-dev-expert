# Coding Standards

## MVVM Architecture details

iOSアプリ開発におけるMVVM（Model-View-ViewModel）は、役割を明確に分けることで「View(ソースコード)の肥大化」を防ぎ、テスト効率と保守性を高めるアーキテクチャです。以下に責務分離の詳細をまとめます。

### Model

「何（データ）」と「どう扱うか（ビジネスロジック）」を定義します。

- **責務**:
    - データの構造定義（Entity / Struct）。
    - データの永続化（CoreData, Realm, UserDefaults）。
    - ネットワーク通信の実行（APIクライアント）。
    - UIに依存しない純粋な計算やロジック。
- 注意点: ViewやViewModelの存在を一切知りません。

### View

「どう見せるか」と「ユーザー入力の受付」に専念します。

- 責務:
    - UIコンポーネントの配置とレイアウト。
    - ユーザーのアクション（タップ、スワイプ等）をViewModelに伝える。
    - ViewModelの状態（State）を監視し、変化に合わせて表示を更新する。
- iOSにおける対象:
    - SwiftUI: `View` 構造体。
    - UIKit: `UIViewController` および `UIView`。
- 注意点: ビジネスロジックを記述してはいけません。

### ViewModel

ViewとModelの仲介役であり、「Viewの状態」を管理します。

- 責務:
    - Viewが表示するためのデータを保持する（表示用に加工された文字列など）。
    - Viewからのイベントを受け取り、Modelの処理を呼び出す。
    - Modelから取得した生データを、Viewが使いやすい形に変換する。
- 注意点: **`import UIKit` を行わない**のが理想です（UIコンポーネントを直接操作しない）。これにより、UIがなくてもロジックのテストが可能になります。

### iOSにおけるデータバインディングの実現

MVVMを成立させるには、ViewModelの変更をViewに伝える**「データバインディング」**が必要です。

- SwiftUIの場合:
    - `@Published` を使ってプロパティの変更を通知し、View側で `@StateObject` や `@ObservedObject` で受け取ります。
- UIKitの場合:
    - Combine: Swift標準のリアクティブフレームワークを使用。
    - Closure: 変化時に実行するクロージャを保持しておく手法。
    - Delegat: 伝統的なデリゲートパターンによる通知。

## Observable Architecture details

このアーキテクチャは、「データ（Model）の変更を検知し、UI（View）を自動的に再描画する」一貫したフローを作ることです。

### 世代による違い

iOSの進化に伴い、その実装方法は大きく2つの世代に分かれます。

* **旧方式（iOS 13〜16）: `ObservableObject`**
    * `Combine`フレームワークに依存。
    * `@Published`プロパティが1つでも変わると、そのオブジェクトを参照しているView全体が更新対象になる（粗い粒度）。
* **新方式（iOS 17以降）: `Observation`フレームワーク**
    * Swift 5.9の`@Observable`マクロを使用。
    * **プロパティ単位**で変更を追跡し、実際にその値を使っているViewのみをピンポイントで更新（細かい粒度）。

### 主要な構成要素

このアーキテクチャは、以下の3つの役割で構成されます。

| 要素 | 役割 | 実装のポイント |
| :--- | :--- | :--- |
| **Model** | 状態（データ）の保持 | `@Observable` クラスとして定義する。 |
| **Store / State** | インスタンスの管理 | `@State` や `@Bindable` を用いてViewと接続する。 |
| **View (Observer)** | データの表示 | データを参照するだけで、自動的に「観察者」となる。 |

### 実装例 (iOS 17+)

`Observation`フレームワークを用いた、最新の基本的な実装パターンです。

```swift
import SwiftUI
import Observation // iOS 17以降で必須

// 1. データモデルの定義（Observable）
@Observable
class UserProfile {
    var name: String = "Guest"
    var score: Int = 0
}

// 2. ビューでの利用（Observer）
struct ScoreView: View {
    // インスタンスの保持
    @State private var profile = UserProfile()

    var body: some View {
        VStack(spacing: 20) {
            Text("User: \(profile.name)")
                .font(.headline)
            
            Text("Score: \(profile.score)")
                .font(.largeTitle)

            Button("Increment Score") {
                // データを変更するだけで、Text(profile.score)が自動更新される
                profile.score += 1
            }
        }
        .padding()
    }
}
```
