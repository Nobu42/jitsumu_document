# 📄 Word形式（.docx）のサーバ設計書から目次候補を抽出するスクリプト集

---

## ✅ Excel VBA版（見出し抽出・列に一覧化）

```vba
Sub ExtractHeadingsFromWordDoc()
    Dim wdApp As Object
    Dim wdDoc As Object
    Dim para As Object
    Dim row As Long
    Dim filePath As String

    ' Wordファイルのフルパスを指定（例：ファイル選択ダイアログ使用）
    With Application.FileDialog(msoFileDialogFilePicker)
        .Title = "設計書（Word）を選択してください"
        .Filters.Clear
        .Filters.Add "Word文書", "*.docx"
        If .Show <> -1 Then Exit Sub
        filePath = .SelectedItems(1)
    End With

    Set wdApp = CreateObject("Word.Application")
    Set wdDoc = wdApp.Documents.Open(filePath, ReadOnly:=True)

    row = 1
    With ActiveSheet
        .Cells(1, 1).Value = "見出しレベル"
        .Cells(1, 2).Value = "見出し内容"

        For Each para In wdDoc.Paragraphs
            With para.Range
                If .Style Like "見出し*" Or .Style Like "Heading*" Then
                    .Cells(row + 1, 1).Value = .Style
                    .Cells(row + 1, 2).Value = .Text
                    row = row + 1
                End If
            End With
        Next para
    End With

    wdDoc.Close False
    wdApp.Quit
    Set wdDoc = Nothing
    Set wdApp = Nothing
End Sub
```

📌 **使い方**：

1. Excelを開き、Alt + F11 でVBAエディタへ  
2. 標準モジュールを追加 → 上記コードを貼り付け  
3. 実行（Alt + F8）すると Wordファイルを選択し、**見出しだけを列挙**します  
4. 結果は **Excelのシートに「見出しレベル」「見出し内容」**で一覧化されます

---

## 🟦 PowerShell版（管理者権限なし・Wordから見出しだけ抽出）

```powershell
# Wordファイル内の「見出しスタイル」だけを抽出して表示する
# PowerShell 5.1 + Microsoft WordがインストールされていればOK

$word = New-Object -ComObject Word.Application
$word.Visible = $false

# Wordファイルパスを手動で指定
$docPath = "$env:USERPROFILE\Documents\サーバ基本設計書.docx"

$doc = $word.Documents.Open($docPath, $false)

foreach ($para in $doc.Paragraphs) {
    $style = $para.Range.Style
    $text = $para.Range.Text.Trim()

    # 見出しスタイルのみ出力（Heading 1〜9）
    if ($style -like "Heading*") {
        Write-Output "$style`t$text"
    }
}

$doc.Close()
$word.Quit()
[System.Runtime.Interopservices.Marshal]::ReleaseComObject($word) | Out-Null
```

📌 **結果例**：

```
Heading 1    1. 概要
Heading 2    1.1 背景と目的
Heading 2    1.2 対象範囲
Heading 1    2. ネットワーク構成
...
```

---

## 💡 応用例・カスタム案

- Excel VBAで「目次.mdファイル」を自動生成
- PowerShellで「目次とページ番号」を含めてHTML形式で出力
- 見出しに対応する「内容の先頭行」まで抜き出す

---

## 🧠 注意点

- Wordで見出しが「標準スタイル」で書かれている場合（例：太字+12ptなど）、**抽出できません**  
  → 見出しには「見出し1～3」などのスタイルをちゃんと適用しておく必要があります

- 日本語Wordでも `"見出し1"` などは `"Heading 1"` に内部変換されている場合が多いです（両方対応済）

---

## ✍️ 最後に

> **「全部読む」のではなく、「目次を読み取る」ことから始めるのがプロの読み方。**

このスクリプトで**全体像の把握とレビュー準備**が格段に楽になるはずです。  
次はこの結果をもとに、「自分用サマリ」や「レビュー質問リスト」を作っていきましょう！

---

*作成日：2025-06-18*  
*作成者：Horiuchi*
