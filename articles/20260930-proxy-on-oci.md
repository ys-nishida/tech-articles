---
title: "動画視聴制御 Proxy を OCI always free で作る"
emoji: "👦"
type: "tech" # tech: 技術記事 / idea: アイデア
topics:
  - "Terraform"
  - "OCI"
  - "Network"
  - "Docker"
published: false
---

# はじめに
* こちらの続編記事です
  * https://future-architect.github.io/articles/20250623a/
* あれから1年近く経過し、Youtube を見るための儀式？は習慣化されたため、自動化する事にしました

# 本記事の概要
* 要件 : 子供の youtube 視聴は『生活の最低限やるべきことをやってから』にしたい
* 設計 : Pac ファイルで宛先を Proxy に設定し、その Proxy の起動停止で見れる見れないを制御
* 実装 : OCI Always free + HCP Terraform で無料＆簡単に運用

# 書いた動機
* 概ね習慣化できた事、出社の日もある中で [前回記事](https://future-architect.github.io/articles/20250623a/)の手動運用が厳しくなってきた事で、自動化しようと思いはじめてきました
* 仕事で触り切れなかった HCP Terraform と、OCI をちゃんと使ってみたい！

# 主な構成要素
* あくまでも、無料の範囲内でやっています。
  * !ここに構成図を乗せる。何で書こうか？claude に手伝ってほしいが

* github gist
* VM on OCI (always free)
  * cron で起動停止
* squid コンテナ
* HCP Terraform

# 苦労したポイント
## 予約ipを消さなくさせる
* OCI のリソースが public ip 発行を巻き込む
  * user dataでアタッチしようとして、ociがない
  * ip がないから oci を install 出来ない、、、
* じゃあ逆に、ephemeral で ip を払い出して、user data 内でアタッチしようとすると
  * oci の権限管理の壁 タグであれこれ設定が必要
    * 頑張った！
  * でも user data の中で、oci login コマンドが必要そう
    * ブラウザが必要そう、、、完全ワンコマンドが無理。。諦めた

* 発行済 IP のアタッチは web コンで人がやる。
* ssh ログインして、start up sh を動かしてセットアップを自動化させる

* いっその事 hcp terraform やめて、gha でやるのはどうだったのだろうか？
  * 手動実行前提なら、gha 内でコマンド実行すればよかったのではないだろうか？
  * 趣旨から外れるのでいったん没にしたが、この案でもよかったのかもしれない

## 認証やめた
* かえって脆弱


# 実装内容
* PCの設定 : 前回記事と変わらず、プロキシに Pac ファイルを指定
!画像

* PACファイルの内容

```pac
function FindProxyForURL(url, host)
{
    // . が含まれない場合。つまりローカルドメイン
    if (isPlainHostName(host))
        return "DIRECT";

    // ローカルIP宛の通信は direct
    if (isInNet(host, "192.168.0.0", "255.255.0.0"))
        return "DIRECT";

    // Proxy 経由の通信。動画配信は別のFQDN
    var proxy = "PROXY [予約 IP アドレス]:10080";
    var domains = [
        "youtube.com",
        "*.youtube.com",
        "youtu.be",
        "youtubekids.com",
        "*.youtubekids.com",
    ];
    for (var i = 0; i < domains.length; i++) {
        if (shExpMatch(host, domains[i])) {
            return proxy;
        }
    }

    // どこにも合致しない場合は、諦めて direct 接続
    return "DIRECT";
}
```

### OCI 側の実装
* [こちら](https://github.com/ys-nishida/proxy-on-oci) に公開
* Public IP が、VM の再作成に道連れをされて消されない様に、基底部分は `common` として分離させました
  * 再作成を頻繁にするリソースを個別リソース、今回で言えば `proxy-vm` として切り出しています
* OCI のリソースは、以下のように

### HCP Terraform の構成
* 実装に合わせて、common と proxy-vm は、workspace を分離する形で構成しています
  * ずっとローカル実行していた派閥なので、フォルダ分離だけでは出来なくて、各フォルダとworksaceを揃えなければ同じProjectで構成できないことを知りました
  * variable は、どちらも利用するため、共通化しています（分けるの面倒だった）
! 画像を2つ


# やろうとしてできなかったこと。頑張ったけどあきらめた！
* immutable
  * 毎回プロビジョニングするのはあまりに時間や計算機コストが多すぎた
* terraform apply 一発化
  * oracle vm を使っても、oci コマンドが入っていない
  * oci コマンドが入ってないから、ipアドレスをアタッチ出来ない
  * ip アドレスをアタッチ出来ないからoci コマンドがインストールできない
  * 鶏卵問題、、、
* フローログ取得
  * 想定しているipからしか来てないことを定期的に確認しようと思ったが、、
* 権限系
  * user data で、バケットから sh を取ってくる方法
  * タグでやる必要がある
  * そのタグをプロビジョニングするのに強い権限が必要
  * hcp terraform に協力な権限を付与
  * 嫌だ、、、

# proxy on oci

```
# プロビジョニングも出来なかった
    # squid.conf を読み込んで、setup_squid.sh.tftpl へ流し込む
    # 初期セットアップはしない。やっぱり VM 上の自動セットアップは複雑
    # user_data = base64encode(templatefile("./user_data/setup_squid.sh.tftpl", {
    #   proxy_user = "myuser"
    #   proxy_pass = "YourSuperSecurePassword123!"
    #   squid_conf = file("./user_data/squid.conf")
    #   # proxy_reserved_ip_id = oci_core_public_ip.proxy_reserved_ip.id
    #   proxy_reserved_ip_id = local.common.proxy_reserved_ip_id
    # }))
```
