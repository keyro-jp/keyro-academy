# Windows IMEを止めないために。C++のTSFとRustの辞書サービスを分ける設計

日本語入力は、ブラウザー、エディター、メール、表計算など、文字を入力するアプリの中で動きます。

もしIMEの中心へ大きな辞書や複雑な処理を全部詰め込んだら、便利には見えても、障害の影響まで入力先のアプリへ持ち込みかねません。

KeyroIME OpenCoreは、この問題に対して、Windowsの入力を受け持つC++のTSFシェルと、辞書検索を受け持つRustサービスを別プロセスに分けています。本稿では、公開コードから、その境界とフォールバックの考え方を追います。

## まず、IMEを三つの役割へ分ける

OpenCoreの実行時構成は、次の三つです。

```text
Windowsの入力先アプリ
        |
        v
KeyroIME.dll  -- 名前付きパイプ -->  keyro_service.exe
        |
        +-- ローカルのローマ字かな変換

keyro_tray.exe  -- 共有設定 -->  KeyroIME.dll
```

- `KeyroIME.dll`：C++17で実装したTSF / COMテキストサービス。入力、変換中の文字列、候補UI、キー配列、ローカルフォールバックを担当します。
- `keyro_service.exe`：Rustで実装したWindowsサービス。辞書検索、候補の呼び出しと順位付け、ページング、選択履歴を担当します。
- `keyro_tray.exe`：通知領域メニュー、状態表示、ライセンス画面など、ユーザーセッション側のUIを担当します。

設計上の重要な制約は、入力先アプリへ読み込まれるTSF DLLを軽く保つことです。公開アーキテクチャでは、TSF DLLへRust本体、完全な辞書、ユーザー設定ファイル、ネットワーク処理、AIランタイムを読み込まない方針を明記しています。

これは言語の好みではなく、**障害範囲を小さくするための境界**です。

## C++とRustの間は、小さなバイナリプロトコル

TSFシェルと辞書サービスは、Windowsのローカル名前付きパイプ `\\.\pipe\KeyroIME.Service.v1` で通信します。

候補検索の要求ヘッダーは9バイトです。

```text
u8  request_type
u32 page
u32 payload_byte_length
u8[] payload
```

応答ヘッダーは8バイトです。

```text
u8  status
u8  candidate_count
u16 total_pages
u32 payload_byte_length
u8[] payload
```

整数はリトルエンディアン、文字列はUTF-8。入力は最大4,096バイト、1ページは最大5候補、クライアントが受け付ける応答ペイロードは最大64KiBです。

JSONではなく固定長ヘッダーを持つ小さな形式にすることで、C++とRustの双方で、長さ、状態、ページ番号を明示的に検証できます。仕様を変えたときは、コード上の定数と設計書が一致することを検査するスクリプトも用意されています。

## サービスが止まっても、入力まで止めない

辞書サービスが利用できないとき、TSFシェルはローカルのローマ字かな変換と英字入力へフォールバックします。

たとえば公開テストでは、次のような変換を確認しています。

```text
jya       → じゃ
lya       → りゃ
insuto-ru → いんすとーる
github    → github
qcheck    → qcheck
```

辞書候補や順位付けの全機能を再現するのではなく、最低限の入力継続をTSF側へ残す考え方です。機能を二重実装するのではなく、障害時に守る範囲を絞っています。

名前付きパイプのクライアントには8ミリ秒のタイムアウト定数があります。公開されているフェイルオーバーのスモークテストは、応答しないテスト用サーバーを作り、タイムアウトとして失敗し、10ミリ秒未満で制御が戻ることを検査します。

ここでの8ミリ秒や10ミリ秒は、一般環境での速度を保証するベンチマーク値ではありません。**入力処理を長く待たせないための実装上の期限とテスト条件**です。

## 正常系より、境界の失敗を先に試す

公開テストが確認しているのは、候補が返る正常系だけではありません。

- 4,096バイトを超える要求を送らないか
- サービスが応答しないとき、期限内に失敗として扱えるか
- サービスなしでもローマ字かな変換を継続できるか
- バックスペースでローマ字の単位を壊さないか
- C++側とRust側でプロトコル定数が一致しているか

IMEでは、変換品質だけでなく、「壊れたときに入力先アプリを巻き込まないこと」も品質です。プロセス分離とフォールバックは、その品質をコードとテストへ落とし込む方法です。

## ローカルIPCでも、信頼境界を言い過ぎない

OpenCoreのパイプはリモートクライアントを拒否し、Windowsのテキストホストに必要な主体へACLを限定する設計です。不完全な要求は短い読み取り期限の後に切断します。

一方で、公開設計書は、このパイプを同じ対話型Windows環境にあるアプリ同士の認可境界とは見なしていません。

「ローカルだから安全」と一言で済ませず、何を制限し、何を保証しないかを分ける。この記述は、デスクトップアプリのIPCを設計するときにも参考になります。

## 公開コードで確認する

今回参照した実装と仕様は、すべてKeyroIME OpenCoreの公開リポジトリで確認できます。

- [アーキテクチャ](https://github.com/keyro-open/KeyroIME-OpenCore/blob/76420ab66400dabc42f3b794f9ccb24a48884bd2/doc/OPENCORE_ARCHITECTURE.md)
- [IPC Protocol v1](https://github.com/keyro-open/KeyroIME-OpenCore/blob/76420ab66400dabc42f3b794f9ccb24a48884bd2/doc/IPC_PROTOCOL_V1.md)
- [Rust側のプロトコル実装](https://github.com/keyro-open/KeyroIME-OpenCore/blob/76420ab66400dabc42f3b794f9ccb24a48884bd2/src/keyro_service/src/protocol.rs)
- [C++側の名前付きパイプクライアント](https://github.com/keyro-open/KeyroIME-OpenCore/blob/76420ab66400dabc42f3b794f9ccb24a48884bd2/src/tsf_shell/ipc/named_pipe_client.h)
- [IPCフェイルオーバーのスモークテスト](https://github.com/keyro-open/KeyroIME-OpenCore/blob/76420ab66400dabc42f3b794f9ccb24a48884bd2/src/tsf_shell/ipc_failover_smoke.cpp)
- [ローカルフォールバックのスモークテスト](https://github.com/keyro-open/KeyroIME-OpenCore/blob/76420ab66400dabc42f3b794f9ccb24a48884bd2/src/tsf_shell/local_fallback_smoke.cpp)

KeyroIME OpenCore本体はGNU GPLv3で公開しています。Windows TSF、C++とRustのプロセス分離、名前付きパイプ、入力フォールバックに関心があれば、コード、Issue、Pull Requestから参加してください。

[keyro-open/KeyroIME-OpenCoreを開く](https://github.com/keyro-open/KeyroIME-OpenCore)

**キーワード：** Windows、IME、TSF、COM、C++、Rust、名前付きパイプ、IPC、フォールバック、KeyroIME OpenCore

確認日：2026-10-03。本文はKeyroIME OpenCore v1.0.6.16、公開コミット `76420ab66400dabc42f3b794f9ccb24a48884bd2` の仕様書と実装に基づきます。数値は公開コード上の制限値・テスト条件であり、一般環境の性能保証ではありません。
