# hifive入門

## インストール

hifiveをインストールする方法は主に3種類用意されています。

### Bowerを使った方法

下記のコマンドでインストールできます。

```sh
$ bower install hifive
```

### Zipダウンロード

[hifive公式サイト](https://www.htmlhifive.com/conts/web/view/download/hifive)よりダウンロードできます。

### CDN版

CDNでホスティングされているJavaScript/CSSファイルを読み込む方法です。本サービスは予告なく改変、停止する場合がありますので本番利用は控えてください。

```html
<link href="//code.htmlhifive.com/h5.css" rel="stylesheet">

<script src="//code.htmlhifive.com/ejs-h5mod.js"></script>
<script src="//code.htmlhifive.com/h5.js"></script>
```

開発用メッセージを確認できるのは下記になります。

```html
<link href="//code.htmlhifive.com/h5.css" rel="stylesheet">

<script src="//code.htmlhifive.com/ejs-h5mod.js"></script>
<script src="//code.htmlhifive.com/h5.dev.js"></script>
```

## はじめてみる

[こちらのZipファイル](/base.zip)をダウンロードしてください。内容は以下のようになっています。

```
$ tree .
.
├── index.html
└── js
    └── app.js

1 directory, 2 files
```

## HTMLのテンプレート

ごく基本的なHTMLテンプレートになります。

```html
<!doctype html>
<html>
  <head>
    <meta charset="UTF-8">
    <!-- hifiveスタイルシートの読み込み -->
    <link href="//code.htmlhifive.com/h5.css" rel="stylesheet">
    <!-- jQueryの読み込み。IE8以下の場合は1.xを、それ以外の場合は2.xを読み込む -->
    <!--[if lt IE 9]>
    <script src="//code.jquery.com/jquery-1.11.1.min.js"></script>
    <![endif]-->
    <!--[if gte IE 9]><!-->
    <script src="//code.jquery.com/jquery-3.2.1.min.js"></script>
    <!--<![endif]-->
    <!-- hifiveフレームワークの読み込み -->
    <script src="//code.htmlhifive.com/ejs-h5mod.js"></script>
    <script src="//code.htmlhifive.com/h5.dev.js"></script>
    <script src="/js/app.js"></script>
  </head>
  <body>
    <div id="container">
      <input type="button" id="btn" value="ボタンを押してください！" />
    </div>
  </body>
</html>
```

JavaScriptファイルは次のようになっています。

```
$(() {
  // hifiveのコントローラ。JavaScriptのオブジェクトとして定義します
  const helloWorldController = {
    // コントローラ名。 __name で定義します。
    __name: 'HelloWorldController',
    // イベント。 "[CSSセレクタ] [イベント]" で呼び出されます
    '#btn click'() {
      alert('hifiveの世界へようこそ！');
    }
  };
  // JavaScriptをhifiveのコントローラ化します
  h5.core.controller('#container', helloWorldController);
});
```

`h5.core.controller` では第一引数にコントローラをバインドさせる要素を指定します（今回の場合は #container 以下で利用します）。

これを簡易的な HTTPサーバ化で動作させます。トップページにアクセスすると、以下のようにボタンが表示されるはずです。

そしてボタンを押すと **hifiveの世界へようこそ！** というアラートが表示されます。

これが hifive の基本です。

## JSFiddleで体験する

上記内容はこちらでも試せます。

<script async src="//jsfiddle.net/qfun9osL/4/embed/result,js,html/"></script>


# 次のステップ

次に[hifiveについて、また要件について学びましょう](./about)。

