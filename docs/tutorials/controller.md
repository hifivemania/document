# コントローラ

コントローラは主に以下の機能を提供します。

- イベントのハンドリング
- 使用するロジック、ビューの宣言
- ライフサイクルイベントの定義
- DOM操作

## コントローラの生成（コントローラ化）

コントローラは最初、JavaScriptのオブジェクトとして定義されます。

```javascript
var sampleController = {
  __name: 'SampleController',
   
  '#btn click': function(context, $el) {
    alert('ボタンがクリックされました。');
  }
};
```

このオブジェクトを hifive のコントローラにするのが `h5.core.controller` メソッドになります。

```javascript
h5.core.controller(selector, controller, params);
```

この時渡す引数は以下の通りです。

- **selector**  
コントローラをバインドする対象になるHTML要素。
- **controller**  
hifive のコントローラになるJavaScriptオブジェクト
- **params**  
コントローラ初期化時に渡す値

初期化パラメータを渡した際の動作は以下のデモで確認できます。 `{hello: 'hifive'}` というパラメータが `args` というキーで取得できます。

<script async src="//jsfiddle.net/3s9p08kp/3/embed/result,js,html/"></script>

## コントローラのライフサイクルイベント

コントローラを初期化する際には幾つかのイベントが呼ばれます。

- **__construct**  
コントローラ化が終わった段階で呼ばれます。ビューテンプレートの読み込みは終わっていません。
- **__init**  
コントローラ化とビューテンプレートの読み込みは完了しています。イベントハンドラはまだバインドされていません。
- **__ready**  
コントローラ化とビューテンプレートの読み込み、そしてイベントのバインドまで完了した状態で呼ばれます。

`__init` の段階ではビューテンプレートにアクセスすることが可能です。しかしバインドは終わっていないのでイベント処理は呼び出せません。 `__ready` はすべて完了していますので、多くの場合は `__ready` を使えば問題ありません。


