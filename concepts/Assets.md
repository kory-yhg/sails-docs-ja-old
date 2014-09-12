#アセット
###概要

アセットとはあなたがサーバにおいて公開したいスタティックファイル(js,css,imagesなど)を指します。Sailsではアセットはassets/ディレクトリに配置されあなたがアプリケーションをデプロイした時に非表示の一時テクディレクトリ(.tmp/public/)に保存され、処理並びに同期がされます。この.tmp/publicフォルダーの中身はSailsが実際に提供するものです。これはおおまかに言ってexpressにおける"public"フォルダーやApacheのようなWebサーバにおける"www"フォルダーと同等です。この中間ステップを踏むことでSailsはアセットがクライアントで実際に使われるための前処理や事前コンパイル（LESSやCoffeeScript、SASS、spritesheets、Jade templatesなどの）を行うことが出来ます。

###スタティックミドルウエア

舞台裏ではSailsはアセットを提供するためにExpressを使っています。このミドルウエアの設定（例えばキャッシュの設定など）は/config/http.jsでできます。

###index.html

ほとんどのWebサーバのようにSailsはindex.htmlの習慣を採用しています。例えばあなたが新しいSailsプロジェクトの中にassets/foo.htmlを作ったとするとhttp://localhost:1337/foo.htmlでアクセス可能になります。しかしもしあなたがassets/foo/index.htmlを作ったとするとhttp://localhost:1337/foo/でもhttp://localhost:1337/fooでもアクセスすることが出来るようになります。

###ルートの優先

スタティックミドルウエアがSailsのルータの背後にインストールされるということを理解することは重要な事です。
そのためもしあなたがカスタムのルートを定義しその一方で競合するパスにアセットを配置した場合、カスタムルートの設定はアクセスがスタティックミドルウエアに到達する前にそのルーティングを妨害してしまいます。
例えば、もしあなたがassets/index.htmlを作成し、config/routes.jsファイルで何もルートを定義しなかった場合そのファイルはホームページとして提供されます。しかし、もしあなたがカスタムのルート'/': 'FooController.bar',を定義した場合、そのルート設定が優先されます。


##既定のタスク
###概要

Sailsにバンドルされているアセットパイプラインはあなたのプロジェクトをより矛盾がなくより生産的に設計するために一般的なデフォルトを使って実装されたGruntタスクのセットになっています。フロントエンドのアセットワークフローはすべてデフォルトのタスクの枠にとらわれずにカスタマイズすることが出来ます。
Sailsはあなたのニーズに合わせて簡単に新しいタスクを作ることが出来ます。
例えば以下の様な機能がSailsのデフォルトのGrunt設定として使うことが出来ます。


* LESSの自動コンパイル
* JITの自動コンパイル
* Coffescriptの自動コンパイル
* その他のアセットの自動注入、最小化、連結
* Web上へアップするためのファイルの作成
* ファイスの監視や同期化
* プロダクション環境でのアセットの最適化

###デフォルトのGruntタスクの振る舞い

いかにSailsプロジェクトに含まれているGruntタスク関して何をするのかを簡潔に記しました。また、それぞれのタスクの説明に関しての詳細な説明ページヘのリンクも用意しました。

####clean

このGruntタスクはプロジェクトの.tmp/public/ディレクトリにある内容を消去します。

利用方法（翻訳未完）

####coffee

assest/js/にあるcoffeeScriptファイルをJavascriptにコンパイルし.tmp/public/js/に移動します。

利用方法（翻訳未完）

####concat

javascriptとcssをそれぞれ連結し.tmp/public/concat/ディレクトリに保存します。

利用方法（翻訳未完）

####copy

dev task設定の時はcoffescriptとlessファイル以外のすべてのファイルとディレクトリーをSailsのアセットフォルダーから.tmp/public/ディレクトリにコピします。

build task設定の時は全てのファイルとディレクトリを.tmp/publicディレクトリからwwwディレクトリにコピーします。

利用方法（翻訳未完）

####cssmin

CSSファイルを最小化し.tmp/public/min/ディレクトリに保管します。

利用方法（翻訳未完）

####jst

