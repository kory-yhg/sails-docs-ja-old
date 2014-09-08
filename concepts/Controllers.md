#コントローラ
###概要

コントローラ（MVCモデルでいうところのC）はSailsアプリケーションがWebブラウザやモバイルアプリケーションなどWebサーバと通信できる各種の端末から受けるリクエストを処理する基礎的なオブジェクトです。コントローラはしばしばModelとViewの中間に立ちます。また、多くのケースではアプリケーションお大半のビジネスロジックはコントローラの中に記述されます。


###アクション

コントローラはアクションと呼ばれるメソッドの集合から構成されます。アクションはあなたのアプリケーションのルーティングから転送されます。つまりクライアントが特定のルートに対してリクエストを行った時にはアクションに転送されそこで何ら家のビジネスロジックを実行した上で（大抵の場合は）レスポンスを生成します。例えば「/helloのルートにGETアクセスがなされた場合以下の様なメソッドが呼び出す」ということが出来ます。


```
function (req, res) {
  return res.send("Hi there!");
}
```

このためブラウザが/helloのURLを呼び出した時はいつでも“Hi there”とメッセージが表示されるようになります。


###コントローラはどこで定義されますか

コントローラはapi/controllersフォルダの中に定義されます。このフォルダにはどんなファイルでも置いていいですがSailsのコントローラとして読み込まれるためにはファイル名は必ずController.jsで終わらなければなりません。慣習に則り、Sailsのコントローラはパスカルケースで命名されます。そのためすべての単語（最初の単語も含め）は大文字で始まらなければなりません。例えばUserController.js, MyController.js やSomeGreatBigController.js はすべて正しいフォーマットのパスカルケース命名です。

もしもコントローラをグループ分けする必要がある場合api/controllersの中にサブフォルダーを作成することが出来ます。ただし、サブフォルダー名はルーティングの仮定でコントローラの名前の一部として取り扱われます。
(詳細は下記の"Routing"セクションをご覧ください。)

###コントローラはどんなフォルダですか

コントローラフォルダはアクション名をキーとしたJavascriptオブジェクトとして定義され、そのオブジェクトのキーの中身にアクションが入ります。
いかにコントローラファイルの簡単な例を挙げます。

```
module.exports = {
  hi: function (req, res) {
    return res.send("Hi there!");
  },
  bye: function (req, res) {
    return res.redirect("http://www.sayonara.com");
  }
};
```

このコントローラには2つのアクションが定義されいます。アクション"hi"はテキストのレスポンスを返し、アクション"bye"は別のWebサイトへのリダイレクトを返します。リクエストオブジェクトとレスオブジェクトはExpress.jsでWebアプリケーションを書いたことのある人は誰でも馴染みあるところです。
これはSailsはルーティングにあたってExpressを裏方で利用しているという設計上の利用です。Expressのミドルウエアとは違いSailsのコントローラは常にリクエストチェーンの最後になります。つまり、常に何らかのレスポンスないしエラーを返さなければならないということです。アクションメソッドの中で別の方針を使うことは可能ですが、この方針をとって構わない時ににはこの方針を取ることを強くおすすめします。


###ルーティング

デフォルトで、Sailsはそれぞれのの中にある各コントローラにコントローラに対してblueprint(訳注：CSSフレームワークの方ではありません)のルートを設定します。そのため/:controllerIdentity/:nameOfActionのルートは指定されたコントローラの指定されたアクションを呼び出します。上のセクションのコントローラがapi/controllers/SayController.jsに用意された場合、/say/hiと/say/byeルートが起動時にデフォルトで有効になります。もしコントローラが/weというサブフォルダーの中に保存されていた場合はルートは/we/say/hiと/we/say/byeとなります。Sailsの提供する自動ルーティング生成機能のさらなる詳細に関してはblueprintsのドキュメンテーションをご覧ください。

これらのデフォルトのルーティングに加え、Sailsではconfig/routes.jsファイルを使うことによりユーザーが手動でルートをバインドすることが出来ます。以下の例がそのようなルートファイルを利用すべき時の例です。


* 同一のパスをHTTPメソッド（verbともいいます）にしたがって複数のアクションに分けて処理したいとき。前述のBlueprintのルートはGET,POST,PUT,DELETEなどを含めすべてのメソッドに対してルートがあっていれば処理します。

