#モデル　Waterline: SQL/NoSQLのマッパー(ORM/ODM)

SailsはWaterlineと呼ばれるデータベース非依存のORM/ODMを内蔵しており一つや複数のデータベースにアクセスする手順をおどろくほど簡単にすることが出来ます。Waterlineはデータベースの上層に抽象化レイヤーを存在させることでベンダー依存の実装コードを書くことなく簡単にデータを参照し変更することが出来ます。


###データベース非依存

Postgres,Oracle, MySQLのようなスキーマ式データベースではモデルはテーブルに置き換えられます。MongoDBではMongoの「コレクション」に置き換えられます。Redisではキーと値のペアに置き換えられます。それぞれのデータベースはその「方言」を持ち、時にはデータベースに接続するために個別のネイティブモジュールをインストールする必要があります。
これにはかなりのオーバーヘッドが生じ、不安定なベンダー依存のコードを蓄積させることになります。（例：あなたのアプリケーションがある程度まとまったSQLクエリを利用している場合、MongoやRedis、vice versaへ後で変更することは困難になるでしょう。）

Waterlineの文法はそれらすべての上層に位置しデータを作成したり検索したり更新したり削除したりというビジネスロジックに焦点を当てたものです。どんなデータベースに接続する場合にも文法は全く一緒です。さらに言えばWaterlineは異なるデータベース上にある複数のモデル間でデータリレーションを.populate()することすら出来ます。つまりこれはアプリケーションのモデルをMongoからPostgresに、MySQLに、Redisに移行し、もとに戻ることすら出来るのです（しかも一切のコード改変なしに！）
もしも低レイヤーのデータベース依存の関数を使いたいときはWaterlineはモデルの下層にあるそれらのデータベースドライバに直接アクセスすることも出来ます。(.query()と.native().を御覧ください。)

###シナリオ

モバイルアプリを持つeコマースアプリケーションを構築することを考えてみましょう。ユーザはカテゴリーや検索で商品を閲覧することが出来、商品を購入することが出来ます。それだけです！アプリケーションのいくつかの部分は非常にシンプルです。アプリケーションはAPIを使ったログイン、サインアップ、購入・決済、パスワードの変更などが出来ます。しかしその他にもいくつかの手間がかかるありふれた機能がロードマップ上に隠れているはずです。おそらく。


###フレキシビリティ

あなたはビジネス部門にどのデータベースを聞くでしょう:

```
"データなんとかって、なんだよ。そう焦らせないでくれよ、間違ったのを選びたくないな。情シスに聞くから先に進めていてくれよ。"
```

WebアプリケーションやAPIに一つのデータベースを選ぶというやり方は多くの利用例において取ることが出来ません。多くの場合において既存の複数のデータセットとの互換性を持たなければなりませんし、場合によってはパフォーマンス上の原因から複数のデータベースを利用しなければならないことすら有ります。

Sailsはデフォルトでsails-diskを利用するので、ローカルの一時ファイルを使ったデータベースを設定なしで利用することが出来ます。実際に使うデータベースに切り替える用意ができた時には（あるいはそれが何なのかみんながわかった時には）アプリケーションの中の接続設定を書き換えるだけですみます。


###互換性

プロジェクトの管理者があなたのところに来て言いました。:

```
"ああ、ところで商品データは実は僕らのPOSシステムの中にすでにあるんだよ。多分何かのERPだと思うんだけど、DB2っていうやつかな。まあ、なんとかなるよね。簡単だろ？"
```

多くの業務用アプリケーションは既存のデータベースと統合しなければなりません。ラッキーなケースにおいてはデータのマイグレーションを一度行うだけで済むでしょう。しかし、多くの場合においては既存のデータは他のアプリケーションから依然として編集されています。アプリケーションを開発するにあたって複数のレガシーなシステムや別の場所にあるデータセットからのデータを結合させる必要があるかも知れません。このデータベースは世界中の離れた５地点にあることだってあるのです。
あるデータベースはMySQLに保管されたリレーショナルデータベースかも知れませんし、またあるほかのものはクラウド上に設置されたMongoやRedisのデータコレクションかも知れません。
｀

Sails/Waterline lets you hook up different models to different datastores; locally or anywhere on the internet. You can build a User model that maps to a custom MySQL table in a legacy database (with weird crazy column names). Same thing for a Product model that maps to a table in DB2, or an Order model that maps to a MongoDB collection. Best of all, you can .populate() across these different datastores and adapters, so if you configure a model to live in a different database, your controller/model code doesn't need to change (note that you will need to migrate any important production data manually)

###Performance

You're sitting in front of your laptop late at night, and you realize:

```
"How can I do keyword search? The product data doesn't have any keywords, and the business wants search results ranked based on n-gram word sequences. Also I have no idea how this recommendation engine is going to work. Also if I hear the words big data one more time tonight I'm quitting and going back to work at the coffee shop."
```

So what about the "big data"? Normally when you hear bloggers and analyst use that buzzword, you think of data mining and business intelligence. You might imagine a process that pulls data from multiple sources, processes/indexes/analyzes it, then writes that extracted information somewhere else and either keeps the original data or throws it away.

