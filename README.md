# npm publish

2018/11/14

kzhrk <info@kzhrk.com>

## 最近publishしたもの

- [ariaset](https://www.npmjs.com/package/ariaset)

- [vue-kindly-select](https://www.npmjs.com/package/vue-kindly-select)

- [wrap-with-tag](https://www.npmjs.com/package/wrap-with-tag)

## npmへの公開方法

### アカウント作成

npmのサインアップページからアカウントを作成する。

https://www.npmjs.com/signup

Node.jsがすでにインストール済であれば、`npm adduser`コマンドでログインする。  
登録したusername, password, emailを聞かれるのでそれそれ入力する。

```
$ npm adduser # username, password, email
```

### ファイル作成

適当な空ディレクトリ内に下記のファイルを作成する。  
何も設定しないとディレクトリ内のファイルは一部（node_modulesなど）を除いてすべてnpmに公開されてしまうので注意する。

- package.json
- index.js

npmの公開時、package.jsonのname, versionプロパティは必須項目になるので埋めておく。  
nameが公開されるpackage名になるので、自前で@usernameといったネームスペースを切って適当なpackage名をつける。  

```js
{
  "name": "@username/publish-test",
  "main": "./index.js",
  "version": "0.0.1"
}
```

### Publish

先程のファイルを作成したディレクトリで`npm publish`コマンドを叩くとnpmにpackageが公開される。  
`npm info package名`コマンドで公開されたpackageの情報が確認できる。  

```
$ npm publish
$ npm info @username/publish-test
```

`npm info`コマンドでpackageの最新情報を確認できるのでpackageの公開確認以外でも、たとえばプロジェクトで使用するpackageのステータスが確認できるので覚えておくと便利。

## 公開時の注意点

### package.jsonのプロパティは極力埋める

公開されたpackageの詳細ページに掲載されている情報はpackage.jsonを元に作成されているため、使用者に情報を伝えるためpackage.jsonのプロパティは極力埋めた方がよい。  
リモートリポジトリを紐づけてからnpm init -yするとbugs、homepage、repositoryなどが自動で埋められるため、バージョン管理をする場合、リポジトリの紐づけが完了してから`npm init -y`すると便利。  
package.jsonのプロパティの順番をソートしてくれるnpmとして`fixpack`が存在する。これをグローバルインストールして`fixpack`コマンドを実行するか、`npx fixpack`コマンドを実行するとpackage.jsonのプロパティ名が整理される。また、package.jsonで不足しているプロパティがあればerrorやwarningを出してくれる。

### package.jsonのプロパティ

| key | type | description |
|:----|:-----|:------------|
| name | string | packageの名前。公開URLにも使用される |
| version | string | packageのバージョン |
| description | string | 検索一覧、ユーザのpackagesページに表示される説明文 |
| homepage | string | packageのホームページ。GitHubのREADMEや、プロジェクトページのURL |
| bugs | object | バグ報告先。GitHubaのissuesページ、ユーザのemailが定義される。<br>{ url, email } |
| license | string | ライセンスの宣言 |
| author | string or object | 作者の情報<br>name &lt;email&gt; (url) or { name, email, url } |
| files | array | npmに公開するファイル/ディレクトリの配列 |
| main | string | requireやimportで呼ばれる際のファイル。CLIのようなpackageの場合、不要 |
| bin | string or object | CLIで実行されるJSファイル。nameプロパティがコマンド名になるが、Objectで指定した場合はキー名がコマンド名になる（例: [gulp-cli => gulp](https://github.com/gulpjs/gulp-cli/blob/master/package.json#L15)） |
| repository | object | バージョン管理の情報 ({type, url}) |
| private | boolean | trueを指定すると、npm publishが実行されない |

![package list](https://raw.githubusercontent.com/kzhrk-slide/npm-publish/master/img/fig1.png)

![package detail](https://raw.githubusercontent.com/kzhrk-slide/npm-publish/master/img/fig2.png)

### LICENSE

LICENSEファイルを追加する。

[wikipedia 主要なライセンス](https://ja.wikipedia.org/wiki/%E3%82%AA%E3%83%BC%E3%83%97%E3%83%B3%E3%82%BD%E3%83%BC%E3%82%B9%E3%83%A9%E3%82%A4%E3%82%BB%E3%83%B3%E3%82%B9#%E4%B8%BB%E8%A6%81%E3%81%AA%E3%83%A9%E3%82%A4%E3%82%BB%E3%83%B3%E3%82%B9)

### 不要なファイルは公開しない

package公開時、不要なファイルは極力公開しないようにしたい。  
`npm publish`時、一部のファイルは自動で公開/除外が行われるようになっている。  
自動で公開される主なものとしては、package.json、README、CHANGESLOG、LICENSE、mainプロパティのファイルなど。  
自動で除外される主なものとしては、.git、.svn、.npmrc、node_modulesなど。  
詳細はnpmのpackage.jsonのドキュメントに書かれている。

[files](https://docs.npmjs.com/files/package.json#files)

package公開時、.gitignoreがある場合は、.gitignoreを元にファイルが除外される。  
Gitのバージョン管理からは外している（.gitignoreに記載がある）ファイルをnpm公開する必要がある場合（たとえばbuild後のjsファイルなど）、以下の2つの対応方法がある。

- .npmignoreファイルでブラックリストを作成して、`npm publish`時の除外ファイルを定義する
- package.jsonのfilesプロパティでホワイトリストを作成し、公開ファイルを定義する

個人的には,gitignoreと.npmignoreの2重管理を避けるため、package.jsonのfilesプロパティで公開したいファイルのホワイトリストを作成する方が妥当だと考えている。

### READMEにバッジを付ける

- ビルド結果（TravisCI, CircleCI, wercker）
- テストのcoverage（coveralls）
- npmのバージョン情報
- ダウンロード数
- ライセンス情報
- GitHubの情報（issues, PR, starts）

### READMEに記載する情報

```
# Name

Overview

## Description

## Demo

## Install

## Usage

## Licence

[MIT](https://github.com/kzhrk/ariaset/blob/master/LICENCE)

## Author

[kzhrk](https://github.com/kzhrk)
```

## npmの更新

npmコマンドにはpackageのバージョン管理用の`npm version`コマンドが存在する。  

- package.jsonのversionをpatch, minor, majorに合わせてアップデートする
- 該当するバージョンのtagの自動作成する

たとえば、v0.0.1のpackageを各コマンドで更新すると以下のようになる。

```
$ npm version patch
v0.0.2
$ npm version minor
v0.1.0
$ npm version major
v1.0.0

$ git tag
v0.0.2
v0.1.0
v1.0.0
```

公開されたnpmの特定バージョンのpackageは`npm unpublish pkg@version`コマンドで非公開にすることができるが、特定バージョンを`npm unpublish`したとしても、同じバージョン番号のpackageを再度publishすることはできないのでバージョンアップをする際には注意が必要。  
`npm unpublish`コマンドは公式的には[deprecate](https://docs.npmjs.com/cli/unpublish#warning)になっている。

## npmを公開して得られたこと

- Gitコマンドの習得
- Gitブランチモデルの習得
- CIによるテストの自動実行
- Unit TestやE2E Testの習得
- 英語力（？）
