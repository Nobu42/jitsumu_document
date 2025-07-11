# Linuxサーバ障害対応一覧

**ハードウェア系障害**

  ---------------------------------------------------------------------------------------------------
  **種類**       **内容**                                      **対処の方向性**
  -------------- --------------------------------------------- --------------------------------------
  ディスク故障   HDD/SSDの物理故障（I/Oエラー、SMARTエラー）   RAID構成・定期バックアップ・監視

  メモリエラー   ECCで訂正できないレベルのビット化け           memtestで確認・メモリ交換

  電源断         停電、UPSバッテリ切れ                         UPS設置・APC連携で自動シャットダウン
  ---------------------------------------------------------------------------------------------------

**OSレイヤの障害**

  --------------------------------------------------------------------------------------------
  **種類**               **内容**                         **対処の方向性**
  ---------------------- -------------------------------- ------------------------------------
  ブート失敗             カーネルパニック、grub設定ミス   LiveCDで修復、grub再インストール

  ファイルシステム破損   ext4/xfsの破損 → fsck起動        定期fsck・UPS・LVMスナップショット

  ログイン不可           SSH鍵やPAM設定のミス             コンソール接続 or
                                                          セーフモードから修復
  --------------------------------------------------------------------------------------------

**ネットワーク関連**

  ----------------------------------------------------------------------------------
  **種類**         **内容**                       **対処の方向性**
  ---------------- ------------------------------ ----------------------------------
  ネットワーク断   ケーブル断線、NICトラブル      IPMI/iLOなどでリモート確認

  IP重複           DHCPミスや固定IPの競合         設計見直し、ARP確認

  name解決不可     DNS設定ミス、内部DNSトラブル   resolv.conf、DNSキャッシュの確認
  ----------------------------------------------------------------------------------

**アプリケーション・サービス系**

  -------------------------------------------------------------------------------------------
  **種類**             **内容**                             **対処の方向性**
  -------------------- ------------------------------------ ---------------------------------
  サービス起動失敗     systemdユニットの失敗・ポート競合    journalctl -xe でログ確認

  設定ミス             nginx, Postfixなどでtypoや構文ミス   \--testオプションなどで事前検証

  セキュリティ誤設定   iptables/firewalldでポート閉鎖       nmapやssで疎通確認
  -------------------------------------------------------------------------------------------

**セキュリティ・運用系**

  --------------------------------------------------------------------------------
  **種類**             **内容**                     **対処の方向性**
  -------------------- ---------------------------- ------------------------------
  root領域の容量逼迫   /var/log肥大化、/tmpあふれ   du -sh \*などで原因調査

  不正アクセス         SSH総当たり、Web改ざん       fail2ban・ログ監視・侵入検知

  cron暴走             無限ループジョブ             ps, top, systemctlで負荷確認
  --------------------------------------------------------------------------------

**ヒューマンエラー（実は一番多い）**

  -----------------------------------------------------------------------
  **ケース**                   **説明**
  ---------------------------- ------------------------------------------
  rm -rf /                     誤操作やスクリプト暴走で全消去

  ネットワーク遮断スクリプト   firewalldやiptablesで自分を閉じる

  設定ファイルのtypo           サービス再起動時に気づくことが多い
  -----------------------------------------------------------------------

**対策のキモ**

- 定期バックアップと自動リストアテスト（Baculaやrsnapshotなど）

- 監視（Zabbix, Prometheus, Nagiosなど）＋アラート通知

- 設定管理（Ansibleなど）で「戻せる構成」

- 検証環境での事前テスト

- ログ監視と整理（logrotate）

**障害発生時の初動対応チェックリスト**

  -------------------------------------------------------------------------------------------------------
  **項目**                           **チェック内容**
  ---------------------------------- --------------------------------------------------------------------
  **冷静さを保つ**                   焦らず、手を止めて、まず状況整理

  **時刻の確認**                     障害発生の時刻、きっかけ（作業やイベント）をメモ

  **誰が何をしたか記録**             操作ログ、システムログ、人物の行動を記録

  **影響範囲の確認**                 どのサービス・サーバに影響があるか？（DBだけ？ネットワーク全体？）

  **ログを確認**                     journalctl, /var/log/messages, アプリログなどを調査開始

  **再現性の確認**                   一時的か、再起動で直るのか、完全に止まっているのか

  **応急対応の実施**                 可能ならサービス再起動、切り戻し、フェールオーバーなど

  **上司・関係者への連絡**           初報を共有：「何が、どの範囲で、いつから」

  **スクリーンショット・ログ保存**   後の調査用にエビデンスを取る（scriptコマンドなども便利）

  **変更履歴を確認**                 Git / Ansible / 手作業ログなど。変更との関連性を検討

  **二次災害防止**                   作業停止、ネットワーク遮断、cron停止など予防策を講じる
  -------------------------------------------------------------------------------------------------------

**一般的なトラブルシュートの流れ**

コピーする編集する

① 現象を整理する（いつ・どこで・誰が・何が）

↓

② 影響範囲を把握する（ネットワーク単位か、1台だけか）

↓

③ 「最近の変更」を洗い出す（config更新、apt/yum updateなど）

↓

④ ハード・リソースをチェック（CPU, メモリ, ストレージ, ネット）

↓

⑤ サービスの状態確認（systemctl, ps, netstat/ss）

↓

⑥ ログを見る（/var/log/, journalctl, アプリログ）

↓

⑦ 切り分ける（ping → DNS → ポート → アプリ）

↓

⑧ 既知障害か確認（社内Wiki、Red Hatナレッジ、Google）

