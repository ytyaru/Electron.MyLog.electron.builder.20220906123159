# electron-builderでパッケージングする

　実行形式ファイルに固める。

<!-- more -->

# 経緯

　かつて[Electronを使ってみる][]のとき、Electronのクイックスタートに書いてあった通り`electron-forge`を使ってビルドを試みたが、エラーが出てできなかった。

[Electronを使ってみる]:https://monaledge.com/edit/487

　今回は[electron-builder][]を使ってみる。

[electron-builder]:https://www.electron.build/
[configuration]:https://www.electron.build/configuration/configuration
[file-patterns]:https://www.electron.build/file-patterns

## 参考

* [外部URLやWebサービスを Electron でデスクトップアプリにするのをやってみる(2022年2月版)]
* [Electronプロジェクトのビルド][]
* [ビルドコマンドを実行][]
* [electron-builder][]
	* [configuration][]
	* [file-patterns][]

[外部URLやWebサービスを Electron でデスクトップアプリにするのをやってみる(2022年2月版)]:https://zenn.dev/junki555/articles/cb3b039ce6d43a
[Electronプロジェクトのビルド]:https://mijinc0.github.io/blog/20200816_electron_builder/
[ビルドコマンドを実行]:https://qiita.com/saki-engineering/items/203892838e15b3dbd300#4%E3%83%93%E3%83%AB%E3%83%89%E3%82%B3%E3%83%9E%E3%83%B3%E3%83%89%E3%82%92%E5%AE%9F%E8%A1%8C

# [electron-builder][]

## インストール

```sh
npm i -D electron-builder
```

# 設定ファイル

## package.json 抜粋

```javascript
{
  "name": "electron-github-api",
  "version": "1.0.0",
  "description": "",
  "main": "src/js/main.js",
  "scripts": {
    "start": "electron .",
    "build": "electron-bilder build -lmw",
    "build_linux ": "electron-bilder build -l",
    "build_mac": "electron-bilder build -m",
    "build_win": "electron-bilder build -w",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "build": {
    "appId": "electron.mylog",
    "productName": "electron.mylog",
    "directories": {
      "output": "build"
    },
    "files": [
      "index.html",
      "src",
      "package.json",
      "package-lock.json"
    ],
    "mac": {
      "icon": "assets/mac/icon.icns",
      "target": [
        "dmg"
      ]
    },
    "win": {
      "icon": "assets/win/icon.ico",
      "target": "nsis"
    },
    "nsis":{
      "oneClick": false,
      "allowToChangeInstallationDirectory": true
    }
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "electron": "^20.0.2",
    "electron-builder": "^23.3.3"
  },
  "dependencies": {
    "axios": "^0.27.2",
    "electron-fetch": "^1.7.4",
    "sql.js": "^1.7.0"
  }
}
```

### ビルドコマンド

```javascript
  "scripts": {
    "start": "electron .",
    "build": "electron-bilder build -lmw",
    "build_linux ": "electron-bilder build -l",
    "build_mac": "electron-bilder build -m",
    "build_win": "electron-bilder build -w",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
```

### ビルド設定

```javascript
  "build": {
    "appId": "electron.mylog",
    "productName": "electron.mylog",
    "directories": {
      "output": "build"
    },
    "files": [
      "index.html",
      "src",
      "package.json",
      "package-lock.json"
    ],
    "mac": {
      "icon": "assets/mac/icon.icns",
      "target": [
        "dmg"
      ]
    },
    "win": {
      "icon": "assets/win/icon.ico",
      "target": "nsis"
    },
    "nsis":{
      "oneClick": false,
      "allowToChangeInstallationDirectory": true
    }
  },
```

# 実行

　参考にした記事をみてコマンドを試した。

## エラー1

```sh
npm build_linux
```
```sh
Unknown command: "build_linux"

Did you mean one of these?
    npm run build_linux  # run the "build_linux " package script
    npm run build_win # run the "build_win" package script

To see a list of supported npm commands, run:
  npm help
```

　え、`package.json`の`scripts`でコマンドを作れるのではなかったの？　なんか勘違いしてた？

　ログに`npm run build_linux`とか出てるので、次はそれを叩いてみよう。

## エラー2

```sh
$ npm run build_linux
npm ERR! Missing script: "build_linux"
npm ERR! 
npm ERR! Did you mean one of these?
npm ERR!     npm run build_linux  # run the "build_linux " package script
npm ERR!     npm run build_win # run the "build_win" package script
npm ERR! 
npm ERR! To see a list of scripts, run:
npm ERR!   npm run

npm ERR! A complete log of this run can be found in:
npm ERR!     /home/pi/.npm/_logs/2022-09-06T03_53_41_226Z-debug-0.log
```

