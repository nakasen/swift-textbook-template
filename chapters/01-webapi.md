# 第1章：WebAPIの基本

> 執筆者：中川　聡
> 最終更新：2026-04-17

## この章で学ぶこと

Apple公式の iTunes Search API を利用してAPIアクセスからアルバムのタイトル名、関連画像をダウンロードしてList Viewに表示するアプリを通じて、XXXXとXXXXを理解する


## 模範コードの全体像

```swift
// ============================================
// 第1章（基本）：iTunes Search APIで音楽を検索するアプリ
// ============================================
// このアプリは、iTunes Search APIを使って
// 音楽（曲）を検索し、結果をリスト表示します。
// APIキーは不要で、すぐに動かすことができます。
// ============================================

import SwiftUI

// MARK: - データモデル

struct SearchResponse: Codable {
    let results: [Song]
}

struct Song: Codable, Identifiable {
    let trackId: Int
    let trackName: String
    let artistName: String
    let artworkUrl100: String
    let previewUrl: String?
    
    var id: Int { trackId }
}

// MARK: - メインビュー

struct ContentView: View {
    @State private var songs: [Song] = []
    @State private var searchText: String = ""
    @State private var isLoading: Bool = false

    var body: some View {
        NavigationStack {
            VStack {
                // 検索バー
                HStack {
                    TextField("アーティスト名を入力", text: $searchText)
                        .textFieldStyle(.roundedBorder)

                    Button("検索") {
                        Task {
                            await searchMusic()
                        }
                    }
                    .buttonStyle(.borderedProminent)
                    .disabled(searchText.isEmpty)
                }
                .padding(.horizontal)

                // 検索結果リスト
                if isLoading {
                    ProgressView("検索中...")
                        .padding()
                    Spacer()
                } else if songs.isEmpty {
                    ContentUnavailableView(
                        "曲を検索してみよう",
                        systemImage: "music.note",
                        description: Text("アーティスト名を入力して検索ボタンを押してください")
                    )
                } else {
                    List(songs) { song in
                        SongRow(song: song)
                    }
                }
            }
            .navigationTitle("Music Search")
        }
    }

    // MARK: - API通信

    func searchMusic() async {
        guard let encodedText = searchText.addingPercentEncoding(
            withAllowedCharacters: .urlQueryAllowed
        ) else { return }

        let urlString = "https://itunes.apple.com/search?term=\(encodedText)&media=music&country=jp&limit=25"

        guard let url = URL(string: urlString) else { return }

        isLoading = true

        do {
            let (data, _) = try await URLSession.shared.data(from: url)
            let response = try JSONDecoder().decode(SearchResponse.self, from: data)
            songs = response.results
        } catch {
            print("エラー: \(error.localizedDescription)")
            songs = []
        }

        isLoading = false
    }
}

// MARK: - 曲の行ビュー

struct SongRow: View {
    let song: Song

    var body: some View {
        HStack(spacing: 12) {
            AsyncImage(url: URL(string: song.artworkUrl100)) { image in
                image
                    .resizable()
                    .aspectRatio(contentMode: .fill)
            } placeholder: {
                Color.gray.opacity(0.3)
            }
            .frame(width: 60, height: 60)
            .clipShape(RoundedRectangle(cornerRadius: 8))

            VStack(alignment: .leading, spacing: 4) {
                Text(song.trackName)
                    .font(.headline)
                    .lineLimit(1)

                Text(song.artistName)
                    .font(.subheadline)
                    .foregroundStyle(.secondary)
            }
        }
        .padding(.vertical, 4)
    }
}

#Preview {
    ContentView()
}

```

**このアプリは何をするものか：**

（アプリの動作を自分の言葉で説明する。スクリーンショットを貼ってもよい。）
<img width="284" height="509" alt="image" src="https://github.com/user-attachments/assets/3c1f1f69-5c82-4e14-8bcc-6e856b29420a" />


## コードの詳細解説

### データモデル（Codable構造体）

```swift
// 該当部分のコードを抜粋して貼る
```

**何をしているか：**
（この部分が果たしている役割を説明する）

**なぜこう書くのか：**
（別の書き方ではなく、この書き方が選ばれている理由を説明する）

**もしこう書かなかったら：**
（この部分を省略したり変えたりすると何が起きるか。実際に試した結果があればここに書く）

---

### API通信の処理

```swift
// 該当部分のコードを抜粋して貼る
```

**何をしているか：**

**なぜこう書くのか：**

**もしこう書かなかったら：**

---

### ビューの構成

```swift
// 該当部分のコードを抜粋して貼る
```

**何をしているか：**

**なぜこう書くのか：**

**もしこう書かなかったら：**

---

（必要に応じてセクションを増やす）

## 新しく学んだSwiftの文法・API

| 項目 | 説明 | 使用例 |
|------|------|--------|
| 例：`Codable` | JSONデータとSwiftの構造体を相互変換するプロトコル | `struct Song: Codable { ... }` |
| 例：`async/await` | 非同期処理を同期的に書ける構文 | `let data = try await URLSession.shared.data(from: url)` |
| | | |
| | | |
| | | |

## 自分の実験メモ

