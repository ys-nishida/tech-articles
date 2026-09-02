---
title: "Professional Cloud Network Engineer(PCNE) 合格体験記"
emoji: "📝"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: 
  - "googlecloud"
  - "network"
  - "claude"
published: false
publication_name: "dgtech"
---

# はじめに
こんにちは、SRE室の西田です
今回は、Google Cloud の Professional Cloud Network Engineer（= PCNE）の資格更新をしてきました。資格対策は今まで模擬試験を軸にやってきましたが、子どもの中学受験のフォローから気づきを得て、今回は生成AIを活用しました。
* 結果、これまで実働12時間ほどかかっていた対策が、3-4時間で終わりました。その体験談をまとめます
* ついでに、試験はそのプロダクトが何を（どこを）目指しているのか？を感じ取れる良い機会なので、私が感じた点も共有します

# 想定読者
* クラウドのネットワークに興味がある方
* 資格対策に興味がある方

# 書くこと・書かないこと
* 書くこと:
  * 資格対策で Claude を利用してみて得られた知見・改善点
  * PCNE の出題傾向から感じた目指している姿と、その背景の歴史的な話
* 書かないこと:
  * PCNE の出題内容（Appendix で分類までは書きますが、内容には焦点は当てません）

# 前段のお話 : PCNE（Professional Cloud Network Engineer）について
![Professional Cloud Network Engineer](https://raw.githubusercontent.com/ys-nishida/tech-articles/refs/heads/main/articles/data/20260821-google-pnce-exam/image.png =180x)

* Google Cloud の認定資格の構造は[こちらに](https://cloud.google.com/learn/certification)
* AWS の Advanced Networking - Specialty（廃止予定）の Google Cloud 版の資格です
* 基本的にこういった資格は、ベストプラクティスを答える問題がメインなので、どういう要望に答えるために作ったサービスなのかを把握するのにはとても効率が良いと思っています

# 実施した試験対策
* この手の試験対策は普段は udemy で模擬試験を購入して、、、という流れが多いと思います
* 中学受験で苦手な形式の問題をピンポイントで作問するのを AI にやってもらっていたことから気づきを得て、今回は Claude に出題者をやってもらいました。サンプルで画像を載せます
* 「前回受験時からの差分」を起点に出題範囲を絞り込み、そのまま試験形式で出題 → 解説の深掘り、という流れです

| ① 前回受験からの差分で出題範囲を予想させる | ② 出題範囲をさらに深掘りさせる |
| --- | --- |
| ![出題範囲の予想](https://raw.githubusercontent.com/ys-nishida/tech-articles/refs/heads/main/articles/data/20260821-google-pnce-exam/sample-1.png) | ![出題範囲の深掘り](https://raw.githubusercontent.com/ys-nishida/tech-articles/refs/heads/main/articles/data/20260821-google-pnce-exam/sample-2.png) |
| ③ 試験形式（シナリオ+4択）で出題させる | ④ 解説を根掘り葉掘り聞く |
| ![試験形式での出題](https://raw.githubusercontent.com/ys-nishida/tech-articles/refs/heads/main/articles/data/20260821-google-pnce-exam/sample-3.png) | ![解説の深掘り](https://raw.githubusercontent.com/ys-nishida/tech-articles/refs/heads/main/articles/data/20260821-google-pnce-exam/sample-4.png) |

### 良かった点
* 疑問に感じたこと、なんでそういう回答になるのか、他の選択肢にしたらどういうことになるのか？などが、根掘り葉掘り聞けます
* これはかなり機能に対して解像度が上がりました。わからないところだけをピンポイントで確認できるので、時短できます

### 悪かった点
* 初めてだったので、プロンプトの精査が甘くて、問題になっていないものもありました
  * 正しい選択肢だけ、太文字が非常に多い
  * 正しい選択肢だけ、明らかに長い
  * 正しい選択肢だけ、非常に細かいことが書いてある
  * 正しい選択肢が、全部 B

| ⑤ 作問バイアスを指摘して是正を依頼する |
| --- |
| ![作問バイアスの指摘](https://raw.githubusercontent.com/ys-nishida/tech-articles/refs/heads/main/articles/data/20260821-google-pnce-exam/sample-5.png) |

* 「上記に気をつけて出題してください」ときちんと指示すると、いい感じになりましたが、それでも途中から B に偏ってました

# 結果 : 所要時間が1/3以下に
* 無事に合格して、資格を更新できました
* 昔のやり方だと、実働で12時間ほど（1日3時間 × 4日）かかっていましたが、今回は **3-4時間** 程度で対策完了です
* 模擬試験を購入するやり方だと、十分理解している問題が多々あり時間が取られること、回答の解説が不十分で理解するために公式ドキュメントをかなり読まないと答えが見えないこと、などがありました
* 今回は、自分のステータスを加味した上でピンポイントで学習しているので、それは当然に早いです！！
* なので、いろんな応用が利くと思います
* 例えば冒頭で自分のバックグラウンドを伝えるだけで、絞り込みの起点が変わります

```text
私は AWS の Advanced Networking - Specialty を保有しています。今回は PCNE に挑戦したいです。
Google Cloud との差分を中心に、学習していくのが効率が良いと思っています。
どういう段取りで学習するべきなのか、計画を立てた上で、模擬試験ベースで確認していきたいです
* 選択肢は4択にしてください
* 正答が見た目だけでわからないように、以下を意識してください
  * 太文字は使わない、長さは揃える、粒度を揃える、正答は ABCD に分散させる
```

```text
私は Google Cloud の Professional Cloud Architect を保有しています。今回は PCNE に挑戦したいです。
実務で使っていたのは、GCE, VPC, FW, Cloud Run, GCR などの基本的な構成でした。
ネットワークは専門の別チームが存在していたので、軽く触れる程度の理解度です。
[以降は、同上]
```

# 受験してみて、私が感じたこと
### 企業の WAN（MPLS）の代替
* 企業の WAN（MPLS）に代わり、それを Google Cloud のバックボーンに代替することを目指しているように感じました
  * 理由は、NCC の利用用途の出題が最多だったからです
  * 国内の本社支社店舗の WAN ではなく、複数リージョンの国際的な WAN の方です
  * 従来だと、オーバーレイ NW を作って、経路の動的切り替えをしないとうまくルーティングの制御ができなかったですが、シンプルなアンダーレイのみの世界で実現できるようになっています
    * DC ルータの LP 値によっては、ホットポテトルーティングになるのは従来通りでした。非対称ルーティングは気にしすぎないこと
  * SASE 界隈ではよくある話ですが、Google の最寄り POP を掴んでリージョン越えができるのでレイテンシが低い

### 社内システムのリフト
* 外部向けのサービス開発ではなく、社内システムのリフトも力を入れ始めた印象を持ちました
  * GKE の VPC-native cluster における IP 大量消費問題への解決方法
    * 随分前から現場では同じことを構成していましたが、ip-masq-agent の ConfigMap を設定することで、宛先に応じて NAT する IP を変える、という話です。
      * 対外部 pod → Cloud NAT → インターネット
      * 対内部 pod → Node IP → 内部のサーバ
    * Routes-based cluster をサポートしなくなった当初は、GKE 用のエイリアス IP の CIDR 範囲が自動的に Peering で経路広報されてしまい、10系アドレス宛の通信が吸い取られる、という事故が結構ありましたよね
  * オンプレとの相互名前解決の話も多かったです
    * Google Cloud → オンプレ : Forwarder を設定し、`35.199.192.0/19` を内部に広報する & FW を開ける
    * オンプレ → Google Cloud : インバウンドサーバーポリシーを作成し、条件付きフォワーダを設定
  * ただ、そうだとすると、内部 HTTPS LB が IP 制限をかけられないという話は未だ健在なので、そこはなんとかして欲しいですね
    * https://qiita.com/ys_nishida/items/dc57862f18865750d8e6

### その他
* PCA（Professional Cloud Architect）の更新の際は、半分以上が生成AIの問題だったんです。ですが、PCNE は生成AIの出題が全くありませんでした
  * Vertex AI との通信の秘匿化やレート制限などの構造を問う問題はあってもよかったと思います

# まとめ
* Claude を活用し大幅に時短しました(実働12 → 3-4時間)
* 効いたのは、範囲を絞る2つの軸です
  * 前回受験からの差分 : 試験の改訂と製品アップデートから、狙われる領域を割り出す
  * 自分のバックグラウンド : 理解が浅いところだけをピンポイントで
* ただし、プロンプトを作り込まないと作問バイアスが出るので、そこだけは手を抜けません
* そして資格対策はそのプロダクトが今どこを目指しているかを読み取る良い機会でもあります
  * 今回で言えば、企業の WAN の代替と、社内システムのリフト
* 当社では Google Cloud の利用はあまり多くないですが、BigQuery を中心としたワークロードはそれなりに存在するので、社内で有識者が増えてくるといいな、と思ってついでに記事にしてみました！資格補助もしてもらえるので、取得しやすい環境が整っています！

# Appendix（参考） : 出題の傾向
### NCC関連
* 特定のルートだけ広報するか、特定のルートだけ除外して広報するか
* SD-WAN ソリューションを使っている場合の hub プロジェクトの構成方法

### GKE関連
* ip-masq-agent の構成方法
  * Cloud NAT 経由 or Node の IP で NAT する場合の使い分け
  * pod からの通信 or pod への通信ができない場合の原因

### PSC関連
* Private Google Access → PSC が推奨となっているケース
* GKE Control Plane や、他プロジェクトのサービスへのアクセスなど

### DNS関連
* オンプレとの双方向での名前解決

### その他
* エラー解析系
  * そもそも通信できない場合 : Connectivity Tests
  * 通信可能だが遅い場合 : パフォーマンス ダッシュボード
  * BGP の経路が消失している原因調査
* Web Proxy 関連の問題（宛先を絞りたい、というケース）
* 各種 LB の使い分け
  * ソース IP が保持できるもの（パススルー NLB）、TLS が終端できるものなど、L4, L7 の差異を問われる系統
* パケットミラーリング、Shared VPC, Cloud CDN, Cloud Interconnect などは通常通り

# 参考文献
* [Professional Cloud Network Engineer 試験ガイド](https://cloud.google.com/learn/certification/guides/cloud-network-engineer/?hl=ja)
* [Professional Cloud Network Engineer 公式練習問題](https://cloud.google.com/certification/practice-exam/cloud-network-engineer?hl=ja)
* [NCC overview](https://cloud.google.com/network-connectivity/docs/network-connectivity-center/concepts/overview)
* [IP masquerade agent](https://cloud.google.com/kubernetes-engine/docs/concepts/ip-masquerade-agent)
