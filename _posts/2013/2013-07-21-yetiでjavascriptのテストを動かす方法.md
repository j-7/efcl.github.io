---
title: YetiでJavaScriptのテストを動かす方法
author: azu
layout: post
permalink: /2013/0721/res3337/
dsq_thread_id:
  - 1514868893
categories:
  - javascript
tags:
  - javascript
  - test
---
[Yeti][1] はYahoo.comがメインで開発してるNode製のTest Runner的なCLIです。

Yeti自体はテストの構文とかは持っていなくて、[Test Frameworks][2]に書かれているようにQUnitやJasmine、MochaやYUI Testなどで書かれたテストを動かすツールになっています。

雰囲気的には[Testem][3]に似ていて、上の[Test Frameworks][2]で書いたhtmlページを用意して、

キャプチャー用のURLを作って、そこにテストしたいブラウザをキャプチャーして、テストを書いたhtmlを指定して実行させるという感じです。  
(意味的にあんまり変わらないですが、先にテスト用htmlを指定して、ブラウザにアクセスさせるという逆の手順もできます)

Yeti自体は数年前からあって、[Bunyip][4]とかでもインスパイアされてたりします。

## 動かしてみる

QUnitやJasmine、Mocha等好きなものが使えますが、今回は[Mocha][5]のテストを動かしてみます。

Mochaのテストのセットアップは、[Yeoman][6]の[Mocha generator][7]を使うと簡単にできるので今回はこれを使用します。

Yetiのチュートリアルを元に進めるので読んでおくと理解しやすいです。