* カスタムのURLでアクションを実行したいとき。(例 PUT /login, POST /signupやGET /:usernameのようなvanity URL )

* ルートがどのように処理されるかを記述した追加的設定をしたいとき。（例　特別なCORS設定）

config/routes.jsでコントローラのアクションに対して手動でルートを設定したいときはHTTPメソッドとパス（
すなわちルーティングアドレス）をキーに、コントローラ名＋アクション名（すなわちルーティングターゲット）を値にして設定すれば出来ます。

例えば以下の手動のルーティングは/make/a/sandwichにPOSTリクエストを受け取った時にapi/controllers/SandwichController.jsのmakeIt() アクションを呼び出すものです。


```
'POST /make/a/sandwich': 'SandwichController.makeIt'
```

###Note:
コントローラファイルがサブディレクトリに保存された場合は以下のようにサブフォルダ名がコントローラ指定の一分になります。

```
'/do/homework': 'stuff/things/HomeworkController.do'
```

この設定では/do/homeworkが呼び出された時はいつでもapi/controllers/stuff/things/HomeworkController.js のdo()アクションが呼び出されることになります。

手動ルーティングに関しての完全な記述はこのドキュメントの範疇外になりますのでルーティングのドキュメンテーションを御覧ください。


###「軽量な」コントローラ

ほとんどのMVCフレームワークはコントローラを「軽量に」保つことを進めており、Sailsもその例外ではありませんが、（つまりSailsのコントローラ可能な限りシンプルにしておくべきです）ここで「なぜそうなのか」を知っておくのもいいでしょう。

コントローラのコートはトリガーやイベント本質的に独立しています。Sailsのようなバックエンドフレームワークでは、イベントは殆どの場合においてインカミングのリクエストです。そのためもしいくつかのコードを一つのコントローラアクションに書いてしまった場合リクエストのコンテキスト（つまりreqオブジェクトとresオブジェクト）ごとに独立しているべきである各コードのスコープとは別のものになってしまいます。それは一時的には何とかなりますが、あとで「ちょっと違うアクション」やコマンドラインから呼び出したいときに困ったことになります。

そのため「軽量なコントローラ」の目指すものは再利用可能なコードが特定のスコープともつれ合ってしまっている状況を解消することです。Sailsにおいてこれを実現する方法はいろいろありますがコードをコントローラから独立させる最も一般的な方法は以下のとおりです。

*特定のモデルに対して特定のタスクを実行するためにカプセリングされたカスタムのモデルメソッドを書く。


* 特定の用途に限定して使われるタスクを実行するためにカプセリングされたファンクションをサービスとして書く。

* もし複数のアプリケーションを通じて有用であるコードを見つけた場合は（時間があれば）nodeのモジュールにしましょう。そうすればあなたの組織を通じて利用することが出来るようになりますし、更に良いこととしてはもしそれをnpmとしてオープンソースで公開すれば誰かが使ってくれたり、メンテナンスを手伝ってくれたりします。

###コントローラの生成

以下のコマンドを入力することでSailのコマンドラインツールから簡単にコントローラを作成することが出来ます。

```
$ sails generate controller <controller name> [action names separated by spaces...]
```

例えば以下のコマンドを実行すると:

```
$ sails generate controller comment create destroy tag like
info: Generated a new controller `comment` at api/controllers/CommentController.js!
```

Sailsは以下のコントローラをapi/controllers/CommentController.jsとして作成します。:

```
/**
 * CommentController.js
 *
 * @description :: Server-side logic for managing comments.
 */

module.exports = {

  /**
   * CommentController.create()
   */
  create: function (req, res) {
    return res.json({
      todo: 'Not implemented yet!'
    });
  },

  /**
   * CommentController.destroy()
   */
  destroy: function (req, res) {
    return res.json({
      todo: 'Not implemented yet!'
    });
  },

  /**
   * CommentController.tag()
   */
  tag: function (req, res) {
    return res.json({
      todo: 'Not implemented yet!'
    });
  },

  /**
   * CommentController.like()
   */
  like: function (req, res) {
    return res.json({
      todo: 'Not implemented yet!'
    });
  }
};
```