Underscoreテンプレートをプレコンパイルし.jstファイルにします。(つまり、HTMLテンプレートを小さなJavascriptのFunctionに変換します。）これによってテンプレートレンダリングを高速化させ帯域利用を減少させることが出来ます。

利用方法（翻訳未完）

####less

LESSファイルをCSSにコンパイルします。assets/styles/importer.lessのみがコンパイルされます。これにより順序を任意に設定することが出来るようになります。（つまり他のスタイルシートの前にdependencies, mixins, variables, resetsなどをインポートすることが出来ます）


利用方法（翻訳未完）

####sails-linker

Javascriptのタグには\<script\>を、CSSファイルには\<link\>を自動的に挿入します。また、\<script\>を使ってプレコンパイル済みのテンプレートに対してリンクを行います。これらに対する詳細な説明はこちらで確認することが出来ますが、おさえておきたい大切なことはスクリプトとスタイルシートの挿入は \<!--SCRIPTS--\>\<!--SCRIPTS END--\>や\<!--STYLES--\>\<!--STYLES END--\のタグを含むファイルのみで行われるということです。また、これらは新しく作成したSailsプロジェクトのviews/layout.ejsにデフォルトで自動的に組み込まれています。もしあなたのプロジェクトでリンカーを使いたくない時は単にこれ他のタグを削除してください。


利用方法（翻訳未完）

####sync

ディレクトリを同期済みにするタスクです。このタスクはgrunt-contrib-copyにとても良く似ていますが、本当に変更されたもののみをコピーしようとします。これはassets/フォルダーから.tmp/public/へのコピーのみを行い、コピー先に既存のすべてのファイルを上書きします。

利用方法（翻訳未完）

####uglify

クライントサイドのjavascriptアセットを最小化します。

利用方法（翻訳未完）


####watch

ファイルパターンが追加され、編集され、削除された時に毎回予め設定されたスクリプトを実行します。assets/フォルダに配置されたファイルを監視し、（例えばLESSやJSTのコンパイルのような）適切なタスクを再実行します。これにより行った編集の結果をSailsを再起動することなく確認することが出来ます。

利用方法（翻訳未完）

##Disabling Grunt

Gruntタスクの組み込みを無効にするためには、Gruntfile(と場合によってはタスクやフォルダーのこともあります)を削除するだけで大丈夫です。同様にGrunt hookの無効化も出来ます。これをやるには.sailsrcのhooksで以下のようにGruntを無効化するだけで出来ます。:

```
{
    "hooks": {
        "grunt": false
    }
}
```

###これをSASSやAngular、クライアントサイドJadeテンプレートなどのためにカスタマイズできますか。

もちろん！あなたのプロジェクトファイルのtasks/ディレクトリにある関係するタスクを書き換えるだけでできますし、例えばSASSのように新しいものを作ることでも実現できます。
もしもGruntを使いたいけれどもデフォルトのフロントエンドのを使いたくない場合はあなたのプロジェクトのアセットフォルダーを削除して関連するフロントエンド関連のタスクをrunt/register/とgrunt/config/から削除してください。

You can also run sails new myCoolApi --no-frontend to omit the assets folder and front-end-oriented Grunt tasks for future projects. 

さらにsails-generate-frontendモジュールを別のサードパーティの物に変えたり自分で開発することも出来ます。これをすることでネイティブのiOSアプリやAndroidアプリ、CordovaアプリSteroidsJSアプリをSailsで開発できる新たなboilerplateを作ることが出来ます。


##タスクの自動化

###概要

tasks/ディレクトリにはGruntタスクとその設定ファイルがまとめられて入っています。タスクは主にフロントエンドのアセットをバンドルする(例えばスタイルシートやスクリプト、クライアントサイドマークアップテンプレートなど）のに利用されますがその他にもbrowserifyのcompilationからデータベースマイグレーションに至るまで様々な開発中の細々とした仕事を自動化するためにも使えます。
Sailsは簡便のためにいくつかのデフォルトのタスクをバンドルしていますが文字通り数百ものプラグインが利用でき、これによってすべての雑用を最小限の手数で完了することが出来ます。もしもあなたが使いたいタスクがなかったら自分でGruntのプラグインを作ってnpmに載せることも出来ます。
今までにGruntを使ったことがない方はGetting Started guideを見てください。そこにはGruntfileの書き方やインストール方法が書かれています。


###アセットパイプライン

アセットパイプラインはビューに挿入されるアセットを整形する部分であり、tasks/pipeline.jsファイルに記述されます。これらは3つの部分からなります。
これらのアセットを設定するにはGruntにタスクファイル設定とwildcard/glob/splaのパターンを使うだけで簡単にできます。

###CSSファイルの挿入

ここではHTMLに\<link\>タグとして挿入されるCSSファイルを指定します。これらのタグは全てのビューの<!--STYLES--><!--STYLES END-->コメントの間に挿入されます。

###Javascriptファイルの挿入

ここではHTMLに\<script\>タグとして挿入されるCSSファイルを指定します。これらのタグは全てのビューの<!--STYLES--><!--STYLES END-->コメントの間に挿入されます。各ファイルは配列の並び順に基づいて挿入されます。（すなわち依存するファイルのパスの前に依存されるファイルのパスを入れなければなりません）


###テンプレートファイルの挿入

ここではjstファンクションにコンパイルされjst.jsファイルに保存されるhtmlファイルの配列を指定します。このファイルはその後\<script\>タグとしてHTMLの\<!--TEMPLATES--\>\<!--TEMPLATES END--\> コメントの間に挿入されます。同じGruntのwildcard/glob/splatとタスク設定ファイルはいくつかのタスク設定jsファイル自体でで使われていますので変更を加えたいときはそれらも同様に編集してください。


###タスク設定

設定済みのタスクはGruntfileが実行されたとき適用されるルールをまとめたものです。これらtasks/config/にあり、完全にカスタマイズ可能です。あなたの使いたい用途に合わせてこれたのGruntファイルのうち全てを編集し、省略し、また置き換えることが出来ます。同様にsomeTask.jsのようなファイルをこのディレクトリに追加し他の適切なタスクと一緒に登録することで(grunt/register/*.jsのファイルを参照してください。)あなた自身のGruntファイルを作成することも出来ます。なおSailsは特段の設定なしに起動できるように便利なデフォルトのタスクのセットを持っているということも忘れずにいてください。


###カスタムのタスクを設定する


プロジェクトにカスタムのタスクを設定するのはとてもシンプルで、Gruntの設定とタスクAPIを使うことでタスクをモジュールにすることが出来ます。既存のタスクを置き換えて新しいタスクを作成する例を見てみましょう。デフォルトで設定されているunderscoreの代わりにHandlebarsをテンプレートエンジンとして使いたいとします。:

* まずはじめに以下のコマンドをターミナルで実行することでHandlebarsのGruntプラグインをインストールするところから始めます。:
npm install grunt-contrib-handlebars --save-dev

tasks/config/handlebars.jsに設定ファイルを作成します。これはhandlebarsの設定を入れるところです。 
```
// tasks/config/handlebars.js
// --------------------------------
// handlebar task configuration.

module.exports = function(grunt) {

  // We use the grunt.config api's set method to configure an
  // object to the defined string. In this case the task
  // 'handlebars' will be configured based on the object below.
  grunt.config.set('handlebars', {
    dev: {
      // We will define which template files to inject
      // in tasks/pipeline.js 
      files: {
        '.tmp/public/templates.js': require('../pipeline').templateFilesToInject
      }
    }
  });

  // load npm module for handlebars.
  grunt.loadNpmTasks('grunt-contrib-handlebars');
};
```

アセットパイプラインにおけるソースファイルのパス設定を書き換えます。ここでの唯一の変更点はunderscoreのテンプレートはシンプルなHTMLファイルの中に入れれるのに対してhandelbarsは.hbs拡張子のファイルを参照するということです。

```
// tasks/pipeline.js
// --------------------------------
// asset pipeline

var cssFilesToInject = [
  'styles/**/*.css'
];

var jsFilesToInject = [
  'js/socket.io.js',
  'js/sails.io.js',
  'js/connection.example.js',
  'js/**/*.js'
];

// We change this glob pattern to include all files in
// the templates/ direcotry that end in the extension .hbs
var templateFilesToInject = [
  'templates/**/*.hbs'
];

module.exports = {
  cssFilesToInject: cssFilesToInject.map(function(path) {
    return '.tmp/public/' + path;
  }),
  jsFilesToInject: jsFilesToInject.map(function(path) {
    return '.tmp/public/' + path;
  }),
  templateFilesToInject: templateFilesToInject.map(function(path) {
    return 'assets/' + path;
  })
};

compileAssetsとsyncAssetsの登録済みタスクににhanldebarsのタスクをインクルードします。ここはjstタスクが使われるところですが、これを新しく設定したhandlebarsで置き換えます。


// tasks/register/compileAssets.js
// --------------------------------
// compile assets registered grunt task

module.exports = function (grunt) {
  grunt.registerTask('compileAssets', [
    'clean:dev',
    'handlebars:dev',       // changed jst task to handlebars task
    'less:dev',
    'copy:dev',
    'coffee:dev'
  ]);
};

// tasks/register/syncAssets.js
// --------------------------------
// synce assets registered grunt task

module.exports = function (grunt) {
  grunt.registerTask('syncAssets', [
    'handlebars:dev',      // changed jst task to handlebars task
    'less:dev',
    'sync:dev',
    'coffee:dev'
  ]);
};
```

jstタスクの設定を削除します。我々はすでにtasks/config/jst.jsを必要としないのでこれを削除することが出来ます。単にプロジェクトから削除するだけで構いません。理想的にはこれをプロジェクトとプロジェクトのnode dependenciesから削除します。これは以下のコマンドを実行することで行えます。

npm uninstall grunt-contrib-jst --save-dev

###タスクのトリガー


デベロップメントモードにおいてはSailsはデフォルトのタスク(tasks/register/default.js)を実行します。これはLESSやCoffeeScript、クライアントサイドのJSTテンプレートをコンパイルし、アプリケーションの動的なビューと静的なHTMLページにリンクします。
本番環境においてはSailsは本番環境のタスク(tasks/register/prod.js)を実行し、これはこれはデフォルトのタスクに加えてアプリケーションのスタイルシートスクリプトを最小化します。これによりアプリケーションの読み込み時間と利用帯域を節約することが出来ます。
これらのタスクのトリガーはtasks/register/フォルダーにあるGruntの"basic"タスクに有ります。以下にSailsの全てのタスクトリガーとそれぞれが走らせるコマンドのリファレンスを説明します。:

####sails lift

デフォルトのタスクを実行します。(tasks/register/default.js).
sails lift --prod

本番環境のタスクを実行します。(tasks/register/prod.js).
####sails www

ビルドタスクを実行します。(tasks/register/build.js).
####sails www --prod (production)

本番環境のビルドタスクを実行します。 (tasks/register/buildProd.js).