（模範コードを改変して試したことを書く）

**実験1：**
- やったこと：
- 結果：
- わかったこと：

**実験2：**
- やったこと：
- 結果：
- わかったこと：

## AIに聞いて特に理解が深まった質問 TOP3

1. **質問：**
- Swiftのguard let は後から追加された構文なの？ もしそれが本当なら、それまでプログラマは if let しか使えなかったの？ それは大変だったのか？ 歴史的背景やエピソードがあればそれらも教えてほしい。<br>
**得られた理解：**
- Gemini Pro 3.1に質問したところ、非常に興味深いエピソードを教えてもらった。後でコラム的な文章にまとめておく（TODOとして）


 -- guard文（およびguard let）はSwiftの最初のバージョンには存在せず、後から追加された構文です。

そして「それは大変だったのか？」という疑問についても、はい、とても大変でした。 当時のiOSアプリ開発者たちは、ある特定の「地獄」と戦っていました。

その歴史的背景と、guard letがいかにして開発者たちを救ったのか、当時のエピソードを交えて解説しますね。

1. guard文はいつ追加されたのか？
guard文は、2015年のWWDC（Appleの開発者会議）で発表された Swift 2.0 で導入されました。

2014年に華々しくデビューしたSwift 1.0には、確かにguardがなく、Optional（値が空かもしれない状態）を安全にアンラップ（中身を取り出す）するには、基本的に if let を使うしかありませんでした。

2. Swift 1.x時代の苦労：「破滅のピラミッド」
if let しかなかった時代、何が大変だったのか。それは**「Pyramid of Doom（破滅のピラミッド）」**と呼ばれるネスト（階層）地獄です。

例えば、「ユーザーがログインしていて、プロフィール画像があり、そのURLが有効な場合だけ画像をダウンロードする」という処理を書くとします。Swift 1.xの時代はこうなっていました。

```Swift
// Swift 1.x時代のコード（if letのネスト地獄）
func downloadProfileImage(user: User?) {
    if let validUser = user {
        if let profile = validUser.profile {
            if let imageURL = profile.imageURL {
                if let url = URL(string: imageURL) {
                    // ここでようやく画像をダウンロードできる！
                    // インデントが深すぎて読みにくい...
                    download(url)
                } else {
                    print("URLが無効です")
                }
            } else {
                print("画像URLがありません")
            }
        } else {
            print("プロフィールがありません")
        }
    } else {
        print("ユーザーがいません")
    }
}
```
コードが右にどんどん寄っていく形が「ピラミッド」のように見えるため、このように呼ばれていました。本来やりたい「画像のダウンロード」というメインロジックがコードの奥深くに追いやられてしまい、エラー処理もどこに対応しているのか分かりにくく、非常に読みにくいコードになっていたのです。

3. guard letの登場と革命
この問題を解決するために、Swift 2.0で guard が導入されました。先ほどのコードを guard let で書き換えると、こうなります。

```Swift
// Swift 2.0以降のコード
func downloadProfileImage(user: User?) {
    guard let validUser = user else {
        print("ユーザーがいません")
        return // 条件を満たさなければ早期退出
    }
    guard let profile = validUser.profile else {
        print("プロフィールがありません")
        return
    }
    guard let imageURL = profile.imageURL else {
        print("画像URLがありません")
        return
    }
    guard let url = URL(string: imageURL) else {
        print("URLが無効です")
        return
    }

    // メインロジックが一番浅いインデントで書ける！
    download(url)
}
```
(※現在では複数のguard letをカンマ区切りで一行にまとめることもできます)

4. なぜ guard はこれほど愛されたのか？
guard の革新性は、単に「ifの逆」というだけでなく、**「アンラップした変数のスコープ（使える範囲）」**を変えたことです。

if let の場合: アンラップされた変数は、ifのブロック { } の中でしか使えません。

guard let の場合: アンラップされた変数は、guard文を抜けた後も（関数が終わるまで）ずっと使えます。

プログラミングの世界には、古くから**「Bouncer Pattern（用心棒パターン）」**または「ガード節（Guard Clause）」というベストプラクティスがありました。「関数の頭で不正な条件を弾いてしまい（早期リターン）、関数のメイン部分はインデントなしでスッキリ書く」という手法です。

Swiftの設計者たちは、このベストプラクティスを言語レベルの機能として昇華させ、しかも「Optional変数のアンラップ」というSwift特有の機能と見事に融合させました。

エピソード：WWDCでの歓声
Swift 2.0が発表されたWWDC 2015のセッションでは、guard文がスクリーンに映し出された瞬間、会場の開発者たちから大きな拍手と歓声が上がりました。それだけ、みんな「破滅のピラミッド」にウンザリしていて、早期リターンを美しく書ける構文を待ち望んでいたのです。

今では当たり前のように使っている guard let ですが、実は「より読みやすく、より安全なコードを書きたい」という開発者たちの切実な願いから生まれた、Swiftの進化を象徴するような機能の一つなんですよ。

2. **質問：**
   **得られた理解：**

3. **質問：**
   **得られた理解：**

## この章のまとめ

（この章で学んだ最も重要なことを、未来の自分が読み返したときに役立つように書く）