However, there are some much more common challenges that lend themselves to the same sort of indexing/analysis needs; features like "driving-direction-closeness" search, or a recommendation engine for related products. Fortunately, a number of databases simplify specific big-data use cases (for instance MongoDB provides geospatial indexing, and ElasticSearch provides excellent support for indexing data for full-text search).

Using databases in the way they're intended affords tremendous performance benefits, particularly when it comes to complex report queries, searching (which is really just customized sorting), and NLP/machine learning. Certain databases are very good at answering traditional relational business queries, while others are better suited for map/reduce-style processing of data, with optimizations/trade-offs for blazing-fast read/writes. This consideration is especially important as your app's user-base scales.

###Adapters

Like most MVC frameworks, Sails supports multiple databases. That means the syntax to query and manipulate our data is always the same, whether we're using MongoDB, MySQL, or any other supported database.

Waterline builds on this flexibility with its concept of adapters. An adapter is a bit of code that maps methods like find() and create() to a lower-level syntax like SELECT * FROM and INSERT INTO. The Sails core team maintains open-source adapters for a handful of the most popular databases, and a wealth of community adapters are also available.

Custom Waterline adapters are actually pretty simple to build, and can make for more maintainable integrations; anything from a proprietary enterprise system, to an open API like LinkedIn, to a cache or traditional database.

###Connections

A connection represents a particular database configuration. This configuration object includes an adapter to use, as well as information like the host, port, username, password, and so forth. Connections are defined in config/connections.js.

```
// in config/connections.js
// ...
{
  adapter: 'sails-mysql',
  host: 'localhost',
  port: 3306,
  user: 'root',
  password: 'g3tInCr4zee&stUfF'
}
// ...
```

The default database connection for a Sails app is located in the base model configuration (config/models.js), but it can also be overriden on a per-model basis by specifying a connection.

###Analogy

Imagine a file cabinet full of completed pen-and-ink forms. All of the forms have the same fields (e.g. "name", "birthdate", "maritalStatus"), but for each form, the values written in the fields vary. For example, one form might contain "Lara", "2000-03-16T21:16:15.127Z", "single", while another form contains "Larry", "1974-01-16T21:16:15.127Z", "married".

Now imagine you're running a hotdog business. If you were very organized, you might set up your file cabinets as follows:

* Employee (contains your employee records)
    * fullName
    * hourlyWage
    * phoneNumber
* Location (contains a record for each location you operate)
    * streetAddress
    * city
    * state
    * zipcode
    * purchases
        * a list of all the purchases made at this location
    * manager
        * the employee who manages this location
* Purchase (contains a record for each purchase made by one of your customers)
    * madeAtLocation
    * productsPurchased
    * createdAt
* Product (contains a record for each of your various product offerings)
    * nameOnMenu
    * price
    * numCalories
    * percentRealMeat
    * availableAt
        * a list of the locations where this product offering is available.
In your Sails app, a model is like one of the file cabinets. It contains records, which are like the forms. Attributes are like the fields in each form.

###Notes

This documentation on models is not applicable if you are overriding the built-in ORM, Waterline. In that case, your models will follow whatever convention you set up, on top of whatever ORM library you're using (e.g. Mongoose.)

##アソシエーション

このセクションは翻訳時に移動しました。

##アトリビュート

###概要

モデルアトリビュートはモデルにおける基本的な情報です。例えばPersonと名付けられたモデルではfirstName, lastName, phoneNumber, age, birthDateやemailAddressのようなアトリビュートがあります。

```
TODO: address sql vs. no sql and stuff like: """ In most cases, this data is homogenous, meaning each record has the same attributes, """
```


###アトリビュート・オプション

これらのオプションを利用することでモデルアトリビュートに各種の制約や拡張を付加することが出来ます。

type
どんな型のデータがモデルアトリビュートに入るかを指定するもので以下のいずれかのものです:

* string
* text
* integer
* float
* date
* datetime
* boolean
* binary
* array
* json

defaultsTo
レコードが作成される時にデータが入っていなかった場合defaultsToで指定した値を入れてレコードを作成します。


```
attributes: {
  phoneNumber: {
    type: 'string',
    defaultsTo: '111-222-3333'
  }
}
```

autoIncrement
指定したアトリビュートをオートインクリメントに指定します。レコードが作成される時にこの値が指定されていなければ最終のデータにインクリメントする形で値を生成します。
備考：autoIncrementに指定するアトリビュートのデータ型はintegerでなければなりません。また、各データストアによってサポートの度合いは異なります。例えばMySQLで1つのテーブルに複数のオートインクリメントは許可されていません。

```
attributes: {
  placeInLine: {
    type: 'integer',
    autoIncrement: true
  }
}
```

unique
指定されたアトリビュートに関して同じ値をも複数のレコードが存在しないことを保証します。これはアダプタレベルでの制約ですので多くのケースでは下部のデータベースレイヤーでこれを指定したアトリビュートには主キー制約がかかります。

```
attributes: {
  username: {
    type: 'string',
    unique: true
  }
}
```

