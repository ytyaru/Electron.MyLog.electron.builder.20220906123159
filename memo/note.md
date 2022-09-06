つぶやきを保存するElectron版6

　setting.jsonまわりをリファクタリングし、デフォルト値をセットするようにした。

<!-- more -->

# ブツ

* [リポジトリ][]

[リポジトリ]:https://github.com/ytyaru/Electron.MyLog.20220905091742

## インストール＆実行

```sh
NAME='Electron.MyLog.20220905091742'
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
	1. `username`：任意のGitHubユーザ名（デフォルト値：`git config --global user.name`）
	1. `email`：任意のGitHubメールアドレス（デフォルト値：`git config --global user.email`）
	1. `token`：`repo`スコープ権限を持ったトークン
	1. `repo.name`：任意リポジトリ名（デフォルト値：ルートディレクトリ名+`.Site`）
    1. 任意で以下もセットする
        1. `repo.description`: デフォルト値：`著者のつぶやきサイトです。`
        1. `repo.homepage`: （空なら自動で`https://${username}.github.io/${repo.name}/`）
1. `dst/mytestrepo/.git`が存在しないことを確認する（あれば`dst`ごと削除する）
1. GitHub上に同名リモートリポジトリが存在しないことを確認する（あれば削除するか別名にする）

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

[自動作成されたリポジトリ]:https://github.com/ytyaru/Electron.MyLog.20220905091742.Site
[自動作成されたリポジトリを手動でデプロイしたサイト]:https://ytyaru.github.io/Electron.MyLog.20220905091742.Site/

# 今回やったこと

　setting.jsonまわりをリファクタリングし、デフォルト値をセットするようにした。

## 項目追加

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
* [topics][]

## デフォルト値を自動でセットするようにした

* `name`はデフォルト値を生成するようにした。ルートディレクトリ名+`.Site`とする。
* `description`
    * デフォルト値＝`著者のつぶやきサイトです。`
    * README.mdにも同じテキストをセットする
* `homepage`
    * 初回push時に空ならデフォルト値として`https://${username}.github.io/${repo.name}/`セットする
    * README.mdにも同じテキストをセットする
* `topics`はGitAppsとかいう謎技術が必要かもしれない。そのときは対象外にする予定。まだAPIの実装はしていないため、setting.jsonにtopicsをセットしても反映されない。

　さらに次の項目を初回時デフォルト値として自動取得する。

* `username`: `git config --global user.name`
* `email`: `git config --global user.email`

　以下は手動でセットすること。

* `token`: GitHubのsettingsページからアクセストークンを手動で作成し、その値を`token`にセットすれば`git push`できる
* `address`: モナコイン用アドレスを作成し、`address`にセットすれば投げモナボタンが表示される

## 新しいWebAPIは極力使わずに実装する方針

　[topics][], [updateRepo][], [deleteRepo][], [authorization][]のような新しいWebAPIの使用が必要になりそうな部分を一切やらず、徹底的に避けた。どれもGitHubのページで同じことができるはずなので、実装する必要性が低いのが理由。代わりが効くのでそこに力を割きたくなかった。

### [topics][]

　べつに必須項目ではない。リポジトリを探すときに使えるかもしれないというだけ。一応JSONデータに用意したが[createRepo][]とは別のAPIを叩かねばならない。GitHubで同じことができるので実装する必要なしと判断した。

### [authorization][]

　トークン生成は[authorization][]を使えば自動化できそうにみえるが、現在はWeb Application flowとやらを経由せねばならないらしい。コマンドで自動化するこはできず、ユーザが手動で操作せねばならない。それならGitHubのsettingsページで作成するのと大差ない。よって各自で作成してもらうことにした。本当は自動化したかった……。昔はできたのに……。

[authorization]:https://docs.github.com/en/enterprise-server@3.1/rest/oauth-authorizations#create-a-new-authorization

### [createRepo][], [updateRepo][], [deleteRepo][]

　`description`, `homepage`, `topics`も画面UIが必要かと思った。しかし、それらはリモートリポジトリのメタデータであり、変更するときはGitHub API [updateRepo][]を使う必要がありそう。面倒なのでそれを実装せず、初回[createRepo][]時のみセットすることとする。もし画面UIとして表示してしまったら、いつでも変更できるかのように期待されそうなので画面UIも作らないようにした。

　さらにいえば`repo.name`を変更すると、`dst/`配下のディレクトリ名も変更しなおす必要がある。`.git/config`の`[remote "origin"]Url`のリポジトリ名部分も修正が必要。その後で`git`コマンドがちゃんと実行できるか未知数。

　リポジトリ削除[deleteRepo][]もできたら便利だと思う。でもそのためには`delete_repo`スコープ権限をもつトークンが必要だし、新しいAPIを実行せねばならない。GitHubで同じことができるので実装する必要なし。

[topics]:https://docs.github.com/ja/rest/repos/repos#replace-all-repository-topics
[createRepo]:https://docs.github.com/ja/rest/repos/repos#create-a-repository-for-the-authenticated-user
[updateRepo]:https://docs.github.com/ja/rest/repos/repos#update-a-repository
[deleteRepo]:https://docs.github.com/ja/rest/repos/repos#delete-a-repository
[authorization]:https://docs.github.com/en/enterprise-server@3.1/rest/oauth-authorizations#create-a-new-authorization

