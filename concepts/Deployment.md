#デプロイ

###概要

####デプロイする前に

アプリケーションをデプロイする前に以下のことを自問してみてください。

* あなたが予期している通信は何ですか。
* 契約上（SLAなど）、稼働時間の補償に適合する必要がありますか。
* どんな種類のフロントエンドアプリかあなたのインフラを「叩いて」きますか。
    * Androidアプリケーション
    * iOSアプリケーション
    * デスクトップWebブラウザ
    * モバイルWebブラウザ (タブレットや携帯、iPadminiなど)
    * テレビ？時計？トースター？。。。
* それらはどんなものをリクエストしてきますか。
    * JSON?
    * HTML?
    * XML?
* Socket.ioを利用してリアルタイムのPublish/Subscribeデザインパターンに対応する必要がありますか。
    * 例：チャット、リアルタイム分析、アプリ内通知・メッセージ
* クラッシュした時やエラーが起こった時どうやって監視しますか。
    * Sailsのログ設定を見てみてください。
    
#### 1台のサーバにデプロイする

Node.jsはものすごく速いです。そのため来るべきトラフィックをハンドルするためには1台のサーバーで十分です（少なくとも最初のうちは）


###設定

* すべてのプロダクション環境の設定はconfig/env/production.jsに保存されています。
* アプリケーションが80番ポートで動作するように設定してください（Nginxのようなプロキシを使わない時は）
* すべてのcssとjsがバンドルされるようにプロダクション環境を設定し、内部サーバが適切な環境に切り替わるようにしてください。（Linkが必要です）
* データベース設定がプロダクション環境に設定されていることを確認して下さい。これはあなたがMySQLのようなリレーショナルデータベースを利用しているときは特に重要です。なぜならSailsはプロダクション環境で実行された時にはすべてのモデルをmigrate:safeに設定します。つまり自動マイグレーションは行われません。データベースの作成は以下の手順でできます。
    * データベースをサーバ上に作成しローカル上でそのデータベースへの接続をするように設定し、更にmigrate:alterの設定をしたうえでSailsを起動します。こレにより自動的にセットアップできます。
    * データベースサーバにリモートから接続できない場合は単純にローカルのスキーマをダンプしてデータベースサーバでインポートしてください。
* POST, PUT, DELETEのリクエストに対してCSRF防御を適用します。
* SSLを有効化します。
* もしSocketを使ってる時は:
    * config/sockets.jsを適切に設定し、socket.ioの推奨するプロダクション環境での設定に適合するようにします。
        * 例：flashsocket transportを有効化する
        
### デプロイ

プロダクション環境ではSailsがクラッシュしてもデーモンが走り続けられるように、Sailを直接使う代わりにforeverかPM2を使います。

* foreverをインストールする:
    * foreverについての詳細: https://github.com/nodejitsu/forever

```
 sudo npm install -g forever
```


* あるいはPM2をインストールする:
    * PM2についての詳細: https://github.com/Unitech/pm2
    
```
  sudp npm install pm2 -g --unsafe-perm
```   
    
* アプリケーションディレクトリーからforever start app.js --prod または pm2 start app.js -x -- --prod　を実行してサーバを起動します。
    * これはsails lift --prodと同じです。しかしこのようにすることでSailsがクラッシュしても自動的に復旧します。
    
##FAQ
###環境変数を利用することは出来ますか」

ポートと環境の選択を環境変数を用いて設定することも出来ます。

```
NODE_ENV=production sails lift PORT=443 sails lift
```

###プロダクション環境のデータベースの認証情報などはどこに置けばいいですか。

その他のdeployment/machine-specificな設定（様々な認証情報を含む）に関してはconfig/local.jsを使うべきです。
このファイルはデフォルトで.gitignoreに登録されているのでうっかり認証情報の入ったファイルをレポジトリにアップロードしてしまう心配がありません。

```
// ローカル設定
// 
// デフォルトで.gitignoreに入っています。
// ここはあなたのローカルのシステムやプロダクション環境に合わせた設定のオーバーライドを書くところです。
//
// 例えばローカルマシンの80番ポートを利用するためには`port`設定をオーバーライドします。
module.exports = {
    port: 80,
    environment: 'production',
    adapters: {
        mysql: {
            user: 'root',
            password: '12345'
        }
    }
}
```

###Sailをプロダクション環境のサーバにどうやってアップロードすればいいですか。

すでにNode.jsのサーバが走っていますか。サーバのIPアドレスが分かるのであればそこにSSHアクセスし、最初にsudo npm install -g foreverを実行してSailsとforeverをセットアップしてください。
その後、新規フォルダーを作成しそこにプロジェクトをgit clone（もしgitレポジトリがないならscpを）してください。さらにそのフォルダにcdし、forever start app.jsでサーバを起動します。

###パフォーマンスのベンチマーク
SailsのパフォーマンスはNode.js/Express相当です。つまり別の言葉で言えば「
速い！」SailsとWaterlineにおいて幾らかのチューニングをしていますが、我々の第一の目標は「元々ものすごく速いものを台無しにしない」というものです。
出でにせよ@ry, @visionmedia, @isaacs, #v8, @joyentを始めとしたNode.jsのコアチームに感謝します。

* http://serdardogruyol.com/?p=111
    
##ホスティング 