↓

⑨ 必要に応じてロールバック・仮復旧・再起動

↓

⑩ 恒久対応・原因分析（再発防止策・ドキュメント化）

**切り分けのヒント（ネットワーク障害なら）**

- ping 8.8.8.8（外には出られるか？）

- ping デフォルトGW（ローカルはOKか？）

- cat /etc/resolv.conf（DNSが正しいか？）

- curl http://localhost（ローカルアプリ生きてる？）

- ss -tlnp や firewalld/iptables 確認（ポート開いてるか？）

AnsibleやBashで「初動診断スクリプト」を自動化する

**Linuxサーバーのよくある障害と対応フロー**

**ケース①：SSH接続できない**

**想定原因：**

- ネットワーク断

- sshd サービス停止

- ファイアウォール/iptablesの遮断

- ポート変更 or SELinux制限

**🔍 トラブルシュート手順：**

**① Pingで死活確認**

bash

コピーする編集する

ping \<サーバーIP\>

- 応答なし → ネットワーク断・ケーブル・NIC・VLANなど

**② ポートスキャン**

bash

コピーする編集する

nmap -p 22 \<サーバーIP\>

- 開いてない → ポート遮断 or sshd起動していない

**③ ローカルでsshdステータス確認**

（コンソール接続できる前提）

コピーする編集する

systemctl status sshd

- inactive や failed → 起動

systemctl start sshd

**④ sshdのポート番号変更を確認**

grep Port /etc/ssh/sshd_config

- Port 22 以外ならクライアント側でポート指定

**⑤ firewalld/iptablesの確認**

firewall-cmd \--list-all

\# または

iptables -L -n

- 22/tcp 開いてなければ許可：

firewall-cmd \--permanent \--add-port=22/tcp

firewall-cmd \--reload

**⑥ SELinuxの制約確認**

getenforce

- Enforcing かつ /var/log/audit/audit.log に拒否ログがある場合：

semanage port -l \| grep ssh

- 必要ならポートを登録：

semanage port -a -t ssh_port_t -p tcp 2222

**ケース②：Webサーバーが応答しない（Apache/Nginx）**

**想定原因：**

- サービスダウン

- ファイアウォール遮断

- ポート競合

- 設定ファイルエラー

**トラブルシュート手順：**

**① サービスステータス確認**

systemctl status httpd \# Apache

systemctl status nginx \# Nginx

- ダウンしていれば起動

systemctl start httpd

**② エラーログの確認**

less /var/log/httpd/error_log

\# or

less /var/log/nginx/error.log

- \"Address already in use\" → ポート競合の可能性あり

**③ ポート開放確認（80, 443）**

ss -tunlp \| grep \':80\'

- 未リッスン → 設定ミス or 起動失敗

**④ 設定ファイルの文法チェック**

apachectl configtest

\# または

nginx -t

- Syntax OK でない場合 → 設定修正

**⑤ ファイアウォール・SELinux確認（80, 443開放）**

firewall-cmd \--list-ports

semanage port -l \| grep http_port_t

**ケース③：ディスク容量不足**

**想定原因：**

- /var や /tmp の肥大化（ログ、キャッシュ、コアダンプ）

**トラブルシュート手順：**

**① 容量確認**

df -h

- /, /var, /tmp を重点的にチェック

**② 大きなファイル検索**

du -ah /var \| sort -rh \| head -20

- /var/log/, /var/cache/, /tmp/ などを重点確認

**③ ログローテート設定確認（肥大化の元）**

less /etc/logrotate.conf

less /etc/logrotate.d/\*

**④ 不要なログの削除（慎重に）**

bash

コピーする編集する

rm -f /var/log/oldlogfile.log

**⑤ ジャーナルログの制限（systemd環境）**

bash

コピーする編集する

journalctl \--disk-usage

journalctl \--vacuum-size=200M

**ケース④：プロセス暴走による高負荷**

**想定原因：**

- 無限ループ処理・不具合スクリプト・cronの暴走など

**🔍 トラブルシュート手順：**

**① top/htop で高負荷プロセス確認**

top -o %CPU

**② 該当PIDを確認し詳細を取得**

ps -fp \<PID\>

**③ 該当ユーザー、コマンド、cronなど確認**

**④ 一時停止またはkill**

kill -STOP \<PID\> \# 一時停止

kill -9 \<PID\> \# 強制終了

**ケース⑤：名前解決できない**

**想定原因：**

- /etc/resolv.confの設定ミス

- DNSサーバ不応答

**トラブルシュート手順：**

**① DNSの設定確認**

cat /etc/resolv.conf

- nameserverが正しいか（8.8.8.8 などでテスト）

**② nslookup / dig で確認**

dig google.com

- 応答がなければDNS障害 or 通信経路に問題

**③ systemd-resolvedを使用している場合**

systemctl status systemd-resolved

**④ DNSキャッシュのフラッシュ**

systemd-resolve \--flush-caches

**補足：汎用的なトラブルシュート方針5ステップ**

  ---------------------------------------------------------------------------
  **ステップ**   **内容**
  -------------- ------------------------------------------------------------
  ①              「再現性があるか？」：1回限り or 継続？

  ②              「対象の層はどこか？」：ネットワーク？OS？アプリ？

  ③              「ログにヒントは？」：/var/log/ を見る

  ④              「分割して考える」：ミドルウェア⇔OS、アプリ⇔DB

  ⑤              「一つずつ潰す」：設定、FW、ポート、プロセスなど
  ---------------------------------------------------------------------------
