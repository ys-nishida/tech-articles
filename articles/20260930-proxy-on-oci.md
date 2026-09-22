---
title: "Proxy on OCI"
emoji: "👏"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: []
published: false
---

# はじめに
* こちらの続編記事です
  * https://future-architect.github.io/articles/20250623a/
* あれから1年近く経過し、Youtube を見るための儀式？は習慣化されたため、自動化する事にしました

# 本記事の概要
* 要件 : 子供の youtube 視聴は『生活の最低限やるべきことをやってから』にしたい
* 設計 : Pac ファイルで宛先を Proxy に設定し、その Proxy の起動停止で見れる見れないを制御
* 実装 : [こちらに公開しています](https://github.com/ys-nishida/proxy-on-oci)

# 書くこと
* [前回記事](https://future-architect.github.io/articles/20250623a/)との差分がメイン
* ついでに、OCI always free のいいところ！

# 

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
