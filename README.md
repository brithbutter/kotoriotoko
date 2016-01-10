# シェルスクリプト製Twitter怪人「恐怖！小鳥男」

## お前は何者だ？

POSIX原理主義に基づき、シェルスクリプトで組まれたTwitterクライアント怪人だ。

* 投稿系
  * 投稿（画像・動画付も可）、リツイート、それらの取消
  * お気に入り登録、取消
* ツイート閲覧系
  * タイムライン表示
  * 検索
* フォロー系
  * フォロー、解除、フォロー先・フォロワー表示

と、一通りのことができる。

POSIX原理主義集団「秘密結社シェルショッカー」は、これをもってTwitterにはびこる人間どもを洗脳し、世界征服を果たすのだ。ワッハッハ！

## 動かすのに必要なものその１（サーバー）

* WebアクセスできるUNIXホスト（これがなくては話にならん）
* 一部の符号化演算コマンドとして次のうちのどちらか
  * [OpenSSL](https://www.openssl.org/)
  * [LibreSSL](http://www.libressl.org/)
* HTTPアクセスコマンドとして次のうちのどちらか
  * [cURL](http://curl.haxx.se/)
  * [GNU Wget](http://www.gnu.org/software/wget/)

レンタルサーバーなら、大抵のところが要件を満たしているはずだ。

### ※なに、「それのどこがPOSIX原理主義なのか」だと？

HTTPアクセスコマンドのみならず、2014年にHeartbleedという**巨大なセキュリティーホールをもたらしたOpenSSLにまで手を出して、もはやそれのどこがPOSIX原理主義なのか、**と言いたいわけだなお前たちは。えぇい、黙れ黙れ！

確かにしょっちゅう脆弱性の見つかるソフトウェアに依存しているようではメンテナンス地獄から解放されず、POSIX原理主義の素晴らしさは発揮できん。だが、cURLコマンドやsendmailコマンドに手を出す時に言ったはずだ。

> 「同等の機能を備えた代替品がすぐに見つかる」、「すぐに乗り換えられる」という状態を担保すべく、利用するにしても基本的な部分だけにとどめるのだ。

と。幸いにしてOpenSSLは、Heartbleed事件の反省から、LibreSSLという同等機能の別実装が登場し、しかも"openssl"という名前まで一緒のコマンドまで用意されているのだ。つまり、**片方の実装に何か問題が起こったらもう片方の実装に簡単に乗り換えられることを担保しておる。**

それにだ！今回の侵略で利用する**Twitter API 1.1やOAuth1.0aがそもそも10年、20年の長きに渡って使えるとも思えん。** だから、Twitterに関してはこれくらいの担保があれば十分なのだよ！

## 動かすのに必要なものその２（Twitterアカウント）

さてサーバーの次は、アカウント登録だ。Twitterに投稿もするのだからTwitterアカウントが必要なのは言うまでも無かろう。持っていないのなら[ユーザー登録](https://twitter.com/signup)をするのだ。

そしてTwitterと連携するアプリケーションを動かすには、さらに[アプリケーション登録](https://apps.twitter.com/)もしなければならぬらしい。しかも2015年あたりから審査が厳しくなって、SMSを受信できる携帯電話が必要になりおった。我々のような秘密結社を阻止しようということか……、小癪な真似をしおって。そんなもの我々には無力だ！

とにかくそうやってアプリケーションの登録まで済ませ、次の4つのコードを手に入れるのだ。

* Consumer Key (API Key)
* Consumer Secret (API Secret)
* Access Token
* Access Token Secret

これさえ手に入れれば、あとはどうというこはない！

## ハウ・トゥ・インストール

では、怪人を起動するまでの方法（インストール）を教えてやろう。

### 0) cURL,Wgetや*SSLがなければインストール

先程言ったように、この小鳥男（プログラム）を動かすには、cURLまたはWget、そしてOpenSSLまたはLibreSSLが必要だ。もし、無いならインストールし、コマンドにパスを通しておくのだ。パッケージだろうとソースからmakeだろうと構わんし、設定もデフォルトのままで構わん。

### 1) GitHubからgit clone

そうしたらまずは、このリポジトリーをgit cloneせよ。「まずは」というかそれでインストールはおしまいだ！あとは設定のみ。

```sh:
$ cd <適当なインストールディレクトリ>
$ git clone https://github.com/ShellShoccar-jpn/kotoriotoko.git
```

もしgitコマンドが使えない場合は、このリポジトリーの[zipファイル](https://github.com/ShellShoccar-jpn/kotoriotoko/archive/master.zip)をダウンロード、展開し、その中の"BIN"と"TOOL"と"UTL"ディレクトリー内の各ファイルに実行パーミッションを与えるのだ。

```sh:
$ cd <適当なインストールディレクトリ>
$ wget https://github.com/ShellShoccar-jpn/kotoriotoko/archive/master.zip
$ unzip master.zip
$ chmod +x kotoriotoko/BIN/* kotoriotoko/TOOL/* kotoriotoko/UTL/*
```

### 2) 設定ファイルにアカウント情報を書き込む

残りは設定作業だけだ。

"CONFIG"ディレクトリーの中にある"COMMON.SHLIB.SAMPLE"というファイルを同じ場所に"COMMON.SHLIB"という名前でコピーして、

```sh:
$ cd <適当なインストールディレクトリ>/kotoriotoko/CONFIG
$ cp COMMON.SHLIB.SAMPLE COMMON.SHLIB
$ vi COMMON.SHLIB
```

この中身に、自分のアカウント名（ログインID）＋先程用意したTwitterの4つのコード（計5コード）を書き込むのだ。

```text:
            :
            :
######################################################################
# アカウント情報
######################################################################

readonly MY_scname='hogehoge'
readonly MY_apikey='1234567890123456789012345'
readonly MY_apisec='12345678901234567890123456789012345678901234567890'
readonly MY_atoken='1234567890-123456789012345678901234567890123456789'
readonly MY_atksec='123456789012345678901234567890123456789012345'
            :
            :
```

5つのコードは、それぞれだいたいこれくらいの長さになっているはずだ。あまりにも違うようならもう一度確認しておくがいい。

### ※ベアラートークンIDを設定する

例えばテレビ番組で「特定のハッシュタグを使ってツイートしてください」などといい、物凄い密度でツイートが集まることがある。そういったツイートを全部かき集めて分析し、侵略計画を策略するのに役立てたい者もお前たちの中にはいるかもしれん。しかし、通常のアクセス方法では最大で5秒あたり1回の頻度（厳密には15分に180回まで）というアクセス制限がある。しかも、1回あたり最大で100ツイートしか取れない。これでは、ツイートをかき集めるには心もとないな。

そこで、このアクセス頻度制限が緩和される btwsrch.sh（2秒あたり1回の頻度まで）と、btwtl.sh（3秒あたり1回の頻度まで）というコマンドを用意した。だが、これらを使うにはもう一つ追加の設定作業が必要だ。これらのコマンドが使いたいなら次の設定をやるがよい。

まずは、getbtwid.shというコマンドを実行せよ。すると、btw*.sh というコマンドを起動するのに必要なIDを問い合わせにいく。成功すると `MY_bearer='xxx......'` というIDが表示されるはずだ。

```sh:
$ cd <適当なインストールディレクトリ>/kotoriotoko/BIN
$ ./getbtwid.sh
MY_bearer='1234567890123456789012345678901234567890123456789012345678901234567890123456789012345678901234567890123456789012'

Write the variable into COMMON.SHLIB.
And you can use btw* commands.
$ 
```

あとは、さっき5つのIDを設定したのと同じように COMMON.SHLIB ファイルに追加せよ。 `MY_bearer=` という文字列ごと COMMON.SHLIB に貼り付けるのだ。


## つかいかた

まずは、インストールされたTwitterクライアント怪人のディレクトリー構成を見よ！これを見れば、もうだいたいの使い方はわかるだろう。BINディレクトリーの中にあるのが、怪人を動かしてTwitter民を侵略するためのプログラムだ。

### ファイル・ディレクトリー構成

```
.
|-- README.md ................ このファイル
|
|-- BIN ...................... Twitterコマンド群（普段使うのはこの中）
|   |
|   |-- tweet.sh ............. 与えられた文字列でツイートする
|   |-- retweet.sh ........... 指定されたツイートをリツイート
|   |-- deltweet.sh .......... 指定されたツイート・リツイートを削除
|   |-- twmediup.sh .......... 指定されたメディアファイル（画像等）をアップロード
|   |                          （tweet.shで画像付ツイートをする時のサブコマンドでもある）
|   |-- twvideoup.sh ......... 指定された動画メディアファイル（MP4）をアップロード
|   |                          （twmediup.shでMP4動画を指定された時のサブコマンドでもある）
|   |
|   |-- twtl.sh .............. 指定されたユーザーのタイムラインを表示する
|   |
|   |-- twsrch.sh ............ 指定された文字列で検索する
|   |
|   |-- twfav.sh ............. 指定されたツイートをお気に入り登録する
|   |-- twunfav.sh ........... 指定されたツイートのお気に入りを解除する
|   |
|   |-- twfollow.sh .......... 指定されたユーザーをフォローする
|   |-- twunfollow.sh ........ 指定されたユーザーのフォローをやめる
|   |-- twfer.sh ............. 指定されたユーザーのフォロワーを表示する
|   |-- twfing.sh ............ 指定されたユーザーのフォローユーザーを表示する
|   |
|   |-- getbtwid.sh .......... 高頻度APIアクセスコマンド（btw*.sh）用のIDを取得する
|   |-- btwsrch.sh ........... 指定された文字列で検索する（ベアラートークンモード）
|   |                          ※ 通常5秒あたり1回までの制限頻度が2秒あたり1回まで短縮可能
|   `-- btwtl.sh ............. 指定ユーザーのタイムラインを表示（ベアラートークンモード）
|                              ※ 通常5秒あたり1回までの制限頻度が3秒あたり1回まで短縮可能
|
|-- CONFIG ................... 設定ファイル置き場
|   |
|   |-- COMMON.SHLIB ......... 共通設定ファイル
|   |                          ・Twitter APIのキーやアクセストークン等を書き込む
|   |                          ・下の ".SAMPLE" ファイルをコピーして作る
|   `-- COMMON.SHLIB.SAMPLE .. 共通設定ファイルのテンプレート
|
|
|-- TOOL ..................... シェルスクリプト開発用コマンドセット "Open usp Tukubai" の一部
|   |                          ・だたしここに置いてあるのは、POSIX環境に移植したクローン
|   |
|   |-- calclock ............. 日常日時―UNIX時間変換コマンド
|   |                          ・Twitter APIからUTC日時で返す日時をJST日時等へ変換するのに利用
|   |                          ・Twitter APIが要求する、現在日時のUNIX時間表現の生成に利用
|   `-- self ................. 列抽出コマンド
|                              ・self 1 3 5 は、awk '{print $1,$3,$5}'と同じ
|                              ・可読性のために利用
|
`-- UTL ...................... その他、自作シェルスクリプト製コマンド置き場
    |
    |-- urlencode ............ URLエンコーダー
    |                          ・Twitter APIが要求するURLエンコード済文字列の生成に利用
    |-- parsrj.sh ............ JSONをシェルスクリプト向けに正規化するコマンド"PARSeR-Json"
    |                          ・Twitter APIが返すJSONの解読に利用
    |-- unescj.sh ............ エスケープ表現されたJSON中のユニコード文字を元に戻すコマンド
    |                          ・日本語ツイートがこれで読めるようになる
    |                          ・Twitter APIが返すJSONの解読に利用
    `-- mime-make ............ MIMEマルチパート作成作成コマンド
                               ・画像ファイルをTwitterサーバーにアップロードする時に利用
```

詳細な使い方は省略するが、各Twitterコマンドの書式が知りたくば"--help"オプションを付けて実行してみるがいい。それでだいたいわかるはずだ。

なぁに大丈夫だ、間違っても大したことないからいろいろ試してみるがいい。大変なことになるとしたら、周到な準備無しに殺人予告や爆破予告をツイートするくらいなものだ。まぁ我々組織としてはそういう使い方は大いに歓迎だがな、フハハハ……。

さぁお前たちもこの怪人を操作してTwitter民たちを炎上させ、世界征服を目指すのだ、ゆけぃ！


# ライセンス

何、ライセンスだと？愚問だな、好きにするがいい。似るなり焼くなり売るなり改造するなり、誰でも構わん。
この意味がわからぬか？パブリックドメインという意味だ。

ただ、一つだけ憶えておくことだ。使い出した瞬間、それはお前たちは我らに洗脳されたということだ。ワッハッハ！