---
title: "windowsでMacの「cmd+矢印キー」の動きをできるようにする"
date: 2022-02-28T08:35:36+09:00
with_date: true
tags: ["Windows", "AutoHotKey"]
draft: false
---


## はじめに

Windowsを使用していてMacと同じように「cmd+→(←)キー」で行頭や行末に移動したいと思いました。
デフォルトだとHomeキーやEndキーで同様のことができるのですが指が遠く使いづらいと感じていました。

そこで、AutoHotKeyを使って同じ動きをできるようにしたのでそれについてまとめました。
[AutoHotKey](http://ahkwiki.net/Top)の説明に関しては割愛します。

結論とハマったポイントを書いていますが、スクリプトの内容だけ知りたい方は結論だけ確認ください。


## 結論

すでにスクリプトファイルを作成している方であれば、以下の内容をスクリプトファイルの中に記述することで、Macと同じように「cmd+→(←)キー」で行頭や行末に移動できるようになります。

```bash
Ctrl & Left::
  if GetKeyState("Shift") {
    Send +{Home}
    return
  }
  Send, {Home}
return

Ctrl & Right::
  if GetKeyState("Shift") {
    Send +{End}
    return
  }
  Send, {End}
return
```


## 余談 Shiftキーが効かない

最初に試した方法だと行頭、行末にカーソルが移動はしてくれるがShftキーを押して選択しようと思ったらできませんでした。
しばらくそのまま使っていたのですが、不便だったので解決方法を探しました。

設定ファイルをちゃんと理解しておらず、自分の中で「cmd+右矢印キー」と「Shiftキー」を押したときには「Homeキー」と「Shiftキー」が押されたようになると思っていたのが原因でした。
実際には「cmd+右矢印キー+Shiftキー」で入力された場合に対応する動きを指定する必要があり、意図した動きになっていないようでした。

以下のように変更することで意図した動きをするようになりました。

```bash
;; 修正前
Ctrl & Left::
  Send, {Home}
return

;; 修正後
Ctrl & Left::
  if GetKeyState("Shift") {
    Send +{Home}
    return
  }
  Send, {Home}
return
```


## おわりに

Homeキー、Endキーを置き換えるだけでなく自分と同じく範囲選択もできるようにしたい。という方の参考になれば幸いです。
最後まで読んでいただきありがとうございました！


## 参考にしたサイト

- [【AutoHotkey】Windowsのカーソル移動をMacっぽく割り当てる！Alt+矢印でHomeやEndを実行！](https://clrmemory.com/pc-mobile/windows/autohotkey-alt-arrow-home-end/)
- [AutoHotkeyで3キー同時押し](https://tex2e.github.io/blog/keyboard/ahk-triple-key)
