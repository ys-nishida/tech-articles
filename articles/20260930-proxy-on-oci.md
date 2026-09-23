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
* 設計 : Pac で youtube の宛先を Proxy にして、その Proxy の起動停止で見れる見れないを制御
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

# 実装内容
* PCの設定 : 前回記事と変わらず、プロキシに Pac ファイルを指定
![Proxy setting](https://github.com/ys-nishida/tech-articles/blob/main/articles/data/20260930-proxy-on-oci/PC_proxy_setting.png?raw=true)

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
    var proxy = "PROXY [OCI 予約 IP アドレス]:10080";
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
  * variable は、どちらも利用するため、共通化しています（=分けるの面倒だった）

![HCP_Terraform_workspace](https://github.com/ys-nishida/tech-articles/blob/main/articles/data/20260930-proxy-on-oci/hcp_terraform_workspace.png?raw=true)

![HCP_Terraform_variable_set](https://github.com/ys-nishida/tech-articles/blob/main/articles/data/20260930-proxy-on-oci/hcp_terraform_variable_set.png?raw=true)

# 苦労したポイント
* 上記の実装になった背景と、苦労したポイントを書いておきます

## 予約 Public IP を VM 再作成でも保持させる事！
* これが本当に悩みました。すごい悩みました、この記事で一番の苦労点です。
* OCI の予約 Public IP のリソースの構造上、予約IP側にアタッチするVMを持つ構造なんです。以下の構造
```
resource "oci_core_public_ip" "test_public_ip" {
	#Required
	compartment_id = var.compartment_id
	lifetime = "RESERVED"
  ...

  private_ip_id = [VM の Private IP の OCID を指定する]
}
```
* 普通にコードを書くと、VM作成後に Public IP が作られる構造になります。よって、VM を削除すると public IP も巻き込まれて消える構造になっています
  * AWS/GCP と違って、**attachment リソースがない**ので巻き込まれて消えます
* 業務でもこのユースケースはよくあり、困ったことがなかったのですが、OCI はなかなかに難しいですね

#### 作戦1 : VM 作成時の起動スクリプト(user data)でコマンドでアタッチすればいいのでは？
* 予約IPはアタッチせずに独立リソースにしておく。VM作成時のshでアタッチコマンドで自分でアタッチさせればいいのでは？ -> oci コマンドがインストールされていない！
    * oci をインストールするコマンドを先に入れる -> public ip が付与されていないのでインストールできない
      * NAT を作ればいいのでは？ -> 有料でした
      * ephemeral public IP をアタッチしておけばいいのでは？ -> 今度は oci login が出来ない
        * oci login は、ブラウザアクセスが必要そうだったので、全自動は無理そうでした
    * oracle 純正の VM なら入っているのでは？ → 入ってませんでした。無料OSの中には該当なし
* 起動スクリプト作戦は、すべての選択肢で行き詰ってしまいました

#### 作戦2 : 外でコマンド実行すればいいのでは？GHA とか
* HCP Terraform が、任意のコマンドを打てない事も問題でした。なので、GHA で完結させる案も有力かもしれません
* GHA 使うなら、HCP Terraform を使う意味がなくなってきたので、趣旨から外れたため没にしました
* が、結果的にはこの案が一番スマートだったかもしれません

#### 作戦3 : apply だけで完結をあきらめる -> 採用
* 発行済 IP のアタッチは web コンで人がやる。
* ssh ログインして、start up sh を動かしてセットアップを自動化させる

## 認証プロキシ化する
* 無料で簡単に出来る範囲だと、Basic 認証を使う事になります。
* ただ、プロキシの Basic 認証は、PWが平文でインターネットを通ることになります
  * パケットキャプチャをすると普通に base64 decode すれば見れます
* ID/PW をプロキシ専用のものを発行して、PC に PW を保存しておけば少しマシだとは思います。
* 思いますが、利用者が子供だと考えた際、何か操作を間違えて認証プロンプトが出てきた際、普通に PC の ID/PW を入力してしまい、漏洩してしまうリスクがかなり高いと考えたため、結果的に脆弱になると考えて、没にしました

# その他、やろうと思ったがあきらめたこと
* 最初は、docker compose ではなく、VM リソースの作成・削除をスケジュール化する、を構想していました
  * セットアップに時間がかかりすぎる事や、リソース枯渇に伴うエラー時の対応など、考慮点が多いので諦めました
* terraform apply のみでセットアップ終了
  * 上記の"苦労したポイント"の通りですが、諦めました
* フローログ取得
  * 想定しているipからしか来てないことを定期的に確認しようと思いましたが、有料なので諦めました
