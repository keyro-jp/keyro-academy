# もし公開コードにAPIキーを置き忘れたら、AIに1,000万トークン食われる？

GitHubの「公開」ボタンを押したあと、設定ファイルにAPIキーが残っていたと気づく。想像するだけで胃が痛い。コードを読みに来た人より先に、誰かの自動収集プログラムが見つけたら？

**先に明言します。これは架空のヒヤリハットです。KeyroIMEでAPIキーの漏えいや1,000万トークンの被害が起きた、という報告ではありません。** OpenCoreを公開する側として、「もしも」を真面目に考えてみました。

![公開コードに秘密の鍵を残した場合を想像したイラスト。実際の漏えい画面ではありません](https://raw.githubusercontent.com/keyro-jp/keyro-academy/main/media/api-key-risk-hero.png)

## そもそも、何を公開したの？

[KeyroIME OpenCore](https://github.com/keyro-open/KeyroIME-OpenCore)は、Windows向け日本語入力ソフトのオープンソース版です。C++17のTSF入力部分とRustのローカル辞書サービスを組み合わせ、日本語の途中に英語、数字、記号が出てきても書き続けやすくすることを目指しています。コードはGPLv3で読めます。

完成済みの[KeyroIME Proの公式ダウンロード](https://github.com/keyro-jp/KeyroIME-Releases/releases/tag/pro-v1.1.0)は別の配布先です。Proは商用版。OpenCoreのソース公開を、Proの無償化や生成AI機能の提供開始と取り違えないでください。

公開するのは楽しい。でも、公開前の点検は少し怖い。小さなチームなら、たった一つの見落としで、開発より後始末に時間を使うことになるからです。

## APIキーとトークン、何が違う？

APIキーは、AIサービスに「このアカウントからのリクエストです」と示す認証情報。家の鍵に近いものです。トークンは、AIが文章を読み書きするときの処理単位。文字数や単語数と完全には一致せず、入力と出力の両方で使われます。

たとえば、**架空の計算**をしてみます。1回の利用で入力と出力を合わせて2,000トークン、1日500回、10日間なら、`2,000 × 500 × 10 = 10,000,000`トークン。鍵を持った第三者が勝手に使えば、その利用が持ち主のアカウントに計上される可能性があります。ただし、1,000万トークンは金額でも、今回の実測被害でもありません。費用はサービス、モデル、入力と出力の内訳、利用制限によって変わります。

![公開コード、APIキー、AI利用量と鍵の交換を示す概念図。実際の被害を示す図ではありません](https://raw.githubusercontent.com/keyro-jp/keyro-academy/main/media/api-key-token-flow.png)

## 「ファイルを消したから大丈夫」は危ない

ここがいちばん笑えないところ。公開後にファイルから鍵を消しても、Gitの履歴や取得済みのコピーに残る場合があります。もし本当に鍵を公開してしまったら、まず提供元でその鍵を無効化し、新しい鍵へ交換。利用履歴と請求も確認します。履歴の整理はその後です。

公開前には、ソースだけでなく設定ファイル、サンプル、コミット履歴も確認したい。鍵はコードに直書きせず、環境変数や秘密情報の管理機能へ。人間の目だけに頼らず、シークレットスキャンも使う。地味ですが、公開を続けるための仕事です。

## ソースを開く。だから、守るところは守る

KeyroIME OpenCoreを公開したのは、日本語入力の中身を開発者に見て、試して、直してもらいたいからです。日本の開発者も、中国語圏から日本語の開発環境で働く方も、Windows TSFとRustの構成をのぞいてみてください。

「公開コードに鍵を置いたら？」という想像で、こちらはもう十分に冷や汗をかきました。もし設計や実装が面白いと思ったら、[OpenCoreのリポジトリ](https://github.com/keyro-open/KeyroIME-OpenCore)にStarを一つお願いします。小さな開発チームには、その一つが次を作る励みになります。気づいた点はIssueやPull Requestでも歓迎です。

**キーワード：** KeyroIME、OpenCore、オープンソース、Windows、IME、APIキー、トークン、GitHub、シークレットスキャン、C++、Rust

**リンク：** [KeyroIME公式サイト](https://keyro.jp/) · [KeyroIME Pro v1.1.0をダウンロード](https://github.com/keyro-jp/KeyroIME-Releases/releases/tag/pro-v1.1.0) · [OpenCoreの公開ソース](https://github.com/keyro-open/KeyroIME-OpenCore)

確認日：2026-10-03。APIキーとトークンの説明は[OpenAIのAPIキー管理ガイド](https://help.openai.com/en/articles/5112595-best-practices-for-api-key-safety)、[トークンの説明](https://help.openai.com/en/articles/4936856-what-are-t)、[GitHubのシークレット対応ガイド](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository)を参考にしました。冒頭の事故と1,000万トークンは仮定であり、KeyroIMEの実被害ではありません。