attributes: { email: { type: 'string', index: true } } ``` -->
（訳注：編集ミス？）

primaryKey
このキーをレコードの主キーとして利用します。たった一つのアトリビュートのみがprimaryKeyになりえます。
備考：autoPKをfalseにしない限りこのオプションは動作しません。

```
attributes: {
  uuid: {
    type: 'string',
    primaryKey: true,
    required: true
  }
}
```

enum
ホワイトリストとして渡した値のうちいずれかしか保存することが出来なくする特殊なオプションです。

```
attributes: {
  state: {
    type: 'string',
    enum: ['pending', 'approved', 'denied']
  }
}
```

size
アダプタ側でサポートされていればアトリビュートのサイズを指定するために利用できます。例えばMySQLではMySQLでのデータ型varchar(n)を指定する際のnで指定することが出来ます。

```
attributes: {
  name: {
    type: 'string',
    size: 24
  }
}
```

columnName
アトリビュートの設定の中でcolumnNameを指定することでSails（Waterline）が設定された接続先（つまりデータベース）でそのアトリビュートを保存する際に利用するカラム名を指定することが出来ます。これはSQLに限定された話でなくMongoDBのフィールドなどにも利用できることをご留意ください。
columnNameプロパティは元々、既存のデータベースを利用する際に作られたのですが、いくつかのアプリケーションでテータベースを共有するときやデータスキーマを変更する権限がないときにもこの機能は便利です。

モデルのnumberOfWheelsアトリビュートをnumber_of_round_rotating_thingsに保管するには:

```
// モデルの中のアトリビュートで:
  // ...
  numberOfWheels: {
    type: 'integer',
    columnName: 'number_of_round_rotating_things'
  }
  // ...
```

それではもっと完璧で実用的な例を挙げましょう。

以下の様なUserというモデルがあったとします。

```
// api/models/User.js
module.exports = {
  connection: 'shinyNewMySQLDatabase',
  attributes: {
    name: 'string',
    password: 'string',
    email: {
      type: 'email',
      unique: true
    }
  }
};
```

これでうまくいくいきます、しかしアプリケーションにおけるユーザデータを格納するはずの既存のデータベースを使う代わりに：


```
// config/connections.js
module.exports = {
  // ...

  // Existing users are in here!
  rustyOldMySQLDatabase: {
    adapter: 'sails-mysql',
    user: 'bofh',
    host: 'db.eleven.sameness.foo',
    password: 'Gh19R!?had9gzQ#Q#Q#%AdsghaDABAMR>##G<ADMBOVRH@)$(HTOADG!GNADSGADSGNBI@(',
    database: 'jonas'
  },
  // ...
};
```

このようなour_usersというテーブルが古いMySQLデータベースにあったとします。:

|the_primary_key|email_address|full_name|seriously_encrypted_password|
|---|---|---|
|7	|mike@sameness.foo	|Mike McNeil	|ranchdressing|
|14	|nick@sameness.foo	|Nick Crumrine	|thousandisland|

この形式をSailsから使うためにUserモデルを以下のように編集する必要があります。:

```
// api/models/User.js
module.exports = {
  connection: 'rustyOldMySQLDatabase',
  tableName: 'our_users',
  attributes: {
    id: {
      type: 'integer',
      unique: true,
      primaryKey: true,
      columnName: 'the_primary_key'
    },
    name: {
      type: 'string',
      columnName: 'full_name'
    },
    password: {
      type: 'string',
      columnName: 'seriously_encrypted_password'
    },
    email: {
      type: 'email',
      unique: true,
      columnName: 'email_address'
    }
  }
};
```

```
この例で我々はtableNameも使っていることがお分かりになるでしょう。これでデータを格納するテーブル名を指定することが出来ます。
```

##ライフサイクルコールバック
###概要

Sailsは特定の処理の前後に呼び出される便利なコールバックを用意しています。例えば我々はAccuntモデルに追加したり更新したりする前にパスワードの暗号化を自動で行ったりします。その他の例としてはもでるProjectのnameアトリビュートが更新された時に自動でURLのスラグを再生成するということが挙げられます。


###create時のコールバック

* beforeValidate: fn(values, cb)
* afterValidate: fn(values, cb)
* beforeCreate: fn(values, cb)
* afterCreate: fn(newlyInsertedRecord, cb)

###update時のコールバック

* beforeValidate: fn(valuesToUpdate, cb)
* afterValidate: fn(valuesToUpdate, cb)
* beforeUpdate: fn(valuesToUpdate, cb)
* afterUpdate: fn(updatedRecord, cb)

###destroy時のコールバック

beforeDestroy: fn(criteria, cb)
afterDestroy: fn(destroyedRecords, cb)

###例

もしパスワードを保存前に暗号化したいときはbeforeCreateコールバックを使います。

```
var bcrypt = require('bcrypt');

