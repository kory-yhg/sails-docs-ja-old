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

dev task config Copies all directories and files, exept coffescript and less files, from the sails assets folder into the .tmp/public/ directory.
build task config Copies all directories and files from the .tmp/public directory into a www directory.

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

Automatically inject \<script\> tags for javascript files and \<link\> tags for css files. Also automatically links an output file containing precompiled templates using a \<script\> tag. A much more detailed description of this task can be found here, but the big takeaway is that script and stylesheet injection is only done in files containing \<!--SCRIPTS--\>\<!--SCRIPTS END--\> and/or \<!--STYLES--\>\<!--STYLES END--\> tags. These are included in the default views/layout.ejs file in a new Sails project. If you don't want to use the linker for your project, you can simply remove those tags.

利用方法（翻訳未完）

####sync

ディレクトリを同期済みにするタスクです。このタスクはgrunt-contrib-copyにとても良く似ていますが、本当に変更されたもののみをコピーしようとします。これはassets/フォルダーから.tmp/public/へのコピーのみを行い、コピー先に既存のすべてのファイルを上書きします。

利用方法（翻訳未完）

####uglify

クライントサイドのjavascriptアセットを最小化します。

利用方法（翻訳未完）


####watch

Runs predefined tasks whenever watched file patterns are added, changed or deleted. Watches for changes on files in the assets/ folder, and re-runs the appropriate tasks (e.g. less and jst compilation). This allows you to see changes to your assets reflected in your app without having to restart the Sails server.

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


###Asset pipeline

The asset pipeline is the place where you will organize the assets that will be injected into your views, and it can be found in the tasks/pipeline.js file. Configuring these assets is simple and uses grunt task file configuration and wildcard/glob/splat patterns. They are broken down into three sections.

###CSS Files to Inject

This is an array of css files to be injected into your html as <link> tags. These tags will be injected between the <!--STYLES--><!--STYLES END--> comments in any view in which they appear.

###Javascript Files to Inject

This is an array of Javascript files that gets injected into your html as \<script\> tags. These tags will be injected between the \<!--SCRIPTS--\>\<!--SCRIPTS END--\> comments in any view in which they appear. The files get injected in the order they are in the array (i.e. you should place the path of dependecies before the file that depends on them.)

###Template Files to Inject

This is an array of html files that will compiled to a jst function and placed in a jst.js file. This file then gets injected as a \<script\> tag in between the \<!--TEMPLATES--\>\<!--TEMPLATES END--\> comments in your html.
The same grunt wildcard/glob/splat patterns and task file configuration are used in some of the task configuration js files themselves if you would like to change those too.

###Task configuration

Configured tasks are the set of rules your Gruntfile will follow when run. They are completely customizable and are located in the tasks/config/ directory. You can modify, omit, or replace any of these Grunt tasks to fit your requirements. You can also add your own Grunt tasks- just add a someTask.js file in this directory to configure the new task, then register it with the appropriate parent task(s) (see files in grunt/register/*.js). Remember, Sails comes with a set of useful default tasks that are designed to get you up and running with no configuration required.

###Configuring a custom task.

Configuring a custom task into your project is very simple and uses Grunt’s config and task APIs to allow you to make your task modular. Let’s go through a quick example of creating a new task that replaces an existing task. Let’s say we want to use the Handlebars templating engine instead of the underscore templating engine that comes configured by default:

* The first step is to install the handlebars grunt plugin using the following command in your terminal:
npm install grunt-contrib-handlebars --save-dev

Create a configuration file at tasks/config/handlebars.js. This is where we’ll put our handlebars configuration.

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

Replace the path to source files in asset pipeline. The only change here will be that handelbars looks for files with the extension .hbs while underscore templates can be in simple html files.

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

Include the hanldebars task into the compileAssets and syncAssets registered tasks. This is where the jst task was being used and we are going to replace it with the newly configured handlebars task.

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

Remove jst task config file. We are no longer using it so we can get rid of tasks/config/jst.js. Simply delete it from your project.
Ideally you should delete it from your project and your project's node dependencies. This can be done by running this command in your terminal.
npm uninstall grunt-contrib-jst --save-dev

###Task triggers

In development mode, Sails runs the default task (tasks/register/default.js). This compiles LESS, CoffeeScript, and client-side JST templates, then links to them automatically from your app's dynamic views and static HTML pages.
In production, Sails runs the prod task (tasks/register/prod.js) which shares the same duties as default, but also minifies your app's scripts and stylesheets. This reduces your application's load time and bandwidth usage.
These task triggers are "basic" Grunt tasks located in the tasks/register/ folder. Below, you'll find the complete reference of all task triggers in Sails, and the command which kicks them off:

####sails lift

Runs the default task (tasks/register/default.js).
sails lift --prod

Runs the prod task (tasks/register/prod.js).
####sails www

Runs the build task (tasks/register/build.js).
####sails www --prod (production)

Runs the buildProd task (tasks/register/buildProd.js).
