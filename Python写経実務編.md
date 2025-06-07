# 7日分のPython写経コード（写経用）

## 月曜：ファイル操作の基礎（file_io.py）

```
# file_io.py
filename = "sample.txt"

# 書き込み
with open(filename, "w") as f:
    f.write("これはテスト用のファイルです。\n")
    f.write("2行目の内容も書きます。\n")

# 読み込み
with open(filename, "r") as f:
    for line in f:
        print(line.strip())
```

## 火曜：Webスクレイピング入門（scrape_news.py）

```
# scrape_news.py
import requests
from bs4 import BeautifulSoup

url = "https://news.yahoo.co.jp/"
res = requests.get(url)
soup = BeautifulSoup(res.text, "html.parser")

print("★ ヘッドライン一覧：")
for a in soup.select("a[href]"):
    text = a.get_text(strip=True)
    if text:
        print("-", text)
```

## 水曜：CSV処理と集計（csv_stats.py）

```
# csv_stats.py
import csv

with open("data.csv", newline='') as f:
    reader = csv.DictReader(f)
    total = 0
    count = 0
    for row in reader:
        total += int(row["score"])
        count += 1

print(f"合計: {total}")
print(f"平均: {total / count:.2f}")
```

## 木曜：JSONと辞書処理（json_read_write.py）

# json_read_write.py
import json

data = {
    "users": [
        {"name": "Alice", "score": 85},
        {"name": "Bob", "score": 92}
    ]
}

with open("data.json", "w") as f:
    json.dump(data, f, indent=2)

with open("data.json") as f:
    loaded = json.load(f)
    for user in loaded["users"]:
        print(f"{user['name']}さんの点数: {user['score']}")
```

## 金曜：環境変数とOS操作（env_and_os.py）

```
# env_and_os.py
import os

# 環境変数の取得（例：MYNAMEを使う）
myname = os.getenv("MYNAME", "デフォルト名")
print(f"こんにちは、{myname} さん！")

# ディレクトリ一覧の表示
cwd = os.getcwd()
print(f"カレントディレクトリ: {cwd}")
print("この中のファイル一覧：")
for file in os.listdir(cwd):
    print("-", file)
```

## 土曜：ログ監視スクリプト（log_watch.py）

```
# log_watch.py
import time

logfile = "/var/log/syslog"  # Linuxの例。macOSなら /var/log/system.log
keyword = "error"

with open(logfile, "r") as f:
    f.seek(0, 2)  # 最後まで読み飛ばす
    while True:
        line = f.readline()
        if not line:
            time.sleep(0.5)
            continue
        if keyword in line.lower():
            print("検出:", line.strip())
```

## 日曜：簡易HTTPサーバ（http_server.py）

```
# http_server.py
from http.server import SimpleHTTPRequestHandler, HTTPServer

host = "127.0.0.1"
port = 8000

server = HTTPServer((host, port), SimpleHTTPRequestHandler)
print(f"サーバを起動します: http://{host}:{port}")
server.serve_forever()
```