module.exports = {

  attributes: {

    username: {
      type: 'string',
      required: true
    },

    password: {
      type: 'string',
      minLength: 6,
      required: true,
      columnName: 'encrypted_password'
    }

  },


  // コールバック
  beforeCreate: function (values, cb) {

    // パスワードを暗号化
    bcrypt.hash(values.password, 10, function(err, hash) {
      if(err) return cb(err);
      values.password = hash;
      //エラーを返す引数でcd()をコールします。これはいくつかの条件がFailした時に処理全体をキャンセルすることが出来、便利です。
      cb();
    });
  }
};
```

##モデル
モデルは構造化されたデータの集合を表し、通常はデータベースの中のひとつのテーブルまたはコレクションを含みます。モデルは通常api/models/フォルダの中にファイルを作成することで定義します。

Sublimeで開いたSails（Waterline）のモデルファイル。screenshot of a Waterline/Sails model in Sublime Text 2

###モデルを使う

モデルはコントローラ、ポリシー、サービス、レスポンス、テスト及びカスタムモデルからアクセス可能です。モデルにはいくつものメソッドが自動で用意されておりそのうち最も大切なのはfind、create、update、destroyです。これらのメソッドは非同期で処理されます。（裏側ではWaterlineがクエリーをデータベースに投げ、レスポンスを待ちます。）

最終的にはクエリメソッドはクエリオブジェクトを返します。実際にクエリを実行するには.exec(cb)をこのクエリオブジェクト上でコールしなければなりません。（cbはクエリが完了後に呼び出されるコールバックです。）

Waterlineはpromiseのためのオプトインサポートも用意しています。クエリオブジェクトで.exec()を呼び出す代わりにQ Promiseを返す.then()、.spread()や .fail()をコールすることも出来ます。


###モデルメソッド（Static/classメソッド）

モデルのクラスメソッドはモデルのインスタンス（つまりレコード）に対して特定のタスクを実行するためにモデル内に書かれるものです。これは.create(), .update(), .destroy(),や.find()などのおなじみの、データペース操作のためのCRUDメソッドが記述されているところです。

####カスタムのモデルメソッド
Waterlineではモデル内にカスタムのモデルメソッドを作成することが出来ます。この機能はWaterlineが認識できないキーを無視するという特性を利用して作られているので最初から用意されているメソッドやダイナミックメソッドをうっかり書き換えてしまわないように注意が必要です。（つまりcreateというメソッドを定義したりしてはいけません）カスタムモデルメソッドは特定のモデルに関連したコントローラコードを切り分けるのに便利です。つまりこれはコントローラコードを取り出し、再利用可能なコードにすることが出来ます。（更に言うとレスポンスとリクエストに依存しなくなります）

モデルメソッドは通常、非同期動作です。慣例に従って非同期のモデルメソッドでは最初の引数にオブジェクトインプット（オプションと呼ばれる）を、2つ目の引数にNodeのコールバックを入れたファンクションを実行し、最初のファンクションとコールバックファンクションの2段階での実行を行う必要があります。代わりにpromiseを返すということも選択できます。（両方の方法ともうまく動きますので、これは好みによるものです。もし特定の好みがなければNodeのコールバックを選んでください。）

カスタムのスタティックモデルメソッドを作成する上でのベストプラクティスはメソッドがレコードとそのプライマリキーの両方を受け入れられるようにすることです。複数のレコードを処理可能なメソッドにおいてはレコードの配列または主キーの配列を受け入れられるようにします。このコードを書くには少し時間がかかりますがメソッドをよりパワフルなものにします。それにもともとの作業はよく使われる作業を切り出す目的で行っているものですので、通常これをやる価値はあります。

例えば:

```
// in api/models/Monkey.js...

// 特定の人と同じ名前の猿を探す
findWithSameNameAsPerson: function (opts, cb) {

  var person = opts.person;

  // すべての作業を行う前にレコードが渡されたのか主キーが渡されたのかを確認する。
  // もし主キーが渡された場合はその人の情報をLookupする。
  (function _lookupPersonIfNecessary(afterLookup){
    // (this self-calling function is just for concise-ness)
    if (typeof person === 'object')) return afterLookup(null, person);
    Person.findOne(person).exec(afterLookup);
  })(function (err, person){
    if (err) return cb(err);
    if (!person) {
      err = new Error();
      err.message = require('util').format('Cannot find monkeys with the same name as the person w/ id=%s because that person does not exist.', person);
      err.status = 404;
      return cb(err);
    }

    Monkey.findByName(person.name)
    .exec(function (err, monkeys){
      if (err) return cb(err);
      cb(null, monkeys);
    })
  });

}
```

そして、以下のように実行できます。:

```
Monkey.findWithSameNameAsPerson(albus, function (err, monkeys) { ... });
// -or-
Monkey.findWithSameNameAsPerson(37, function (err, monkeys) { ... });
```

```
さらなるTipsに関してはTimothy the Monkeyに関してのincidentを御覧ください。
```

その他の例:

```
// api/models/User.js
module.exports = {

  attributes: {

    name: {
      type: 'string'
    },
    enrolledIn: {
      collection: 'Course', via: 'students'
    }
  },

  /**
   * ユーザは一つまたは複数のコースに加入する
   * @param  {Object}   options
   *            => courses {Array} list of course ids
   *            => id {Integer} id of the enrolling user
   * @param  {Function} cb
   */
  enroll: function (options, cb) {

    User.findOne(options.id).exec(function (err, theUser) {
      if (err) return cb(err);
      if (!theUser) return cb(new Error('User not found.'));
      theUser.enrolledIn.add(options.courses);
      theUser.save(cb);
    });
  }
};
```

####ダイナミックファインダー

Sailsの起動時に自動的に動的に作成される特別なクラスメソッドです。たとえばPersonもでるに"firstName"があるとすると以下のメソッドが生成されます。:

```
Person.findByFirstName('emma').exec(function(err,people){ ... });
```

####Resourceful Pubsub Methods

pubsubのhookに接続された特別なクラスメソッドです。詳細はresourceful pubsubの項目をご覧ください。

####アトリビュートメソッド（レコード/インスタンスメソッド）

アトリビュートメソッドはWaterlineクエリーから帰ってきたレコード（つまりモデルインスタンス）で利用可能なファンクションです。
例えばStudentモデルからGPAの高い10人の生徒を探してきた場合、それぞれ生徒のレコードはカスタムアトリビュートメソッドや既存のアトリビュートメソッドにアクセスできます。

既存のアトリビュートメソッド
すべてのWaterlineモデルにはいくつかのアトリビュートメソッドが自動的に含まれています。例えば：


* .toJSON()
* .save()
* .destroy()
* .validate()


カスタムアトリビュートメソッド
Waterlineではカスタムのアトリビュートメソッドを定義することも出来ます。他のアトリビュートと同じように定義しますが、右辺にはオブジェクトを代入する代わりにファンクションを代入します。

```
// api/models/Person.jsより