Sails.jsをサポートしているホスティングプロバイダの一部をリストにしました。

###Modulusにデプロイする

* http://blog.modulus.io/sails-js

###NodeJitsuにデプロイする

NodeJitsuにデプロイするにはちょっとした設定変更が必要です。: sppフォルダの中のconfig/local.jsを開きここに以下の行を加えてください。 

```
// Port this Sails application will live on
    port: 80,
    host: 'subdomain.jit.su',        
```

The host:は元々記述されていませんのでこれを追加する必要があります。Nodejitsuはjitsu deployを実行するときにサブドメインを聞いてきます。

* https://blog.nodejitsu.com/keep-a-nodejs-server-up-with-forever/
* https://github.com/balderdashy/sails/issues/455

###OpenShiftにデプロイする

OpenShiftにデプロイするにはちょっとした設定変更が必要です。: sppフォルダの中のconfig/local.jsを開きここに以下の行を加えてください。 

```
port: process.env.OPENSHIFT_NODEJS_PORT,
    host: process.env.OPENSHIFT_NODEJS_IP,
```

###DigitalOceanを使う

* https://www.digitalocean.com/community/articles/how-to-create-an-node-js-app-using-sails-js-on-an-ubuntu-vps
* https://www.digitalocean.com/community/articles/how-to-use-pm2-to-setup-a-node-js-production-environment-on-an-ubuntu-vps
* https://www.digitalocean.com/community/articles/how-to-host-multiple-node-js-applications-on-a-single-vps-with-nginx-forever-and-crontab

###Herokuにデプロイする

* SailsCasts: Deploying a Sails App to Heroku
* https://groups.google.com/forum/#!topic/sailsjs/vgqJFr7maSY
* https://github.com/chadn/heroku-sails
* http://dennisrongo.com/deploying-sails-js-to-heroku/#.UxQKPfSwI9w
* http://stackoverflow.com/a/20184907/486547

###AWSにデプロイする

* http://blog.grio.com/2014/01/your-own-mini-heroku-on-aws.html
* http://serverfault.com/questions/531560/creating-an-sails-js-application-on-aws-ami-instance
* http://bussing-dharaharsh.blogspot.com/2013/08/creating-sailsjs-application-on-aws-ami.html
* http://cloud.dzone.com/articles/how-deploy-nodejs-apps-aws-mac

###PM2を使う

* http://devo.ps/blog/2013/06/26/goodbye-node-forever-hello-pm2.html

###CloudControlにデプロイする

* https://www.cloudcontrol.com/dev-center/Guides/NodeJS/Sailsjs

###専門家の手助けを求める

最近、パワフルなアプリケーションにデプロイするのはだんだんと簡単になってきています。とは言え常にそれを自分でやる時間があるとは限りません。
Sails.jsは私（訳注：原著者）がテキサス州Austinで経営するNode.jsのコンサルタント会社であるBalderdashによってメンテナンスされています。もしあなたの会社が専門家の手助けを必要とする時はご連絡いただければ喜んでお手伝いします。デプロイはホントはそんなに難しいものではなく2〜3時間以上をかけるようなものではあえりません。

##スケーリング
直近の予定としてトラフィックの増大を想定しているなら（あるいはすでに多くのトラフィックがあるなら）多くの人のアクセスを受け入れられるようなスケーラブルなアーキテクチャでのセットアップを行いましょう。


###ベンチマーク

ほとんどの部分に関してSailsのベンチマークは他のConnectやExpress、Socket.ioのアプリケーションと全く同じです。このことはいくつ管異なる環境下で確認されており、直近のものはこれです。もしあなた自身でおこなったベンチマークの結果をシェアしてもいいならgithubひプルリクエストを送ってください。


###アーキテクチャ例

```
Sails.js server
                             ....                 
                    /  Sails.js server  \      /  Database (e.g. Mongo, Postgres, etc)
Load Balancer  <-->    Sails.js server    <-->    Socket store (Redis)
                    \  Sails.js server  /      \  Session store (Redis)
                             ....                 
                       Sails.js server
                       
```
###クラスタ環境へのデプロイに対応した設定

* モデルに使われているデータベース（MySQLやPostgres、Mongoなど）がスケーラブルなこと（シェーディングやクラスタリングがなされている）を確認して下さい。
* 共有のセッションストアを利用する設定をしてください。
    * Radisを使った実装がサポートされています。(config/session.jsのアダプタオプションをご確認ください)
* もしもSocketを使ってるなら:
    * 共有のソケットストアを利用する設定をしてください。
        * Radisを使った実装がサポートされています。(config/sockets.jsのアダプタオプションをご確認ください
        * 備考:もしソケットストアを利用する実装をしていない場合ロードバランサーでsticky sessionsを設定することが解決策になりえます。
* その他の依存関係にある部分が共有メモリに依存していないことを確認してください。

###Sailsのクラスタを複数台のサーバにアップロードする

* ロードバランサの後ろに複数個のインスタンス（つまりプロジェクトが動いているサーバ）をセットアップする。
    * それぞれのインスタンスでforeverを使ってSailsを起動する。
    * ドードバランサーに関してのさらなる詳細は: http://en.wikipedia.org/wiki/Load_balancing_(computing)
* ロードバランサーがSSLを終端するように設定する
    * そのためSails側でSSLを設定する必要はありません。（通信はすでに復号されています。）

