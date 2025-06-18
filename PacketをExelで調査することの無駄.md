# パケットキャプチャをExcelで分析するのは無駄？

## ❌ なぜExcelでの分析が非効率なのか

| 問題点 | 詳細 |
|--------|------|
| **精度低下** | CSV出力で列ズレ、データ欠落、時刻の丸めなどが発生しやすい |
| **再現性の欠如** | Wiresharkの再解析機能（Follow TCP Stream等）が使えなくなる |
| **手間がかかる** | GUIで一瞬のフィルタ作業が、Excel＋SQLだと煩雑になる |
| **目的に合っていない** | ExcelやSQLは「統計処理」向きであり、**通信の振る舞い分析**には不向き |
| **GUIの利点を無視** | Wiresharkのプロトコル解析・可視化機能が全て無駄になる |

> 💡 **結論：SQLで集計したいならNetFlow/sFlowのような「集計向けデータ」を使うべき**

---

# 効率よく輻輳の原因を特定するための調査手法一覧

## 🧭 調査ステップ別に適した手法

### ① ネットワーク全体のトラフィック傾向を掴む
- SNMP（例：`snmpwalk`, Zabbix, Cacti など）
- インタフェース統計（`ip -s link`, `netstat`, `iftop`, `nload`）

### ② 「どの通信」が多いかを知る
- NetFlow/sFlow/IPFIXを利用（例：nfdump, pmacct）
- トップトークアプリケーション、ポート、IPを抽出

### ③ TCPの再送、遅延、スロースタート、輻輳制御の挙動を見る
- Wireshark or tshark
  - TCP Stream再構築（Follow TCP Stream）
  - TCP Retransmission/ZeroWindowの検出
  - RTT/ウィンドウサイズ確認

### ④ スイッチ/ルータの転送遅延やQoSの問題を調査
- QoSマーク（DSCP/CoS）の有無をパケットで確認
- ネットワーク機器のログ確認（`show queue`, `show interface`）

### ⑤ 輻輳の影響が出ているアプリを特定する
- アプリのログと突合（HTTP 500系、DB接続タイムアウトなど）
- APMツール（例：Datadog, Prometheus+Grafana, Zabbix）

---

## 🔧 具体的なツールの選び方

| 調査対象 | 推奨ツール |
|----------|------------|
| インタフェース使用率 | SNMP, `ip -s link`, `netstat`, `iftop` |
| 通信量ランキング | NetFlow/sFlow + nfdump/pmacct |
| パケットレベルの挙動 | Wireshark, tshark |
| ログベースの相関分析 | APM, ログ監視ツール（Fluentd, Lokiなど） |

---

## ✅ 結論

> パケットキャプチャは「ピンポイントで深掘り」するツール。  
> SQLで全体を見たいなら、**NetFlowやログ集計ツールを使う方が遥かに効率的。**

