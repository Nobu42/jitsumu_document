# 📘 サーバー設計書向け PlantUML 図テンプレート集

## 📡 ネットワーク構成図（基本構成）
```
@startuml
!includeurl https://raw.githubusercontent.com/plantuml-stdlib/Cicon-PlantUML/master/Cicon-Common.puml

title ネットワーク構成図（基本構成）

actor User
node "Firewall" as FW
cloud "Internet" as NET
node "Web Server" as Web
node "App Server" as App
database "DB Server" as DB

User --> NET
NET --> FW
FW --> Web
Web --> App
App --> DB

@enduml
```
## 🏢 DMZを含むネットワーク構成図
```
@startuml
title DMZ構成図

rectangle "外部ネットワーク" {
  actor User
  cloud "Internet" as NET
}

rectangle "DMZ" {
  node "Firewall (外部)" as FW1
  node "Web Server" as Web
}

rectangle "内部ネットワーク" {
  node "Firewall (内部)" as FW2
  node "App Server" as App
  database "DB Server" as DB
}

User --> NET
NET --> FW1
FW1 --> Web
Web --> FW2
FW2 --> App
App --> DB

@enduml
```
## 🔀 ロードバランサ構成図
```
@startuml
title ロードバランサ構成図

actor User
cloud "Internet"
node "Load Balancer (HAProxy)" as LB
node "Web Server 1" as Web1
node "Web Server 2" as Web2
database "DB Server" as DB

User --> Internet
Internet --> LB
LB --> Web1
LB --> Web2
Web1 --> DB
Web2 --> DB

@enduml
```

## 🔐 セキュリティゾーン構成図
```
@startuml
title セキュリティゾーン構成

rectangle "ゾーン: 外部" {
  actor "外部ユーザー" as ExtUser
}

rectangle "ゾーン: DMZ" {
  node "Web Proxy"
  node "メールGW"
}

rectangle "ゾーン: 内部" {
  node "業務サーバ"
  database "社内DB"
}

ExtUser --> "Web Proxy"
ExtUser --> "メールGW"
"Web Proxy" --> "業務サーバ"
"業務サーバ" --> "社内DB"

@enduml
```
## 🧩 サービス間構成図（マイクロサービス的）
```
@startuml
title サービス連携構成図

rectangle "フロントエンド" {
  node "SPA (Vue/React)"
}

rectangle "バックエンド" {
  node "API Gateway"
  node "Auth Service"
  node "User Service"
  node "Order Service"
}

rectangle "データベース" {
  database "User DB"
  database "Order DB"
}

"SPA (Vue/React)" --> "API Gateway"
"API Gateway" --> "Auth Service"
"API Gateway" --> "User Service"
"API Gateway" --> "Order Service"

"User Service" --> "User DB"
"Order Service" --> "Order DB"

@enduml
```

## 🖥️ サーバスペック一覧（コンポーネント図的）
```
@startuml
title サーバー構成スペック一覧

package "Webサーバ" {
  [OS: AlmaLinux 9.4]
  [CPU: 4core]
  [Memory: 8GB]
  [App: Apache, PHP]
}

package "DBサーバ" {
  [OS: Rocky Linux 9.4]
  [CPU: 8core]
  [Memory: 16GB]
  [DB: PostgreSQL 15]
}

@enduml
```
## 📶 VLAN構成図
```
@startuml
title VLAN構成図

rectangle "L2スイッチ" {
  [ポート1: VLAN 10] as P1
  [ポート2: VLAN 10] as P2
  [ポート3: VLAN 20] as P3
  [ポート4: VLAN 20] as P4
}

P1 --> "業務PC A"
P2 --> "業務PC B"
P3 --> "管理者PC"
P4 --> "プリンタ"

@enduml
```

## 🧯 冗長構成図（クラスタリング）
```
@startuml
title 冗長構成図（クラスタリング）

actor User
cloud "Internet"
node "LVS (VIP)" as LVS
node "App Server 1" as App1
node "App Server 2" as App2
database "共有DB (クラスタ構成)" as ClusterDB

User --> Internet
Internet --> LVS
LVS --> App1
LVS --> App2
App1 --> ClusterDB
App2 --> ClusterDB

@enduml
```

## 💾 バックアップ構成図
```
@startuml
title バックアップ構成図

node "業務サーバ" as Work
node "バックアップサーバ" as Backup
database "NAS" as NAS

Work --> Backup : rsync/cron
Backup --> NAS : 定期同期（夜間）

@enduml

```
## 🔄 CI/CD構成図（Ansible + Git）
```
@startuml
title CI/CD構成図（Ansible + Git）

actor Dev as "開発者"
node "Git リポジトリ" as Git
node "CIサーバ (Jenkins)" as CI
node "ターゲットサーバ" as Target
node "Ansible" as Ansible

Dev --> Git : Push
Git --> CI : Webhook
CI --> Ansible : playbook実行
Ansible --> Target : 構成適用

@enduml
```
## 🌐 DNS/DHCP構成図
```
@startuml
title DNS/DHCP構成図

actor PC
node "DHCPサーバ" as DHCP
node "DNSサーバ" as DNS
node "Webサーバ" as Web

PC --> DHCP : IP要求
DHCP --> PC : IPアドレス付与
PC --> DNS : 名前解決
DNS --> PC : IP返答
PC --> Web : アクセス

@enduml
```