　コマンドがないだと？

## エラー3

　`npx`ってのもある。`package.json`に書いたコマンドをそのまま渡してみる。

```sh
$ npx run electron-bilder build -l
Need to install the following packages:
  run
Ok to proceed? (y) 
```
```sh
Watching /tmp/work/Electron.MyLog.electron.builder.20220906123159 and all sub-directories not excluded by your .gitignore. Will not monitor dotfiles.
Found & ignored ./node_modules ; is listed in .gitignore
Found & ignored ./src/css/styles.css ; has ignored extension
Found & ignored ./src/css/toastify.css ; has ignored extension
Found & ignored ./src/css/toot-dialog.css ; has ignored extension
Found & ignored ./src/css/transaction.css ; has ignored extension
Found & ignored ./src/css/ui.css ; has ignored extension
Found & ignored ./src/db/mylog.db ; has ignored extension
Found & ignored ./src/js/app/github/export/style.css ; has ignored extension
Found & ignored ./src/lib/tingle/0.16.0/tingle.css ; has ignored extension
Found & ignored ./src/lib/toastify/1.11.2/min.css ; has ignored extension

Starting: electron-bilder build -l
node:internal/modules/cjs/loader:936
  throw err;
  ^

Error: Cannot find module '/tmp/work/Electron.MyLog.electron.builder.20220906123159/electron-bilder'
    at Function.Module._resolveFilename (node:internal/modules/cjs/loader:933:15)
    at Function.Module._load (node:internal/modules/cjs/loader:778:27)
    at Function.executeUserEntryPoint [as runMain] (node:internal/modules/run_main:77:12)
    at node:internal/main/run_main_module:17:47 {
  code: 'MODULE_NOT_FOUND',
  requireStack: []
}
```

　ここでフリーズした。モジュールが見つからない？　なんのこっちゃ。

## 成功

　`./node_modules/.bin/electron-builder`を直接叩いた。数分かかったがビルドできた！

