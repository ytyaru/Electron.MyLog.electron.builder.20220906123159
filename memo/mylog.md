# GitHub Pagesにpushするファイル一式の作成

　HTTPSにアップロードすることで動作するようにした。詳細は以下ソースコード参照。

* src/js/app/github/site-maker.js

　ざっくりいうと以下の通り。

* mpurse APIを使い投げモナボタンを実装する
* sql.js（WASM版）でSQLファイルmylog.dbの内容を読み取る
* 必要な各ファイルをコピーなり一部書き換えなりパス変更なりをする
* つぶやきの日付表示バグを修正した

　Node.jsでなく、ブラウザ上で動作するコードにする必要があった。基本的には以下をベースにしている。

* [つぶやきを保存するツール6][]

[つぶやきを保存するツール6]:https://monaledge.com/article/482






# git pushのアップロードに失敗してる謎

　Electronでshellコマンドを実行しgit pushしたが、なぜか一部ファイルのアップロードがされなかった。ローカルファイルのコピーは成功しているが、GitHubのソースコード側では一部アップされてない。原因不明。

<!-- more -->

# 症状

　投げモナボタンの画像ファイルがアップロードされていなかった。原因不明。そのせいで投げモナボタンが表示されない。

# ブツ

* [リポジトリ][]

[リポジトリ]:https://github.com/ytyaru/Electron.MyLog.20220831120615

## インストール＆実行

```sh
NAME='Electron.MyLog.20220831120615'
git clone https://github.com/ytyaru/$NAME
cd $NAME
npm install
npm start
```

### 準備