module.exports = {
  attributes: {
    // Primitive attributes
    firstName: {
      type: 'string',
      defaultsTo: ''
    },
    lastName: {
      type: 'string',
      defaultsTo: ''
    },

    // Associations (aka relational attributes)
    spouse: { model: 'Person' },
    pets: { collection: 'Pet' },

    // Attribute methods
    getFullName: function (){
      return this.firstName + ' ' + this.lastName;
    },
    isMarried: function () {
      return !!this.spouse;
    },
    isEligibleForSocialSecurity: function (){
      return this.age >= 65;
    },
    encryptPassword: function () {

    }
  }
};
```

```
備考　ビルトインのsave()と.destroy()を除いて（これらは特筆すべき例外です）慣例上、アトリビュートメソッドはほとんど同期動作です。
```

どんな時にアトリビュートメソッドを書いたらいいですか
カスタムアトリビュートは一部の情報をレコードから除外する場合にとくに便利です。すなわち取得した一つまたは複数のレコードの情報を削減する時です。（つまり、「婚姻状況」を抜き出したいときなど）


```
if ( rick.isMarried() ) {
  // ...
}
```

アトリビュートメソッドを書くべきではない時
非同期のアトリビュートメソッドを書くべきではありません。.save()や.destroy() のようなビルトインの非同期のアトリビュートメソッドは便利ですが、オリジナルの非同期のアトリビュートメソッドは予期せぬ結果をもたらすことが有ります。また、その方法はアプリケーションの開発上効率のいい方法ではありません。

例えば、婚姻状況を管理するアプリケーションを挙げます。Personモデルにおいてそれぞれの人の結婚相手アトリビュートを更新するアトリビュートメソッドを書くかもしれえません。そうすればこのようなコントローラコードを書くことが出来ます。:

```
personA.marry(personB, function (err) {
  if (err) return res.negotiate(err);
  return res.ok();
})
```

これは一見大丈夫に見えます。もっとも、personAに実際のレコードがないときに実行する別のアクションを作る必要が出るまでの間ですが。。。

もっと良いストラテジーはカスタムのモデルメソッドを書くことです。こうすることで実際のレコードインスタンスがない場合にでも隠せす可能になるのでファンクションをもっと再利用可能にし、もっと有用なものにすることが出来ます。上記のコードを以下のようにリファクタしましょう。 :

```
Person.marry([joe,raquel], function (err) {
  if (err) return res.negotiate(err);
  return res.ok();
})
```

####アトリビュートメソッドに命名する
アトリビュートメソッドに命名するときにはあなたの作業中のモデルに最初からあるアトリビュートメソッドとあなたが作った跡リニューとメソッドとの間でキュ号を越さないために一定の命名規則で行ってください。良いプラクティスとしては"get*" (例えばgetFullName())の形式でプレフィックスを付けるということとレコードそのものを改編するアトリビュートメソッドを書くのを避けるということです。


##Waterlineクエリー言語
Waterline Query languageはサポートされているすべてのデータベースコネクタからデータを取り出すことの出来るオブジェクトベースの比較式です。つまり、MySQLで使う時と同じクエリをMongo DBでもRediasでも使うことが出来るということです。これにより、コードの変更なしにデータベースの変更が可能です。


###クエリー言語の基礎

Criteriaオブジェクトは４種類のオブジェクトのうち一つから構成されます。これらはクエリオブジェクトのトップレベルオブジェクトです。このオブジェクトはMongoDBで使われているクエリオブジェクトに「ゆるく」似ていますがちょっとした違いが有ります。

クエリはアトリビュートを指定するためにwhereを使い、またlimitやskipなどのオプションをオブジェクトのどこの部分を取り出すかを指定するために使います。


```
Model.find({ where: { name: 'foo' }, skip: 20, limit: 10, sort: 'name DESC' });

// OR

