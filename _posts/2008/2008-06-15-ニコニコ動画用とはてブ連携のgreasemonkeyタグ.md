---
title: 'ニコニコ動画とはてブを連携して投稿するGreasemonkey[タグ]'
author: azu
layout: post
permalink: /2008/0615/res218/
SBM_count:
  - '00002<>1355370024<>0<>0<>1<>1<>0'
dsq_thread_id:
  - 304386956
categories:
  - Greasemonkey
tags:
  - Greasemonkey
  - はてブ
  - ニコニコ動画
---
ニコニコ動画上の動画からはてなブックマークをするときに便利なGreasemonkeyスクリプトを書いてみました。(やや自分用に)

機能

1.  ニコニコ動画のロックタグ(固定タグ)をそのままはてブのタグにしてポストできる。
2.  投稿者の説明をそのままはてブのコメント(内容)としてポストできる。(設定でON,OFFできます)  
    var descshow = true;//投稿者の説明を入れるならtrue,　いれないならfalse

*   [nicohatebu_tagpost.user.js][1]

インストールした状態でニコニコ動画の動画再生ページにいくと

<img src="https://efcl.info/wp-content/uploads/a.png" alt="" width="374" height="100" />

マイリストの近くに**はてブ**というリンクがあるのでそれをクリックすると、  
普段のはてブ投稿画面にタグなどが記載された状態で表示されます。

<img src="https://efcl.info/wp-content/uploads/hatebu1a.png" alt="" width="362" height="169" />

最初はブックマークレットにしようと思いましたが、よく分からなかったのでGreasemonkeyにしてみました。  
まだ、微妙なところ(タグにカテゴリとか投稿者コメントがあるのが)があるのでそこを少し調整する予定。

 [1]: https://efcl.info/wp-content/uploads/2008/06/js/nicohatebu_tagpost.user.js