```
$ ./node_modules/.bin/electron-builder
  • electron-builder  version=23.3.3 os=5.10.103-v7l+
  • loaded configuration  file=package.json ("build" field)
  • description is missed in the package.json  appPackageFile=/tmp/work/Electron.MyLog.electron.builder.20220906123159/package.json
  • writing effective config  file=build/builder-effective-config.yaml
  • packaging       platform=linux arch=armv7l electron=20.0.3 appOutDir=build/linux-armv7l-unpacked
  • downloading     url=https://github.com/electron/electron/releases/download/v20.0.3/electron-v20.0.3-linux-armv7l.zip size=78 MB parts=8
  • downloaded      url=https://github.com/electron/electron/releases/download/v20.0.3/electron-v20.0.3-linux-armv7l.zip duration=52.234s
  • building        target=snap arch=armv7l file=build/electron-github-api_1.0.0_armhf.snap
  • building        target=AppImage arch=armv7l file=build/electron.mylog-1.0.0-armv7l.AppImage
  • application Linux category is set to default "Utility"  reason=linux.category is not set and cannot map from macOS docs=https://www.electron.build/configuration/linux
  • default Electron icon is used  reason=application icon is not set
  • application Linux category is set to default "Utility"  reason=linux.category is not set and cannot map from macOS docs=https://www.electron.build/configuration/linux
  • downloading     url=https://github.com/electron-userland/electron-builder-binaries/releases/download/appimage-12.0.1/appimage-12.0.1.7z size=1.6 MB parts=1
  • downloading     url=https://github.com/electron-userland/electron-builder-binaries/releases/download/snap-template-4.0-1/snap-template-electron-4.0-1-armhf.tar.7z size=1.3 MB parts=1
  • downloaded      url=https://github.com/electron-userland/electron-builder-binaries/releases/download/appimage-12.0.1/appimage-12.0.1.7z duration=3.842s
  • downloaded      url=https://github.com/electron-userland/electron-builder-binaries/releases/download/snap-template-4.0-1/snap-template-electron-4.0-1-armhf.tar.7z duration=4.143s
  ⨯ cannot execute  cause=exit status 1
                    errorOut=/home/pi/.cache/electron-builder/appimage/appimage-12.0.1/linux-arm32/mksquashfs: Compressor "xz" is not supported!
    /home/pi/.cache/electron-builder/appimage/appimage-12.0.1/linux-arm32/mksquashfs: Compressors available:
    	gzip (default)
    
                    command=/home/pi/.cache/electron-builder/appimage/appimage-12.0.1/linux-arm32/mksquashfs /home/pi/.cache/electron-builder/snap/snap-template-electron-4.0-1-armhf/lib /home/pi/.cache/electron-builder/snap/snap-template-electron-4.0-1-armhf/desktop-gnome-specific.sh /home/pi/.cache/electron-builder/snap/snap-template-electron-4.0-1-armhf/desktop-common.sh /home/pi/.cache/electron-builder/snap/snap-template-electron-4.0-1-armhf/data-dir /home/pi/.cache/electron-builder/snap/snap-template-electron-4.0-1-armhf/usr /home/pi/.cache/electron-builder/snap/snap-template-electron-4.0-1-armhf/desktop-init.sh /home/pi/.cache/electron-builder/snap/snap-template-electron-4.0-1-armhf/gnome-platform /home/pi/.cache/electron-builder/snap/snap-template-electron-4.0-1-armhf/.DS_Store /tmp/work/Electron.MyLog.electron.builder.20220906123159/build/__snap-armhf/command.sh /tmp/work/Electron.MyLog.electron.builder.20220906123159/build/__snap-armhf/scripts /tmp/work/Electron.MyLog.electron.builder.20220906123159/build/__snap-armhf/meta /tmp/work/Electron.MyLog.electron.builder.20220906123159/build/linux-armv7l-unpacked/electron-github-api /tmp/work/Electron.MyLog.electron.builder.20220906123159/build/linux-armv7l-unpacked/resources /tmp/work/Electron.MyLog.electron.builder.20220906123159/build/linux-armv7l-unpacked/chrome_100_percent.pak /tmp/work/Electron.MyLog.electron.builder.20220906123159/build/linux-armv7l-unpacked/snapshot_blob.bin /tmp/work/Electron.MyLog.electron.builder.20220906123159/build/linux-armv7l-unpacked/chrome_200_percent.pak /tmp/work/Electron.MyLog.electron.builder.20220906123159/build/linux-armv7l-unpacked/chrome_crashpad_handler /tmp/work/Electron.MyLog.electron.builder.20220906123159/build/linux-armv7l-unpacked/v8_context_snapshot.bin /tmp/work/Electron.MyLog.electron.builder.20220906123159/build/linux-armv7l-unpacked/vk_swiftshader_icd.json /tmp/work/Electron.MyLog.electron.builder.20220906123159/build/linux-armv7l-unpacked/libvulkan.so.1 /tmp/work/Electron.MyLog.electron.builder.20220906123159/build/linux-armv7l-unpacked/libEGL.so /tmp/work/Electron.MyLog.electron.builder.20220906123159/build/linux-armv7l-unpacked/libvk_swiftshader.so /tmp/work/Electron.MyLog.electron.builder.20220906123159/build/linux-armv7l-unpacked/libGLESv2.so /tmp/work/Electron.MyLog.electron.builder.20220906123159/build/linux-armv7l-unpacked/libffmpeg.so /tmp/work/Electron.MyLog.electron.builder.20220906123159/build/linux-armv7l-unpacked/icudtl.dat /tmp/work/Electron.MyLog.electron.builder.20220906123159/build/linux-armv7l-unpacked/resources.pak /tmp/work/Electron.MyLog.electron.builder.20220906123159/build/linux-armv7l-unpacked/locales /tmp/work/Electron.MyLog.electron.builder.20220906123159/build/electron-github-api_1.0.0_armhf.snap -no-progress -quiet -noappend -comp xz -no-xattrs -no-fragments -all-root
                    workingDir=
```

### 実行時エラー

```
Uncaught (in promise) Error: Error invoking remote method 'loadDb': Error: ENOENT: no such file or directory, open 'src/db/mylog.db'
```

　ファイルないってさ。[file-patterns][]を参考にし、`package.json`の`files`を以下のように修正してから再ビルドしても同じだった。

```javascript
    "files": [
      "**/*"
    ],
```

　え、困るんだが。どうすればいいの？　なんでファイル参照エラーになるの？　ちゃんと指定したつもりだけど。どこか間違ってるかな？　わからん……。

　以下のように修正して再ビルドしても変わらなかった……。

```javascript
    "files": [
      "**/*",
      "src/db/mylog.db"
    ],
```

