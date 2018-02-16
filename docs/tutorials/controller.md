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

以下はライフサイクルのデモです。 `Date.new()` を使って呼び出されたタイミングを出力しています。 `__construct` -&gt; `__init` -&gt; `__ready` の順番で呼び出されているのが分かるはずです。

<script async src="//jsfiddle.net/31au10ap/2/embed/js,html,result/"></script>

## コントローラの公開

JavaScriptではファイルを分割したりすると変数が読み込めなくなります。そのため、グローバルな変数に定義されるのですが、数が多くなると名前が重なって不具合につながったりします。そこで hifive では名前空間を分けつつ、コントローラを公開できます。

公開する際には `h5.core.expose` を使います。一つ目に公開したい変数をとりますが、その `__name` キーを使って外部公開します。例えば以下のように `AppController` を公開します。

```
$(function() {
  var AppController = {
    __name: 'Sample.AppController', // ここが肝です！
    __construct: function(context) {  
    },    
  };
  
  h5.core.controller('#container', AppController);
  h5.core.expose(AppController);
});
```

そうすると `Sample.AppController` としてコントローラにアクセスできるようになります。

```
$(function() {
  console.log(Sample.AppController.__name); // -> Sample.AppController
});
```

ドット（.）を使って名前空間を区切ることで、変数名のバッティングを防げるようになります。

### デモ

実際にコントローラを公開するデモを以下で試せます。

<script async src="//jsfiddle.net/xcrms08m/5/embed/js,html,result/"></script>

## コントローラの連携

一画面一コントローラで管理すると、新しい要素を追加した場合や別な画面でのコントローラの再利用性が悪化します。そこで画面を構成する要素によってコントローラを分割するのがお勧めです。

hifive ではコントローラ同士の連携をサポートしています。そのためには親コントローラを定義し、その中に子コントローラを配置します。そして親コントローラはイベントを購読しており、そのイベントを受け取って別なコントローラを呼び出します。子コントローラ同士で直接メッセージを送れませんので注意してください。

![コントローラの連携](/images/controller-relations.png)

そのため、大型なWebアプリケーションを開発する際にはまず全体をAppControllerといったコントローラで囲み、中の要素に応じて子コントローラを割り当てるのが良いでしょう。

### イベントを通知する子コントローラの定義

親コントローラが定義される **前** に子コントローラが定義されている必要があります。そのため、まず子コントローラを記述します。子コントローラにはイベントハンドリング（ #btn のクリックイベント）が定義されています。

子コントローラでは親コントローラに対してイベントを通知する際に this.trigger を使います。 this.trigger は一つ目にイベント名、二つ目にオプションとして渡したいデータを引数にします。

```js
$(function() {
  var Child1Controller = {
    __name: 'Child1Controller',
 
    '#btn click': function(context, $el) {
      console.log('#btn click');
      this.trigger('msg' ,{
        msg: 'Child1Controllerからのメッセージ'
      });
    }
  };
  h5.core.controller('#child1', Child1Controller);
  h5.core.expose(Child1Controller);
});
```

### イベントを受け取る子コントローラの定義

次にイベントを受け取る側の子コントローラを定義します。こちらはメソッドをそのまま定義するだけです。 `msg` が親コントローラから呼ばれるメソッドになります。

```js
$(function() {
  var Child2Controller = {
    __name: 'Child2Controller',
 
    __construct: function(context) {  
    },
        
    'msg': function(msg) {
      this.$find('.result').text(msg);
    }
  };
  h5.core.controller('#child2', Child2Controller);
  h5.core.expose(Child2Controller);
});
```

### 親コントローラの定義

イベントの通知と受け取りが定義できたら、親コントローラで二つを繋ぎます。 `_*Controller` としてコントローラを紐付ける必要があります。これでそのコントローラ以下で実行されるイベントを購読するようになります。

購読は `{rootElement} msg` といった形で `{rootElement}` を付けてください。元々オプションとして渡していたデータは一つ目の引数の `evArg` キーで受け取れます。

```
$(function() {
  var AppController = {
    __name: 'AppController',
 	_child1Controller: Child1Controller,
    _child2Controller: Child2Controller,
    
    __construct: function(context) {  
    },
    
    '{rootElement} msg': function(context) {
      var msg = context.evArg.msg;
      this._child2Controller.msg(msg);
    }
  };
  
  h5.core.controller('#container', AppController);
});
```

### デモ

実際にコントローラ連携するデモを以下で試せます。

<script async src="//jsfiddle.net/xcrms08m/embed/js,html,result/"></script>