Model.find({ name: 'foo' })
```

####Key Pairs

キーペアは指定された値に厳密に合致するレコードを探すのに使えます。キーはモデルの中のどのアトリビュートを指し示すかを表し、値はアトリビュートの中の値がどの内容に厳密に合致して欲しいか指定します。これはCriteriaオブジェクトの基礎です。

```
Model.find({ name: 'walter' })
```

複数のアトリビュート指定を同時に行うことも出来ます。

```
Model.find({ name: 'walter', state: 'new mexico' })
```

####Modified Pairs

改良型ペアは様々な補助演算子を使い、厳密比較ではうまくいかないケースをサポートします。

```
Model.find({
  name : {
    'contains' : 'alt'
  }
})
```

####In Pairs

INクエリはMySQLの'in queries'と似ています。配列中のそれぞれのエレメントはorとして処理されます。

```
Model.find({
  name : ['Walter', 'Skyler']
});
```

####Not-In Pairs

Not-InクエリはINクエリと似ていますが、比較オブジェクトがネストされているという部分が異なります。

```
Model.find({
  name: { '!' : ['Walter', 'Skyler'] }
});
```

####Or Pairs

クエリペアの配列を使うことでORクエリが実行できます。配列の中のいずれかの条件を満たすレコードが結果として帰ってきます。

```
Model.find({
  or : [
    { name: 'walter' },
    { occupation: 'teacher' }
  ]
})
```

####Criteria修飾子

クエリを作成する際には以下の修飾子を利用することが出来ます。

* '<' / 'lessThan'
* '<=' / 'lessThanOrEqual'
* '>' / 'greaterThan'
* '>=' / 'greaterThanOrEqual'
* '!' / 'not'
* 'like'
* 'contains'
* 'startsWith'
* 'endsWith'

####'<' / 'lessThan'

指定された値より小さな値を持つレコードを検索します。

```
Model.find({ age: { '<': 30 }})
```

####'<=' / 'lessThanOrEqual'

指定された値と同じ値か、より小さな値を持つレコードを検索します。

```
Model.find({ age: { '<=': 21 }})
```

####'>' / 'greaterThan'

指定された値より大きな値を持つレコードを検索します。

```
Model.find({ age: { '>': 18 }})
```

####'>=' / 'greaterThanOrEqual'

指定された値と同じ値か、より大きな値を持つレコードを検索します。

'''
Model.find({ age: { '>=': 21 }})
```

#### '!' / 'not'

指定した値と合致しないレコードを検索します。

```
Model.find({ name: { '!': 'foo' }})
```

####'like'

%記号を使ってパターンマッチングでレコードを検索します。

```
Model.find({ food: { 'like': '%beans' }})
```

####'contains'

両端に%記号を付けたパターンマッチング検索のショートカットです。指定した内容が文字列中のどこかに存在するレコードを返します。

```
Model.find({ class: { 'contains': 'history' }})

// 上記のコードは下記のコードと一緒です。

Model.find({ class: { 'like': '%history%' }})
```

####'startsWith'

右端に%記号を付けたパターンマッチング検索のショートカットです。文字列が指定した内容で始まるレコードを返します。

```
Model.find({ class: { 'startsWith': 'american' }})

// 上記のコードは下記のコードと一緒です。

Model.find({ class: { 'like': 'american%' }})
```

####'endsWith'

左端に%記号を付けたパターンマッチング検索のショートカットです。文字列が指定した内容で終わるレコードを返します。

```
Model.find({ class: { 'endsWith': 'can' }})

// 上記のコードは下記のコードと一緒です。

Model.find({ class: { 'like': '%can' }})
```

####'Date Ranges'

比較演算子をと使って日付範囲の検索を出来ます。

```
Model.find({ date: { '>': new Date('2/4/2014'), '<': new Date('2/7/2014') } })
```

###クエリオプション

クエリプションを使うことでクエリに対して帰ってくる結果をさらに改良することが出来ます。現在使えるオプションはい次のものです。:
* limit
* skip
* sort

####Limit

返しますレコードの数を制限します。

```
Model.find({ where: { name: 'foo' }, limit: 20 })
```

####Skip

最初の指定した個数を除く全てのレコードを受け取ります。

```
Model.find({ where: { name: 'foo' }, skip: 10 });
```

###Pagination

skipとlimitを一緒に使うことでページネーションが出来ます。

```
Model.find({ where: { name: 'foo' }, limit: 10, skip: 10 });
```

paginateはskipとlimitを一緒に使うのと同じ役割を果たすWaterlineヘルパーメソッドです。

```
Model.find().paginate({page: 2, limit: 10});
```

####Waterline
WaterlineAPIに関して更に詳しくは以下のドキュメントで確認できます。
* Sails.js Documentation
* Waterline README
* Waterline Documentation
* Waterline Github Repository

####Sort

検索結果をアトリビュートの名前でソートすることが出来ます。単にアトリビュートを指定するだけで昇順のソートが出来る他ascまたはdescのフラグを指定することでそれぞれのアトリビュートに対して昇順と降順の並べ順を指定できます。

```
// nameの昇順で並び替え。
Model.find({ where: { name: 'foo' }, sort: 'name' });

// nameの降順で並び替え。
Model.find({ where: { name: 'foo' }, sort: 'name DESC' });

// nameの昇順で並び替え。
Model.find({ where: { name: 'foo' }, sort: 'name ASC' });

// バイナリNotationで並び替え。
Model.find({ where: { name: 'foo' }, sort: { 'name': 1 }});

// 複数のアトリビュートで並び替え。
Model.find({ where: { name: 'foo' }, sort: { name:  1, age: 0 });
```
###ケースセンシティブか？

