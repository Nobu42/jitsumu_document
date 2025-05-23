# DNSとSquidの連携に関する詳細設計まとめ

## 概要

このドキュメントは、SquidプロキシサーバとDNSサーバとの連携に関する設計方針をまとめたものです。主にFQDNによるアクセス制御、キャッシュ最適化、ログの可読性向上、および内部DNSの利用に関する設定を対象とします。

---

## 1. 目的

| 項目 | 内容 |
|------|------|
| 対象サービス | Squid（プロキシサーバ） |
| 目的 | DNSと連携してアクセス制御・キャッシュ制御・ログ強化を実現 |
| 重要性 | 高（誤設定によりアクセス制限・キャッシュ・ログ出力が不安定になる恐れあり） |

---

## 2. 主な連携内容

### 2.1 FQDNベースのACL（アクセス制御）

Squidの設定で、IPアドレスではなくホスト名ベースでの制御を行う。

```conf
acl blocked_sites dstdomain .example.com
http_access deny blocked_sites
```

> 解説: DNSを利用して`.example.com`のIPを解決し、その通信を遮断します。

---

### 2.2 キャッシュ対象URLにFQDNが含まれる

SquidはURL単位でキャッシュを管理するため、名前解決の結果によってキャッシュキーが影響を受ける。

```text
http://www.example.com/page1.html → FQDNの正確な解決が必要
```

---

### 2.3 ログの可読性向上（逆引きDNS）

ログファイルに出力されるIPをホスト名で表示することで解析がしやすくなる。

```conf
logformat custom %>a %dn %rm %ru
access_log /var/log/squid/access.log custom
```

> `%dn`: クライアントのホスト名（逆引きDNS）

---

## 3. 内部DNSサーバとの連携

### 3.1 独自ドメインを使っている場合の設定例

```conf
dns_nameservers 192.168.0.53 192.168.0.54
fqdncache_size 1024
```

| 項目 | 内容 |
|------|------|
| `dns_nameservers` | 使用するDNSサーバを明示的に指定 |
| `fqdncache_size` | FQDNキャッシュの最大エントリ数 |

> 注: `/etc/resolv.conf`と異なるDNSサーバを使いたい場合に有効。

---

## 4. 可用性とセキュリティ

| 課題 | 対応 |
|------|------|
| 動的IP/FQDNの変化 | DNSリフレッシュを有効にする |
| 不正なDNS応答の影響 | DNSサーバを信頼できるものに限定 |
| キャッシュ汚染 | DNSキャッシュの定期クリアを検討 |

---

## 5. 推奨設定まとめ（Squid）

```conf
# DNSサーバの指定
dns_nameservers 192.168.1.1 8.8.8.8

# DNSキャッシュサイズ
fqdncache_size 1024

# FQDNベースのアクセス制御
acl restricted dstdomain .restricted.local
http_access deny restricted

# ログフォーマットにホスト名を含める
logformat custom %>a %dn %rm %ru
access_log /var/log/squid/access.log custom
```

---

## 6. 参考

- [Squid official documentation](http://www.squid-cache.org/)
- [RFC 1034: Domain names - concepts and facilities](https://datatracker.ietf.org/doc/html/rfc1034)
- 社内DNS設計書（internal-dns-design.md）

---

## 7. 補足

今後、DNSサーバの冗長化やキャッシュDNSサーバとの連携が求められる可能性があります。要件に応じて設計を更新してください。