1. [GitHubアカウントを作成する](https://github.com/join)
1. `repo`スコープ権限をもった[アクセストークンを作成する](https://github.com/settings/tokens)
1. `npm start`でアプリ起動し終了する（`db/setting.json`ファイルが自動作成される）
1. `db/setting.json`に以下をセットしファイル保存する
	1. `address`：任意のモナコイン用アドレス
	1. `username`：任意のGitHubユーザ名
	1. `email`：任意のGitHubメールアドレス
	1. `token`：`repo`スコープ権限を持ったトークン
	1. `repo`：任意リポジトリ名（`mytestrepo`等）
1. `dst/mytestrepo/.git`が存在しないことを確認する（あれば`dst`ごと削除する）
1. GitHub上に同名リモートリポジトリが存在しないことを確認する（あれば削除する）

### 実行

1. `npm start`で起動またはアプリで<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>R</kbd>キーを押す（リロードする）
1. `git init`コマンドが実行される
	* `repo/リポジトリ名`ディレクトリが作成され、その配下に`.git`ディレクトリが作成される
1. [createRepo][]実行後、リモートリポジトリが作成される

### GitHub Pages デプロイ

　アップロードされたファイルからサイトを作成する。

1. アップロードしたユーザのリポジトリページにアクセスする（`https://github.com/ユーザ名/リポジトリ名`）
1. 設定ページにアクセスする（`https://github.com/ユーザ名/リポジトリ名/settings`）
1. `Pages`ページにアクセスする（`https://github.com/ユーザ名/リポジトリ名/settings/pages`）
    1. `Source`のコンボボックスが`Deploy from a branch`になっていることを確認する
    1. `Branch`を`master`にし、ディレクトリを`/(root)`にし、<kbd>Save</kbd>ボタンを押す
    1. <kbd>F5</kbd>キーでリロードし、そのページに`https://ytyaru.github.io/リポジトリ名/`のリンクが表示されるまでくりかえす（***数分かかる***）
    1. `https://ytyaru.github.io/リポジトリ名/`のリンクを参照する（デプロイ完了してないと404エラー）

### 結果

* [自動作成されたリポジトリを手動でデプロイしたサイト][]
* [自動作成されたリポジトリ][]

[自動作成されたリポジトリ]:https://github.com/ytyaru/Electron.MyLog.Upload.Test.20220831120615
[自動作成されたリポジトリを手動でデプロイしたサイト]:https://ytyaru.github.io/Electron.MyLog.Upload.Test.20220831120615/

　投げモナボタンが表示されなかった。ボタンの画像ファイルがアップロードされなかったせい。`asset/`ディレクトリまるごと。なぜアップされなかったかは不明。ちゃんと`site-maker.js`で以下のようなコードを書いてあるのに。

```javascript
this.#cp(`asset/`),
```

　ローカルリポジトリ内にはファイルコピーされていた。なのにGitHubにアップロードされてない。

　非同期がうまくいってない感じかな？　でもちゃんと`main.js`で`await`しているし。謎。

```javascript
await maker.make()
await git.push()
```













つぶやきを保存するElectron版4

　

<!-- more -->

# ブツ

* [リポジトリ][]

[リポジトリ]:https://github.com/ytyaru/Electron.MyLog.20220903102841

## インストール＆実行

```sh
NAME='Electron.MyLog.20220903102841'
git clone https://github.com/ytyaru/$NAME
cd $NAME
npm install
npm start
```

### 準備

1. [GitHubアカウントを作成する](https://github.com/join)
1. `repo`スコープ権限をもった[アクセストークンを作成する](https://github.com/settings/tokens)
1. [インストール＆実行](#install_run)してアプリ終了する
	1. `db/setting.json`ファイルが自動作成される
1. `db/setting.json`に以下をセットしファイル保存する
	1. `username`:任意のGitHubユーザ名
	1. `email`:任意のGitHubメールアドレス
	1. `token`:`repo`スコープ権限を持ったトークン
	1. `repo`:任意リポジトリ名（`mytestrepo`等）
	1. `address`:任意モナコイン用アドレス（`MEHCqJbgiNERCH3bRAtNSSD9uxPViEX1nu`等）
1. `dst/mytestrepo/.git`が存在しないことを確認する（あれば`dst`ごと削除する）
1. GitHub上に同名リモートリポジトリが存在しないことを確認する（あれば削除する）

### 実行

1. `npm start`で起動またはアプリで<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>R</kbd>キーを押す（リロードする）
1. テキストエリアに任意のテキストを入力する
1. `つぶやく`ボタンを押す
    * `repo/リポジトリ名`ディレクトリが作成され、その配下に`.git`ディレクトリが作成される
    * [createRepo][]実行後、リモートリポジトリが作成される

### GitHub Pages デプロイ

　アップロードされたファイルからサイトを作成する。

1. アップロードしたユーザのリポジトリページにアクセスする（`https://github.com/ユーザ名/リポジトリ名`）
1. 設定ページにアクセスする（`https://github.com/ユーザ名/リポジトリ名/settings`）
1. `Pages`ページにアクセスする（`https://github.com/ユーザ名/リポジトリ名/settings/pages`）
    1. `Source`のコンボボックスが`Deploy from a branch`になっていることを確認する
    1. `Branch`を`master`にし、ディレクトリを`/(root)`にし、<kbd>Save</kbd>ボタンを押す
    1. <kbd>F5</kbd>キーでリロードし、そのページに`https://ytyaru.github.io/リポジトリ名/`のリンクが表示されるまでくりかえす（***数分かかる***）
    1. `https://ytyaru.github.io/リポジトリ名/`のリンクを参照する（デプロイ完了してないと404エラー）

　すべて完了したリポジトリとそのサイトが以下。

* [作成DEMO][]
* [作成リポジトリ][]

[作成DEMO]:https://github.com/ytyaru/Electron.MyLog.Upload.Test.20220903102841
[作成リポジトリ]:https://github.com/ytyaru/Electron.MyLog.Upload.Test.20220903102841

　投げモナボタンも表示されてる。よしよし。

# 今回やったこと

## git.js

* git pushに必要なデータの
    * 存在確認（非null判定のみ（GitHubのほうに存在するか判定する方法は不明））
    * バリデーション（やらない。リポジトリ名の仕様などが明記されていない等の理由。問題あればGitHubAPIリクエスト時にHTTP4xxエラーになると思われる）
* git config --localで値セットするために`cd`した
* `#post`ボタン押下時に`git push`する

## 画面UIをワークフローにあわせたい

* `つぶやく`ボタンで`git init`, `git push`する
    * setting.jsonファイルの状態に応じてエラーダイアログを表示する
* `削除`ボタンで`git push`する

## SQLite3まわりのバグ修正

* `window.myApi.insert`呼び出しの`await`忘れ
* `window.myApi.exportDb`呼び出しされずファイル保存されてなかった
    * `insert`, `delete`で呼び出す

```javascript
const r = await window.myApi.insert({content:content, created:now});
```

  `site-maker.js`の`#mpurseSendButtonCode()`で以下修正。

```javascript
code = code.replace(/\'\.\/src\/asset\/image\/monacoin\/\'/g, `'./asset/image/monacoin/'`)
```












つぶやきを保存するElectron版6

# やりたいこと

[createRepo]:https://docs.github.com/ja/rest/repos/repos#create-a-repository-for-the-authenticated-user
[topics]:https://docs.github.com/ja/rest/repos/repos#replace-all-repository-topics

- [x] setting.json
    - [x] 項目追加
        - [x] `homepage`
        - [x] `description`
        - [x] [topics][]
    - [x] デフォルト値
        - [x] `username`,`email`: git config globalから取得してセットする
        - [x] `repo.name`に`Electron.MyLog.yyyyMMddHHmmss`のようなデフォルト名をセットする
        - [x] `homepage`は初回push時、値が空なら`https://${username}.github.io/${repo.name}/`にする
- [ ] push
    - [ ] 初回
        - [x] アップロードファイルを必要最小限にする（未使用ライブラリ等を省く）
    - [ ] 二回目以降
- [ ] リモートリポジトリ・メタデータ修正（これは実装しなくてもいい）
    - [ ] name
    - [ ] description
    - [ ] homepage
    - [ ] topics
- [ ] リモートリポジトリ削除（これは実装しなくてもいい）
    - [ ] アクセストークンに`delete_repo`スコープ権限が必要
- [ ] ドキュメント作成
    - [ ] ファイルを用意する
        - [ ] README.md
            - [x] README.md
            - [ ] README.ja.md
            - [ ] README.en.md
            - [ ] Content
                - [ ] Install
                - [ ] Setting
                - [ ] Features
                - [ ] Notes
                - [ ] Requirementa
                    - [ ] git version
                    - [ ] Node.js version
                    - [ ] Electron version
                    - [ ] electron-fetch version
                    - [ ] sql.js version
                    - [ ] mpurse version
        - [ ] LICENSE.txt
            - [x] cc0-1.0（法の許す限り著作権放棄）
            - [ ] cc-by-4.0
            - [ ] cc-by-sa-4.0
            - [ ] mit（cc-by風）
            - [ ] agpl（最強のコピーレフト）
    - [ ] index.html
        - [x] Copyright表示
            - [x] ユーザ名
            - [x] pushした年
        - [x] サイト元リポジトリURLリンク
        - [ ] About
            - [ ] サイト趣旨（Authorが何かをつぶやくサイトです等）
            - [ ] ライセンス
            - [ ] サイト実体（ソースコード）
                - [ ] サイト元リポジトリ
            - [ ] 作成方法
                - [ ] サイト元作成リポジトリ（Electron）
            - [ ] 説明
                - [ ] つぶやく内容
                    - [ ] テキスト
                    - [ ] URLリンク
                    - [ ] 画像
                    - [ ] 音声
                    - [ ] 動画
                - [ ] メタ
                    - [ ] 日付
                    - [ ] リンク
                    - [ ] 投げモナボタン
                    - [ ] コメントボタン
        - [ ] Author
            - [ ] 名前
            - [ ] 利用SNSリンク
        - [ ] HowTo
            - [ ] つぶやく
            - [ ] 削除
        - [ ] Install
        - [ ] Setting
- [ ] Electronをパッケージ化してバイナリファイルを作成する
- [ ] 機能追加
    - [ ] AutoPager（最新20件ずつ表示するなど）
    - [ ] つぶやき一件ごとに独立したHTMLページを作りリンクする
        - [ ] 以下どちらで出力するのがよいかわからないので両方可能なようにしたい
            - [ ] 動的HTML出力
            - [ ] 静的HTML出力
    - [ ] コメント受付・表示（Webmention）
- [ ] 調査
    - [ ] [Electronでブラウザ拡張機能が使えるか](https://www.electronjs.org/ja/docs/latest/api/extensions)

# 前回やったこと

* アップロードファイルを必要最小限にする（未使用ライブラリ等を省く）
* README.md
* LICENSE.txt
* index.html
    * サイト元リポジトリURLリンク
    * Copyright
        * yearは2022で固定
            * 本当は最初につぶやいた年をセットしたい
            * よく考えると最初に`git init`やcreateRepoした日時の年でいいから`new Date().getFullYear()`でイケる

# 今回やったこと

- [x] setting.json
    - [x] 項目追加
        - [x] `homepage`
        - [x] `description`
        - [x] [topics][]
    - [x] デフォルト値
        - [x] `username`,`email`: git config globalから取得してセットする
        - [x] `repo.name`に`Electron.MyLog.yyyyMMddHHmmss`のようなデフォルト名をセットする
        - [x] `homepage`は初回push時、値が空なら`https://${username}.github.io/${repo.name}/`にする

# 発見したバグ

* 内部リンクが機能しない
    * `<a id="id-0">`, `index.html#id-0`

## 内部リンクが機能しない

　`<a id="">`にセットする先頭文字は数字だとダメらしい。そこで`id-`を先頭に付与してID値をセットするよう変更した。

　それでも機能しない。

　原因は動的にHTMLを作成しているせいか？　ブラウザのURL欄にアンカーつきURLを入力すると、HTMLを作成する。そのあとでアンカー箇所にスクロールしてほしかったが、スクロールしない。


　そもそも内部リンクは本当にこのような形式でいいのか？

* つぶやき一件ごとに独立したページをもたせたほうがいいかも
* それは静的HTML、動的HTMLどちらにすべきか

　仮に今のまま1ページに全件表示するとしたらパフォーマンス問題が出てくる。

* 最新順に20件ずつ表示するなどすべきでは？
* そうした場合、やはりつぶやき一件ごとに独立したページにしたほうがよいのでは？

　ちゃんと考えると、ものすごく大変なことになりそうな気がする。


# setting.json

　前回までは以下のとおりだった。

```javascript
{
    "mona":{
        "address":""
    },
    "github":{
        "username":"",
        "email":"",
        "token":"",
        "repo":""
    }
}
```

　それを以下のように追加した。

```javascript
{
    "mona":{
        "address":""
    },
    "github":{
        "username":"",
        "email":"",
        "token":"",
        "repo":{
            "name":"",
            "description":"",
            "homepage":"",
            "topics":["", ""],
        }
    }
}
```

　今回は以下の3項目を追加した。

* homepage
* description
* [topics](https://docs.github.com/ja/rest/repos/repos#replace-all-repository-topics)

　修正具合は以下。

* `name`はデフォルト値を生成するようにした。ルートディレクトリ名+`.Site`とする。
* `description`はREADME.mdにも同じテキストをセットする。デフォルト値＝`著者のつぶやきサイトです。`
* `homepage`は初回push時に空ならデフォルト値として`https://${username}.github.io/${repo.name}/`セットする。
* `topics`はGitAppsとかいう謎技術が必要かもしれない。そのときは対象外にする予定。まだAPIの実装はしていないため、setting.jsonにtopicsをセットしても反映されない。

　さらに次の項目を初回時デフォルト値として自動取得する。

* `username`: `git config --global user.name`
* `email`: `git config --global user.email`

　以下は手動でセットすること。

* `token`: GitHubのsettingsページからアクセストークンを手動で作成し、その値を`token`にセットすれば`git push`できる
* `address`: モナコイン用アドレスを作成し、`address`にセットすれば投げモナボタンが表示される

　トークン生成は[authorization][]を使えば自動化できそうにみえるが、現在はWeb Application flowとやらを経由せねばならないらしい。コマンドで自動化するこはできず、ユーザが手動で操作せねばならない。それならGitHubのsettingsページで作成するのと大差ない。よって各自で作成してもらうことにした。本当は自動化したかった……。


　`description`, `homepage`, `topics`も画面UIが必要かと思った。しかし、それらはリモートリポジトリのメタデータであり、変更するときはGitHub API [updateRepo][]を使う必要がありそう。面倒なのでそれを実装せず、初回[createRepo][]時のみセットすることとする。もし画面UIとして表示してしまったら、いつでも変更できるかのように期待されそうなので画面UIも作らないようにした。

　さらにいえば`repo.name`を変更すると、`dst/`配下のディレクトリ名も変更しなおす必要がある。`.git/config`の`[remote "origin"]Url`のリポジトリ名部分も修正が必要。その後で`git`コマンドがちゃんと実行できるか未知数。

　ようするに、今回は[topics][], [updateRepo][], [deleteRepo][], [authorization][]など新しいWebAPIの使用が必要になりそうな部分を一切やらず、徹底的に避けた。GitHubのページでできるはずなので、必要性が低いのが理由。代わりが効くのでそこに力を割きたくなかった。

[topics]:https://docs.github.com/ja/rest/repos/repos#replace-all-repository-topics
[updateRepo]:https://docs.github.com/ja/rest/repos/repos#update-a-repository
[deleteRepo]:https://docs.github.com/ja/rest/repos/repos#delete-a-repository
[authorization]:https://docs.github.com/en/enterprise-server@3.1/rest/oauth-authorizations#create-a-new-authorization








# 今回やりたいこと

## ドキュメント整備

* 作成元リポジトリのREADME.md
    * Install
    * Setting
    * Start
    * Usage
* 作成元アプリ画面
    * HowTo
        * つぶやく
        * 削除する
        * 設定する
    * このアプリでユーザが作った成果物
        * 作成後サイトリポジトリURL
        * 作成後サイトURL
    * このアプリのソースコード
        * 作成元リポジトリURL
* 作成後サイトリポジトリのREADME.md
    * 作成後サイトURL
    * 作成元リポジトリURL
* 作成後サイト画面
    * 作成後サイトリポジトリURL
    * 作成元リポジトリURL

## ビルド


# 今回やったこと

- [ ] 作成元リポジトリのREADME.md
    - [ ] プロジェクト名
    - [ ] デモ
    - [x] 特徴
    - [x] 開発環境
    - [ ] インストール
        - [x] git
        - [x] Node.js
        - [ ] このアプリ
            - [ ] プロジェクト名
    - [x] 準備
        - [x] アカウント・トークン作成
        - [x] 設定
    - [x] 使い方
        - [x] つぶやく
        - [x] 削除
    - [ ] GitHub Pages デプロイ
        - [ ] デモURL
    - [ ] 著者
    - [ ] ライセンス

## Electronで指定URLを新しいウインドウで表示する

* https://www.electronjs.org/ja/docs/latest/api/window-open

```javascript
window.open('https://github.com', '_blank', 'top=500,left=200,frame=false,nodeIntegration=no')
```

　Electronでも指定URLを表示できた。

## index.html

* リポジトリURLリンクを設置した
* 自動作成リポジトリ・サイトのURL表示するボタンを設置した
* 使い方に設定の項目を追加した