Waterlineのすべてのクエリはケースセンシティブではありません。このおかげでクエリを簡単に実行できますが、文字列をインデックスするのは難しくなります。このことは文字列を検索したり文字列で並び替えたりするときに念頭に置いておいてください。

現在のところ、ケースセンシティブなクエリを発行するもっともよい手段は.native()か.query()のメソッドを使うことです。


##バリデーション
Sailsはモデルのアトリビュートに対する自動バリデーションを用意しています。レコードを作成したり更新するときは常にあなたの指定したバリデーションルールにそっているか自動的に確認されます。これにより、アプリケーションに保存されるデータが不正なものでないことを保証することが簡単に実現出来ます。


###バリデーションルール

バリデーションはバリデータの最上位層に位置するAncherと呼ばれる軽量なレイヤーによってハンドルされます。AncherはNode.jsで最もパワフルなバリデータの一つです。一部にデータベース上での実装がなされていることが必要な物は有りますが、Sailsではバリデータの中のほとんどすべての種類のバリデーションをサポートします。

|バリデータ名|確認するもの|利用上の注意|
|---|---|---|
|after|入力された日付の文字列が指定日より後のものか。|正しいJavascriptのDate形式である必要があります。|
|alpha|	アルファベッドのみ(a-zA-Z)を含む文字列かどうか。|
|alphadashed|   |数字とダッシュのみを含む文字列であるかどうか。（訳注：要確認）|
|alphanumeric| アルファベッドと数字のみを含む文字列かどうか。|
|alphanumericdashed	|アルファベッド、数字、ダッシュのみを含む文字列かどうか。|
|array|	正しいJavascriptの配列形式であるか。|「配列化された文字列」はパスしない。|
|before|入力された日付の文字列が指定日より前のものか。|
|binary|バイナリデータか|文字列であれば常にパスします。|
|boolean|正しいJavascriptのBoolean形式であるか。|文字列はパスしない。|
|contains|文字列がseedを含んでいるかどうか|
|creditcard|文字列はクレジットカードの形式かどうか。|
|date|文字列は日付型かどうか。|文字列とJavascript型のどちらでもパスする。|
|datetime|文字列はJavascriptのdatetime型かどうか。|
|decimal|   |小数点を含むかあるいは１より小さいか。|
|email|	文字列はメールアドレスのように見えるか。|
|empty|	長さが０で数えられるプロパティもない配列と文字列、引数オブジェクトが "empty"とみなされます。	|lo-dash _.isEmpty()|
|equals|文字列は指定された内容と同じか。	| === ! 型と値が合致してる必要があります。|
|falsey|JavascriptエンジンがFalseとみなすものかどうか。|
|finite	|値または値から変換できる数が有限数かどうか。|True時にBoolを、False時にからの文字列を返すネイティブのisFiniteとは同じでありません。 
|float|	Floatがたの数値化どうか。|
|hexadecimal|十六進の数値かどうか|
|hexColor|	十六進色表記かどうか|
|in	|指定された文字列のうちいずれかと合致するものであるか。|
|int|文字列は整数型であるか。|
|integer|intと同じ。	|どうしてこの両方が存在するかはわかりません。|
|ip|	文字列は正しいIP(v4またはv6)アドレスかどうか。|
|ipv4|	文字列は正しいIP(v4)アドレスかどうか。|
|ipv6|	文字列は正しいIP(v6)アドレスかどうか。|
|is	|   | REGEXと一緒に使います。|
|json|正しいJSON型かどうかTry and Catchします。|
|len|与えられた整数 > param1 && < param2であるかどうか|どうやってParamを設定するんだろうか。。。|
|lowercase|	文字列は全て小文字であるか。|
|max|
|maxLength|	与えられた整数 > 0 && < param2かどうか|
|min|
|minLength|	
|not|   |REGEX関連。|
|notContains|		
|notEmpty|
|notIn|	モデルアトリビュートの値が定義されたバリデータの値の範囲内（なおかつ同じ型）に存在するか。|文字列と配列を処理できます。|
|notNull|内容はNULLでないかどうか。|
|notRegex|
|null|内容がNULLかどうか。|
|number|数字かどうか。|	NaNは数字とみなされます。|
|numeric|文字列は数字のみを含むかどうか。|
|object|オブジェクトのLanguage typeであるかどうか。|配列、関数、オブジェクト、正規表現、new Number(0)とnew String('')がパスします !|
|regex|		
|protected|	モデルインスタンスでtoJSONが呼び出された時にこのアトリビュートは取り除かれるべきか。|
|required|	レコードが作成される前の段階で何らかの正当な値が入っている必要があるかどうか。|
|string|文字列かどうか
|text|文字列かどうか
|truthy|JavascriptエンジンがFalseとみなすものかどうか。|訳注：多分Tureの誤植。
|undefined|	Javascriptエンジンが'undefined'とみなすものかどうか。|
|unique|新しいレコードのアトリビュートがユニークかどうか。|
|uppercase|	文字列が全て大文字かどうか。|
|url	|文字列がURLの形式かどうか。|
|urlish	|文字列が何らかの拡張子を含むルートかどうか。|	/^\s([^\/]+.)+.+\s*$/g|
|uuid	|文字列はUUID(v3,v4,もしくはv5)か。|
|uuidv3	|文字列はUUID(v3)か。|
|uuidv4	|文字列はUUID(v4)か。|

