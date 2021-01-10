@def date = Date(2021, 1, 10)
@def title = "bug.n メモ"



\toc

## 概要

- [AutoHotKey](https://www.autohotkey.com)で書かれている[タイル型ウィンドウマネージャ](https://ja.wikipedia.org/wiki/%E3%82%BF%E3%82%A4%E3%83%AB%E5%9E%8B%E3%82%A6%E3%82%A3%E3%83%B3%E3%83%89%E3%82%A6%E3%83%9E%E3%83%8D%E3%83%BC%E3%82%B8%E3%83%A3)
- オープンソース＆GPL3.0ライセンス
- [scoop](https://scoop.sh)経由でインストールできないが、[リリース](https://github.com/fuhsjr00/bug.n/releases/latest)にはバイナリが含まれていてポータブルアプリとして動作する
- バイナリを使わずahkスクリプトとして動作させることもできる（設定を弄りまわすならこっち）
- \key{Win}キーを含むWindows標準のショートカットを上書きする



画像検索などをしてみてもあまり柔軟なレイアウトは作成できないようだが、数週間使ってみてWindows標準の仮想デスクトップを置き換えるだけでも有用だと思った。



## 既定のキーバインド

[Default_hotkeys.md](https://github.com/fuhsjr00/bug.n/blob/master/doc/Default_hotkeys.md) より

「ビュー→仮想デスク」、「マスターエリア→メインエリア」など一部を言い換えた。また、挙動がよくわからないものなどは空欄のまま（分かり次第追記）。

### ウィンドウ

| キー                                  | 操作                                       |
| ------------------------------------- | ------------------------------------------ |
| \key{Win} + \key{↓}                   | 次のウィンドウをアクティブにする           |
| \key{Win} + \key{↑}                   | 前〃                                       |
| \key{Win} + \key{Shift} + \key{↓}     | アクティブウィンドウを次の位置に移動       |
| \key{Win} + \key{Shift} + \key{↑}     | 〃前の位置に移動                           |
| \key{Win} + \key{Shift} + \key{Enter} | アクティブウィンドウをメインの位置に動かす |
| \key{Win} + \key{c}                   | アクティブウィンドウを閉じる               |
| \key{Win} + \key{Shift} + \key{d}     | タイトルバーの表示をトグル                 |
| \key{Win} + \key{Shift} + \key{f}     | アクティブウィンドウをフロートさせる       |
| \key{Win} + \key{Shift} + \key{m}     | 〃フロートさせたあと移動                   |
| \key{Win} + \key{Shift} + \key{s}     | 〃フロートさせたあとサイズ変更             |
| \key{Win} + \key{Ctrl} + \key{m}      | 〃フロートさせて最小化                     |
| \key{Win} + \key{Shift} + \key{x}     | 〃フロートさせたあと最大化                 |
| \key{Win} + \key{i}                   | アクティブウィンドウの情報を表示           |
| \key{Win} + \key{Shift} + \key{i}     | ウィンドウリストを表示                     |

### レイアウト

| キー                                             | 操作                                             |
| ------------------------------------------------ | ------------------------------------------------ |
| \key{Win} + \key{Tab}                            | レイアウトをひとつ前に状態に戻す                 |
| \key{Win} + \key{t}                              | タイルモード                                     |
| \key{Win} + \key{f}                              | フローティングモード（一般的なウィンドウ操作）   |
| \key{Win} + \key{m}                              | 片眼鏡(monocle)モード ＝ 全ウィンドウ最大化      |
| \key{Win} + \key{←}                              | メインウィンドウの横幅を減らす                   |
| \key{Win} + \key{→}                              | 〃増やす                                         |
| \key{Win} + \key{Ctrl} + \key{t}                 | レイアウトの分割軸変更（縦or横）                 |
| \key{Win} + \key{Ctrl} + \key{Enter}             | メインとサブの位置を反転                         |
| \key{Win} + \key{Ctrl} + \key{Tab}               | メインエリア分割軸を変更（→水平→垂直→片眼鏡→）   |
| \key{Win} + \key{Ctrl} + \key{Shift} + \key{Tab} | スタックエリア分割軸を変更（→水平→垂直→片眼鏡→） |
| \key{Win} + \key{Ctrl} + \key{↓}                 | メインエリアの垂直方向のウィンドウ数を減らす     |
| \key{Win} + \key{Ctrl} + \key{↑}                 | 〃増やす                                         |
| \key{Win} + \key{Ctrl} + \key{←}                 | メインエリアの水平方向のウィンドウ数を減らす     |
| \key{Win} + \key{Ctrl} + \key{→}                 | 〃増やす                                         |
| \key{Win} + \key{Shift} + \key{←}                | タイル・片眼鏡モードでウィンドウ間の余白を減らす |
| \key{Win} + \key{Shift} + \key{→}                | 〃増やす                                         |
| \key{Win} + \key{Ctrl} + \key{BackSpace}         | タイルレイアウトを既定の状態にリセット           |

### 非動的タイリングモード

| キー                                  | 操作                                                 |
| ------------------------------------- | ---------------------------------------------------- |
| \key{Alt} + \key{↓}                   | 手動でアクティブウィンドウを次のエリアに移動         |
| \key{Alt} + \key{↑}                   | 〃前〃                                               |
| \key{Alt} + \key{Shift} + \key{Enter} |                                                      |
| \key{Alt} + <num>                     | アクティブウィンドウを<num>番目のエリアに移動（1~9） |
| \key{Alt} + \key{BackSpace}           |                                                      |

### タグ・仮想デスク

| キー                              | 操作                                                        |
| --------------------------------- | ----------------------------------------------------------- |
| \key{Win} + \key{Shift} + \key{n} |                                                             |
| \key{Win} + <num>                 | <num>番目の仮想デスクをアクティブにする（表示）             |
| \key{Win} + \key{BackSpace}       | 直前のアクティブ仮想デスクをアクティブに                    |
| \key{Win} + \key{Shift} + \key{0} | アクティブウィンドウを全仮想デスクに表示\br ＝「全て」のタグをつける |
| \key{Win} + \key{Shift} + <num>   | アクティブウィンドウを<num>の仮想デスクに移動               |
| \key{Win} + \key{Ctrl} + <num>    | アクティブウィンドウを<num>番目の仮想デスクにも表示。\br ＝<num>のタグを追加 |

### マルチモニター

モニターが一枚なら無縁

| キー                                           | 操作                           |
| ---------------------------------------------- | ------------------------------ |
| \key{Win} + \key{.}                            | 次のモニターをアクティブにする |
| \key{Win} + \key{,}                            | 前の〃                         |
| \key{Win} + \key{Shift} + \key{.}              |                                |
| \key{Win} + \key{Shift} + \key{,}              |                                |
| \key{Win} + \key{Ctrl} + \key{Shift} + \key{.} |                                |
| \key{Win} + \key{Ctrl} + \key{Shift} + \key{,} |                                |

### GUI

| キー                                  | 操作                         |
| ------------------------------------- | ---------------------------- |
| \key{Win} + \key{Shift} + \key{Space} | ステータスバーの表示をトグル |
| \key{Win} + \key{Space}               | タスクバーの表示をトグル     |
| \key{Win} + \key{y}                   | コマンドランナーを開く\br 独自のbug.nが使えるかもしれない（要調査） |
| \key{Win} + \key{Shift} + \key{y}     |                              |
| \key{Alt} + \key{Shift} + \key{y}     |                              |

### bug.n管理

| キー                             | 操作                                                   |
| -------------------------------- | ------------------------------------------------------ |
| \key{Win} + \key{Ctrl} + \key{e} | 設定ファイルを標準テキストエディタで開く               |
| \key{Win} + \key{Ctrl} + \key{s} | 現在のmonitor, view, layout の状態を設定ファイルに保存 |
| \key{Win} + \key{Ctrl} + \key{r} | bug.nを再起動する                                      |
| \key{Win} + \key{Ctrl} + \key{q} | bug.n起動以前の状態を復元して終了                      |