### 成功（`mylog.db`を手動で配置する）

　ビルドで作成された実行ファイル`electron.mylog-1.0.0-armv7l.AppImage`のディレクトリに`src/db/`ディレクトリを作成し、`mylog.db`ファイルをコピーすると成功した。

　アドレスをセットして再起動すると、投げモナボタンも表示された。画像ファイルもちゃんと含まれたっぽい。

　……つまり、なぜか`mylog.db`だけが含まれなかったということか。なぜそんなことに？　よくわからない。まあ動いたからいいや。

　ビルドされたファイルは84.2MiB。めちゃ重い。たぶんChromiumが丸ごと入っているのだろう。

## 失敗（ラズパイでwindowsビルドしようとした）

　`./node_modules/.bin/electron-builder --windows`を直接叩いた。やはり失敗した。

```sh
$ ./node_modules/.bin/electron-builder --windows
  • electron-builder  version=23.3.3 os=5.10.103-v7l+
  • loaded configuration  file=package.json ("build" field)
  • description is missed in the package.json  appPackageFile=/tmp/work/Electron.MyLog.electron.builder.20220906123159/package.json
  • writing effective config  file=build/builder-effective-config.yaml
  • packaging       platform=win32 arch=armv7l electron=20.0.3 appOutDir=build/win-armv7l-unpacked
  ⨯ cannot resolve https://github.com/electron/electron/releases/download/v20.0.3/electron-v20.0.3-win32-armv7l.zip: status code 404
github.com/develar/app-builder/pkg/download.(*Downloader).follow
	/Volumes/data/Documents/app-builder/pkg/download/downloader.go:237
github.com/develar/app-builder/pkg/download.(*Downloader).DownloadNoRetry
	/Volumes/data/Documents/app-builder/pkg/download/downloader.go:128
github.com/develar/app-builder/pkg/download.(*Downloader).Download
	/Volumes/data/Documents/app-builder/pkg/download/downloader.go:112
github.com/develar/app-builder/pkg/electron.(*ElectronDownloader).doDownload
	/Volumes/data/Documents/app-builder/pkg/electron/electronDownloader.go:192
github.com/develar/app-builder/pkg/electron.(*ElectronDownloader).Download
	/Volumes/data/Documents/app-builder/pkg/electron/electronDownloader.go:177
github.com/develar/app-builder/pkg/electron.downloadElectron.func1.1
	/Volumes/data/Documents/app-builder/pkg/electron/electronDownloader.go:73
github.com/develar/app-builder/pkg/util.MapAsyncConcurrency.func2
	/Volumes/data/Documents/app-builder/pkg/util/async.go:68
runtime.goexit
	/usr/local/Cellar/go/1.17/libexec/src/runtime/asm_arm.s:838  
  ⨯ /tmp/work/Electron.MyLog.electron.builder.20220906123159/node_modules/app-builder-bin/linux/arm/app-builder process failed ERR_ELECTRON_BUILDER_CANNOT_EXECUTE
Exit code:
1  failedTask=build stackTrace=Error: /tmp/work/Electron.MyLog.electron.builder.20220906123159/node_modules/app-builder-bin/linux/arm/app-builder process failed ERR_ELECTRON_BUILDER_CANNOT_EXECUTE
Exit code:
1
    at ChildProcess.<anonymous> (/tmp/work/Electron.MyLog.electron.builder.20220906123159/node_modules/builder-util/src/util.ts:250:14)
    at Object.onceWrapper (node:events:642:26)
    at ChildProcess.emit (node:events:527:28)
    at maybeClose (node:internal/child_process:1092:16)
    at Process.ChildProcess._handle.onexit (node:internal/child_process:302:5)
```

　たぶんフラグを付与せず`./node_modules/.bin/electron-builder`とすれば、自分の環境に応じた実行形式ファイルを生成してくれるのだろう。

　ということは、リリースファイルとして各OSのバイナリを配布するには、以下のようなことが必要ということでいいのかな？

1. Windows、Mac、Linuxの各OSマシンを用意する
1. 各マシンで`./node_modules/.bin/electron-builder`コマンドを実行する
1. 作成された実行形式ファイルをリリースファイルとして提供する

　それはキツイ。もう各自ソースコードからビルドしてもらいたい。そもそも84.2MiBって重すぎる。わざわざ実行ファイルにするメリットってなんだっけ？　Node.jsなどの実行環境を用意せずに済むことか。うーん、どっちがいいかな。とりあえず、基本はソースコード配布がよさそう。