###Custom Validation Rules

You can define your own types and their validation with the types object. It's possible to access and compare values to other attributes (with "this"). This allows you to move validation business logic into your models and out of your controller logic.

Note that your own type always have to be a variant of the basic data-types ("string", "int", "json", ...)

####Example Model

```
// api/models/foo
module.exports = {

  types: {
    is_point: function(geoLocation){
      return geoLocation.x && geoLocation.y
    },
    password: function(password) {
      return password === this.passwordConfirmation;
    }
  },
  attributes: {
    firstName: {
      type: 'string',
      required: true,
      minLength: 5,
      maxLength: 15
    },
    location: {
      //note, that the base type (json) still has to be defined
      type: 'json',
      is_point: true
    },
    password: {
      type: 'string',
      password: true
    },
    passwordConfirmation: {
      type: 'string'
    }

  }
}
```

##Model Settings

The following properties can be specified at the top level of your model definition to override the defaults for that particular model. To override the default settings shared by all of your models, edit config/models.js.

###migrate

```
migrate: 'safe'
```

In short, this setting controls whether/how Sails will attempt to automatically rebuild the tables/collections/sets/etc. in your schema.

In a production environment (NODE_ENV==="production") Sails always uses migrate:"safe" to protect inadvertent deletion of your data. However during development, you have a few other options for convenience:

1. safe - never auto-migrate my database(s). I will do it myself (by hand)
2. alter - auto-migrate, but attempt to keep my existing data (experimental)
3. drop - wipe/drop ALL my data and rebuild models every time I lift Sails

When your sails app lifts, waterline validates your all of the data in your database. This flag tells waterline what to do with data when the data is corrupt. You can set this flag to safe which will ignore the corrupt data and continue to lift. You can also set it to `


|Auto-Migration Strategy|Description|
|---|---|
|safe|never auto-migrate my database(s). I will do it myself, by hand.|
|alter|auto-migrate columns/fields, but attempt to keep my existing data (experimental)|
|drop|wipe/drop ALL my data and rebuild models every time I lift Sails|

Note, by using drop, or even alter, you risk losing your data. Be careful. Never use drop or alter with a production dataset.

###schema

```
schema: true
```

A flag to toggle schemaless or schema mode in databases that support schemaless data structures. If turned off, this will allow you to store arbitrary data in a record. If turned on, only attributes defined in the model's attributes object will be stored.

For adapters that don't require a schema, such as Mongo or Redis, the default setting is schema:false.

###connection

```
connection: 'my-local-postgresql'
```

The configured database connection where this model will fetch and save its data. Defaults to localDiskDb, the default connection that uses the sails-disk adapter.

###identity

```
identity: 'purchase'
```

The lowercase unique key for this model, e.g. user. By default, a model's identity is inferred automatically by lowercasing its filename. You should never change this property on your models.

###globalId

```
globalId: 'Purchase'
```

This flag changes the global name by which you can access your model (if the globalization of models is enabled). You should never change this property on your models- to disable globals, see sails.config.globals.

###autoPK

```
autoPK: true
```

A flag to toggle the automatic definition of a primary key in your model. The details of this default PK vary between adapters (e.g. MySQL uses an auto-incrementing integer primary key, whereas MongoDB uses a randomized string UUID). In any case, the primary keys generated by autoPK will be unique. If turned off no primary key will be created by default, and you will need to define one manually, e.g.:

```
attributes: {
  sku: {
    type: 'string',
    primaryKey: true,
    unique: true
  }
}
```

###autoCreatedAt

```
autoCreatedAt: true
```

A flag to toggle the automatic definition of a createdAt attribute in your model. By default, createdAt is an attribute which will be automatically set when a record is created with the current timestamp, e.g.:

```
attributes: {
  createdAt: {
    type: 'datetime',
    defaultsTo: function (){ return new Date(); }
  }
}
```

###autoUpdatedAt

```
autoUpdatedAt: true
```

A flag to toggle the automatic definition of a updatedAt attribute in your model. By default, updatedAt is an attribute which will be automatically set with the current timestamp every time a record is updated, e.g.: 
attributes: { updatedAt: { type: 'datetime', defaultsTo: function (){ return new Date(); } } }

###tableName

```
tableName: 'some_preexisting_table'
```

You can define a custom name for the physical collection in your adapter by adding a tableName attribute. This isn't just for tables. In MySQL, PostrgreSQL, Oracle, etc. this setting refers to the name of the table, but in MongoDB or Redis, it refers to the colelction, and so forth. If no tableName is specified, Waterline will use the model's identity as its tableName.

This is particularly useful for working with pre-existing/legacy databases.

###attributes

```
attributes: {
  name: { type: 'string' },
  email: { type: 'email' },
  age: { type: 'integer' }
}
```

See Attributes.