*   [Yeti v0.2.24 &#8211; Quick Start][8]

作成したサンプルは以下に置いてあります

*   [azu/yeti-setup][9]

### インストール

Node環境は各自作っておいて下さい。

必要なものをnpm経由でインストールします。

    # Yoemanをインストール
    npm install -g yo grunt-cli bower
    # Mochaのテンプレートを作るGeneratorをインストール
    npm install -g generator-mocha
    # Yetiをインストール
    npm install -g yeti
    

これで必要なものは揃いました。

### テストのセットアップ

先ほど、[Mocha generator][7]をインストールしたので、 `$yo mocha` というコマンドが使えるようになっています。

    yo mocha
    

これを実行すると、現在のディレクトリに以下のようにmochaのテストのテンプレートを作ってくれます。

    現在のディレクトリ
    └── test
        ├── index.html
        ├── lib
        │   ├── chai.js
        │   ├── expect.js
        │   └── mocha
        │       ├── mocha.css
        │       └── mocha.js
        └── spec
            └── test.js
    

試しに、 `test/index.html` にアクセスするとmochaのテストが動くことがわかります。

### Yetiでテストの実行

一番、単純なオプションなしの `yeti` コマンドを使ってみます。

    yeti test/*.html
    

のように、実行したいテスト用のHTMLを指定します。(globが利用できます、また設定ファイルに分けることもできます。)

    Creating a Hub. Open `yeti --server` in another Terminal to reuse browsers for future batches.
    
    Waiting for agents to connect at http://192.168.1.3:9000
    ...also available locally at http://localhost:9000
    

テストを動かしたいブラウザで、 http://localhost:9000 にアクセスするとキャプチャされます。

    When ready, press Enter to begin testing.
      Agent connected: Chrome (28.0.1500.71) / Mac OS from 127.0.0.1
    

キャプチャされた状態で `Enter` を押すとテストが実行され結果が出力されます。

    ✓ Testing started on Chrome (28.0.1500.71) / Mac OS
    ✓ Agent completed: Chrome (28.0.1500.71) / Mac OS
    ✓ 1 tests passed! (0.29 seconds)
    

#### yeti &#8211;server

`yeti --server` を使うと、先にブラウザをキャプチャしておいて、 `yeti` コマンドを実行した段階で、テストが実行されて結果が表示されます。

[Buster.JS][10] 等の方式と同じです。

    yeti test/*.html
      Agent connected: Chrome (28.0.1500.71) / Mac OS from 127.0.0.1
    ✓ Testing started on Chrome (28.0.1500.71) / Mac OS
    ✓ Agent completed: Chrome (28.0.1500.71) / Mac OS
    ✓ 1 tests passed! (0.21 seconds)
    

#### .yeti.json

毎回、実行するテスト用HTMLを指定するのは面倒なので、  
`.yeti.json` という設定ファイルを作ってそこに書いておくことができます。

以下の内容の `.yeti.json` を作成して、プロジェクトのrootにおいておきます。

<div class="highlight">
  <pre><span class="p">{</span>
    <span class="nt">"basedir"</span><span class="p">:</span> <span class="s2">"."</span><span class="p">,</span>
    <span class="nt">"glob"</span><span class="p">:</span> <span class="s2">"**/test/*.html"</span>
<span class="p">}</span>
</pre>
</div>

rootに `.yeti.json` ある場合は、自動で読み取ってくれるので、

    $ yeti test/*.html
    

の代わりに

    $ yeti
    

を実行するだけで、上記の事と同様の結果が得られます。

### Sauce Labs連携で色んなブラウザで実行させる

[Sauce Labs][11] と連携する事で、iOS等のモバイルも含めた色々なブラウザでテストをCLIから動かすことができます。(実行はSauce Labs側でされるのでローカルには該当ブラウザはいらない)

詳しくは以下を読むといいかと思います。

*   [Testing JavaScript with Yeti and Sauce Labs WebDriver — Ryuichi Okumura][12]

#### [Sauce Connect][13] のインストール

Sauce Labs連携をするには[Sauce Connect][13]を使う必要があります。

[Testing JavaScript with Yeti and Sauce Labs WebDriver — Ryuichi Okumura][12] に書かれているように、 `Sauce-Connect.jar` を使えばできますが、

今回は、`Sauce-Connect.jar`のラッパーである、[saucelauncher][14]を使って行います。

[saucelauncher][14] は以下のようにしてインストール出来ます。

    npm install -g saucelauncher
    

[Sauce Labs][15] にアカウントを作ってログインすると、左下にAPI Keyを取得する所があるのでそれをコピーします。

<img src="https://efcl.info/wp-content/uploads/2013/07/Sauce-Labs-2013-07-21-01-01-36.jpg" alt="Sauce Labs 2013 07 21 01 01 36" title="Sauce Labs 2013-07-21 01-01-36.jpg" border="0" width="297" height="163" />

`$ saucelauncher` というコマンドが使えるようになっているので、 `saucelauncher --user [user:api_key] tunnel` という感じで、毎回API Keyを指定して使うのもいいですが、  
[.saucelabs.json][16] というファイルをホームディレクトリに作成しておくことでここから読み込んでくれるようになります。

[.saucelabs.json][16]は以下の様な形式です。

    {
        "username":"user名",
        "api_key":"APIKEY"
    } 
    

このファイルを作成した状態で、Sauce Labs連携を行います。

#### Sauce Labsでテストを動かす

簡単にまとめると以下の様な手順でテストを実行します。

1.  [Sauce Connect][13]でSauce Labsに接続します。
2.  `yeti --server --wd-url` で Sauce LabsとYetiを接続します。
3.  `yeti` コマンドでブラウザを指定してテストを実行します。

実際には以下の様なコマンドを叩きます。

    # Sauce Connect
    saucelauncher tunnel
    # Saunce Labs + Yeti
    yeti --server --wd-url http://[username]:[apikey]@ondemand.saucelabs.com:80
    # yetiでテストを実行
    yeti --browser "iphone/6/mac 10.8"
    

(yeti &#8211;server &#8211;wd-url にAPIKeyとか含めるのが少しだるい…)

`yeti --browser ブラウザ名/バージョン/OS` でブラウザを指定してテストを実行できます。

上記の例では、Mac OS 10.8のiPhoneのiOS6上でテストを実行した結果が得られます。

    yeti --browser "iphone/6/mac 10.8"
    Found 1 file to test.
      Agent connected: Mozilla/5.0 (iPhone; CPU iPhone OS 6_0 like Mac OS X) AppleWebKit/536.26 (KHTML, like Gecko) Mobile/10A403 from 192.168.1.3
    ✓ Testing started on Mozilla/5.0 (iPhone; CPU iPhone OS 6_0 like Mac OS X) AppleWebKit/536.26 (KHTML, like Gecko) Mobile/10A403
    ✓ Agent completed: Mozilla/5.0 (iPhone; CPU iPhone OS 6_0 like Mac OS X) AppleWebKit/536.26 (KHTML, like Gecko) Mobile/10A403
    ✓ 1 tests passed! (38 seconds)
      Agent disconnected: Mozilla/5.0 (iPhone; CPU iPhone OS 6_0 like Mac OS X) AppleWebKit/536.26 (KHTML, like Gecko) Mobile/10A403 from 192.168.1.3
    

ブラウザの指定方法は以下に詳しく書かれています。

*   [Browser launching][17]
*   [Yeti 0.2.21 Released &#8211; YUI Blog][18]

## おわり

[Yeti][1] は比較的シンプルな感じで、[Testem][3] と似てる部分はありますが、よりシンプルな感じです。

*   [Yeti][1] 
*   [azu/yeti-setup][9]
*   [How to run Yeti with QUnit testing — Ryuichi Okumura][19]

 [1]: https://github.com/yui/yeti "yui/yeti"
 [2]: https://github.com/yui/yeti#test-frameworks "Test Frameworks"
 [3]: https://github.com/airportyh/testem "airportyh/testem"
 [4]: http://www.thecssninja.com/javascript/bunyip "Bunyip"
 [5]: http://visionmedia.github.io/mocha/ "Mocha"
 [6]: http://yeoman.io/ "Yeoman"
 [7]: https://github.com/yeoman/generator-mocha#readme "Mocha generator"
 [8]: http://yeti.cx/quick-start/index.html "Yeti v0.2.24 - Quick Start"
 [9]: https://github.com/azu/yeti-setup "azu/yeti-setup"
 [10]: http://docs.busterjs.org/en/latest/ "Buster.JS"
 [11]: https://saucelabs.com/home "Sauce Labs: Mobile and Web App Testing Tools For Developers"
 [12]: http://www.okuryu.com/2013/05/31/testing-javascript-yeti-saucelabs-webdriver.html "Testing JavaScript with Yeti and Sauce Labs WebDriver — Ryuichi Okumura"
 [13]: https://saucelabs.com/docs/connect "Sauce Connect"
 [14]: https://github.com/airportyh/saucelauncher "saucelauncher"
 [15]: https://saucelabs.com/account "Sauce Labs"
 [16]: https://github.com/airportyh/testem/tree/master/examples/saucelabs ".saucelabs.json"
 [17]: https://github.com/yui/yeti#browser-launching "Browser launching"
 [18]: http://www.yuiblog.com/blog/2013/04/11/yeti-0-2-21-released/ "Yeti 0.2.21 Released - YUI Blog"
 [19]: http://www.okuryu.com/2012/11/28/how-to-run-yeti-with-qunit-testing.html "How to run Yeti with QUnit testing — Ryuichi Okumura"
