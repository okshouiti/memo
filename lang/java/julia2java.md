@def date = Date(2021, 02, 02)
@def title = "Julia から Java へ入門"

Julia をベースにして Java に入門する。Julia と異なる場面のみ記載。（[Julia から JavaScript へ入門](/lang/javascript/julia2js/)の知識も前提にしてしまっているが・・・）

\toc



## 概要
Java プログラムは Java 仮想マシン上で動くため、OS の違いを Java が吸収しあらゆる環境で動くプログラムを作成できる。
- エディション
  1. Java SE (Standaard Edition)
     - VM や標準 API などを含む標準エディション
  2. Java EE (Enterprise Edition)
     - サーバアプリ開発用の機能が多く含まれる
  3. Java ME (Micro Edition)
     - 組み込みデバイスなどリソースが限定される環境向け
- ２つの環境
  1. JRE (Java Runtime Environment)
     - ランタイム。コンパイル済みの JAR ファイルやクラスファイルを実行する環境。
  2. JDK (Java Development Kit)
     - 開発環境。実行環境+コンパイラ+デバッガ+etc...。
- 様々な VM
  - Oracle 製で最大シェアの HotSpot やオープンソースの OpenJDK など数多ある
- 実行フロー
  1. `.java`ファイル（ソースコード）を作成
  2. ソースコードを`.class`中間コード（VM 上で実行可能な Java 実行ファイル）にコンパイル
  3. 中間コードを各種 OS 上の VM で実行





## インストール
```powershell
scoop bucket add java
scoop install oraclejdk  # 2020年2月現在はversion15が入る
```





## ファイル作成、コンパイル、実行
```java
public class Hello{
    public static void main(String... args){
        System.out.println("Hello, world!");
    }
}
```
という`hello world`プログラムがあったとして、これを`Hello.java`というファイル名で保存（ファイル名＝クラス名、UTF-8 BOM 無し）。そして、これをコンパイルするには
```powershell
javac -encoding UTF-8 Hello.java
```
とすればよい。これを実行するには拡張子をつけずに
```powershell
java Hello
```
でＯＫ。





## 全般

### コメント
```java
// 単行コメント

/*
複数行コメント
*/
```

### Javadoc とアノテーション
Julia の docstring 同様、Java にもソース中に埋め込んだ文字列からドキュメントを生成する機能がある。また`@override`でオーバーライドを、`@Depricated`で非推奨を、`@SuppressWarnings`で警告を出さないことを注釈できる。
```java
/**
 * 点数を扱うクラス
*/
class Score {
    /** 生徒名 */
    String name;

    /** 英語の点数 */
    int eng;

    /**
     * コンストラクタ
     * @param name 生徒名
     * @param eng 英語の点数
    */
    Score(String name, int eng) {
        this.name = name;
        this.eng = eng;
    }
    @Override
    public String toString() {
        return name;
    }
}
```





## クラス

### クラスの概要
- クラス名はアッパーキャメルケース
- メンバ - 「クラスに属するもの = フィールド&メソッド」
  - クラスメンバ　「インスタンス化しなくても参照できる、`static`修飾子をつける」
```java
修飾子 class クラス名{
    フィールド...;   // クラス内のすべてのメソッドからアクセス可能
    クラス名(引数...) {
        // クラス名メソッド=コンストラクタ
    }
    メソッド...;
}


class Score {
    String name;
    int eng, math;
    private int jap = 80;   // 外から見えない
    static int lastYearAverage = 76;   // クラスメンバ
    // 初期化ブロック、フィールドの規定値を設定。staticでクラスメンバにすることもできる
    {
        name = "taro";
        eng = 80;
        math = 77;
    }
    Score(String name, int eng) {
        this.name = name;
        this.eng = eng;
    }
    // アクセッサメソッド
    int getJapaneseScore() {
        return jap;
    }
    void setJapaneseScore(int score) {
        if ((0 < score) && (score < 100)) {
            this.jap = score;
        } else {
            System.out.println("Score must be [0 < score < 100]");
        }
    }
    void printEnglishScore(int eng) {
        System.out.println(eng);   // フィールドと同名の引数が優先される
        System.out.println(this.eng);
    }
}
```

### メンバのアクセス範囲

| 修飾子      | どこからアクセスできるか                     |
| ----------- | -------------------------------------------- |
| なし        | 同クラス内、同パッケージ内                   |
| `public`    | アクセス範囲無制限                           |
| `private`   | 同クラス内メンバのみ                         |
| `protected` | 同クラス内およびサブクラス内、同パッケージ内 |

### メンバの修飾子

| aaa        | nnn                                  |
| ---------- | ------------------------------------ |
| `static`   | インスタンス化なしでアクセス可能に   |
| `final`    | メンバを定数のように上書き不可に     |
| `abstract` | インスタンス化不可の抽象クラスを定義 |
| etc...     |                                      |

### インスタンス
```java
// インスタンス化
クラス名 変数 = new クラス名(引数...);

Score score = new Score("kaori", 90);
score.eng;   // -> 90
score.getJapaneseScore();   // -> 80
score.setJapaneseScore(110);  // -> Score must be [0 < score < 100]
```

### 継承
```java
修飾子 class クラス名 extends 親クラス名 {
    // 子クラスのメンバを定義
}

public class BunkeiScore extends Score {
    int poliEcon = 77;
}
```

### ポリモーフィズムなど
- オーバーライド
  - 上位クラス内で定義されているメソッドをサブクラス内で再定義すること
  - `@Override`でオーバーライドを明示する
- オーバーロード
  - 引数の型や数が異なる複数のメソッドを定義すること
  - コンストラクタでも同様
- メソッドの動的束縛
  - 呼び出されるメソッドはインスタンス変数の型によらずインスタンスの種類によって決定される
```java
class A {
    void greet() {
        System.out.println("Hi! I'm A!");
    }
}
class B extends A {
    void greet() {
        System.out.println("Hi! I'm B!");
    }
}
A aaa = new A();
A bbb = new B();   // A型でBのインスタンスを作成
aaa.greet();   // -> Hi! I'm A!
bbb.greet();   // -> Hi! I'm B!
```

### main メソッド
```java
public static void main(String... args) {
    // プログラム開始時に実行される処理
}
```





## パッケージ
Julia におけるモジュールに該当。
- 各ファイルの先頭に記述
- パッケージに所属させることでクラス名の衝突を回避できる
- 外からは`PkgName.ClassName`でアクセスできる
- サブパッケージのように階層構造を形成できる
- （？おそらく命名規則は lowercase で区切り文字は`.`）
```java
package packagename;

// 別パッケージのクラスをパッケージ名を省略して呼べるようにする
import otherpackage.OtherClass;
import otherpackage.*;   // ワイルドカードで全部読み込む
import static otherpackage.Otherclass.*;   // staticインポートでクラスメンバをクラス名なしで参照できるように

public class MyClass {
    // メンバなど
}
```





## 変数、型、リテラル
暗黙的に型変換されるが、意図的な型変換（キャスト）もできる。変数名は case-sensitive、命名は lower キャメルケース。
```java
// 型推論を効かせる
var 変数名 = 値;

// 定数
final 型 変数名 = 値;
final int MY_AGE = 24;

// 型変換
int m = 32768;   // shortの表現限界2^15-1より１大きい
short n = (short)m;   // キャスト（ナローイング）

// 文字
char c1; // 宣言
char c2 = 'a'; // 初期化
char c3, c4; // まとめて宣言
c3 = c4 = '\u0061';

// 文字列
// """みたいな自動エスケープリテラルはない？
String str = "my string";

// 整数
int num1 = 255;
System.out.println(1000000000L); // long型を断定、juliaでの`::Int64`か？
3_000_000; // 桁区切り

// 浮動小数点数
double num2 = 3.14;
2.0e3; // 2.0x10^3 = 2000.0
2.0e-3; // 2.0x10^-3 = 0.002

// ブール型
boolean bool = true;
```

### プリミティブ型

| 型        | リテラル式例        | ラッパークラス        | 対応する Julia の型 | 説明               |
| --------- | ------------------- | --------------------- | ------------------- | ------------------ |
| `byte`    | `0x2c`              | `java.lang.Byte`      | `Int8`              | 8bit 整数          |
| `short`   | `12345`             | `java.lang.Short`     | `Int16`             | 16bit 整数         |
| `int`     | `12345`             | `java.lang.Integer`   | `Int32`             | 32bit 整数         |
|           | `0101011`           |                       |                     | 8 進数、先頭に 0   |
|           | `0x1ac`             |                       |                     | 16 進数            |
|           | `0b10`              |                       |                     | 2 進数             |
| `long`    | `123_456_789L`      | `java.lang.Long`      | `Int64`             | 64bit 整数         |
| `float`   | `1.732f`、`2F`      | `java.lang.Float`     | `Float32`           | 単精度浮動小数点数 |
| `double`  | `1.414`、`3d`、`3D` | `java.lang.Double`    | `Float64`           | 倍精度浮動小数点数 |
| `char`    | `'c'`               | `java.lang.Character` | `Char`              | 文字               |
| `boolean` | `true`              | `java.lang.Boolean`   | `Bool`              | 真偽値             |

### ラッパークラス
プリミティブ型はそれぞれラッパークラスと呼ばれるクラスを持ち、これらが定数やメソッドを提供する。プリミティブ型からラッパーオブジェクトへの変換は必要時に自動で行われる。

#### メンバ例
ラッパークラスが提供する定数やメソッドの一例

| ラッパークラスのメンバ         | 例                            | 説明                                                    |
| ------------------------------ | ----------------------------- | ------------------------------------------------------- |
| `SIZE`                         | `Byte.SIZE` (= 8)             | ビット数                                                |
| `BYTES`                        | `Byte.BYTES` (= 1)            | バイト数                                                |
| `MAX_VALUE`                    | `Byte.MAX_VALUE` (= 127)      | 最大値                                                  |
| `MIN_VALUE`                    | `Byte.MIN_VALUE` (= -128)     | 最小値                                                  |
| `valueOf(v)`                   | `String.valueOf(1245)`        | プリミティブ値`v`からラッパークラスオブジェクトをつくる |
| `valueOf(str)`                 | `Integer.valueOf("412")`      | 文字列から〃                                            |
| `valueOf(str, base)`           | `Integer.valueOf("412", 2)`   | 文字列から`base`進数で〃                                |
| `parse<PrimiClass>(str)`       | `Integer.parseInt("332")`     | 文字列をプリミティブ値へパース                          |
| `parse<PrimiClass>(str, base)` | `Integer.parseInt("332", 16)` | 文字列を`base`進数で〃                                  |
| `toString(v)`                  | `Integer.toString(56)`        | プリミティブ値`v`から文字列へ                           |
| `toString(v, base)`            | `Integer.toString(56, 16)`    | `base`進数で〃                                          |

#### 初期値
フィールドを宣言する際ラッパー型とプリミティブ型では初期値が異なる。例えば、整数をプリミティブ型で宣言した場合は`0`が初期値だがラッパー型で宣言した場合は`null`となるため、値の不在を判定しなければならない場合などはラッパー型で宣言する。

### 参照型
working...

| 型       | bbb | ccc | ddd |
| -------- | --- | --- | --- |
| `String` |     |     |     |





## 演算子
```java
/*
更新・比較・論理演算子を含め、ほぼ同じ
三項演算子 a ? b : c　までも同じ
*/

// 文字列連結は*ではなく+
str1 + str2

// インクリメント、デクリメント
++x;   // インクリメント後に値を返す
y--;   // 値を返してデクリメント

// 排他的論理和
true ^ false;   // -> true
true ^ true;   // -> false
```





## ループ
```java
/*
初期化式 = 反復変数を初期化する式
操作式 = 各ループ処理ごとに反復変数を操作する式
*/

// 初期化式を省略してfor以前に初期化された変数を使って回すこともできる for ( ; 条件式; 操作式)
for (初期化式; 条件式; 操作式){
    コード...
}

// 反復変数は複数同時に使える
for (初期化式1, 初期化式2, ...; 条件式; 操作式1, 操作式2, ...){
    コード...
}

// for-each コレクションの要素を反復変数として使う for i ∈ collection
for (型 変数名:コレクション){
    コード...
}



/*
do-whileループ
先に条件式を評価するwhileと異なり、まず処理を実行してから条件式を評価してループ
*/

// 処理が一つの場合
do
  単文;
while (条件式);

do{
    コード...
}while (条件式);
```





## 条件評価

### if 文
```java
if (条件式){
    コード...
}else if (条件式){
    コード...
}else{
    コード...
}

// 処理がひとつ
if (条件式)
    単文;
else
    単文;
```

### switch 文
これは julia に無い。式を評価し（結果は整数型でなければならない）、同じ値が`case`で指定されているとその位置から処理を実行する。複数の`case`に同じ処理をさせることも可能（例：値 2,値 3）。同じ値がない場合に`default`が定義されているならそこへ移る。`if`文のように使いたい場合は`case`の処理の最後で`break`する。
```java
switch (式){
    case 値1:
        コード...;
    case 値2:
    case 値3:
        コード...;
    default:
        コード...;
}
```

### `break`、`continue`
```java
/*
ラベル付きbreak
ラベル付きでループを定義し、break対象を指定できる
*/
parent: for (int i = 1; i < 5; i++){
    for (int j = 1; j < 5; j++){
        break parent;
    }
    System.out.println(i)
}

// continueも同様にラベル付け可
```





## 様々なデータ

### 文字列
```java
// リテラルで生成
String str = "Hello, ";

// 文字の配列から作る
char chars[] = {'a', 'b', 'c'};
String str = new String(chars);

// 演算など
String str2 = "world";
str + str2;   // -> "Hello, world!"
str.equals(str2);   // -> false
str.charAt(1);   // -> "e"
str.length();   // -> 7
```

### 数値
```java
int a = 123
```

### 配列
```java
// 宣言と生成
型 変数名[];   // 方法１
型[] 変数名;   // 方法２
変数名 = new 型[要素数];   // 宣言された配列を生成
型 変数名[] = new 型[要素数];   // 宣言＆生成
型[] 変数名 = new 型[要素数];   // 〃
型 [] 変数名 = new 型[要素数];   // 〃
// 初期化
String strArray[] = {"string1", "string2"};
int [] arr = {1,2,3};

// 多次元配列
int multi1[][] = new int[2][];
multi1[0] = new int[2];
multi1[1] = new int[2];
// ↑ かあるいは int multi1[][] = new int[2][2];
multi1[0][0] = 5;
multi1[0][1] = 6;
multi1[1][0] = 7;
multi1[1][1] = 8;
for (int i=0; i<2; i++) {
    for (int j=0; j<2; j++) {
        System.out.println(multi1[i][j]);   // -> 5,6,7,8
    }
}
// 初期化
int numarr[][] = {
    {3, 6, 4},
    {5, 7, 3}
};


// 生成された配列に値を設定する
変数名[添字] = 値;
int arr[] = {1,2};
arr[2] = 3;   // 生成時の要素数を超えて代入できない（StaticArrays.jlみたいなものか）

// コピーを作らない
int a[] = {1, 2};
int b[] = a;
b[0] = 10;
System.out.println(a[0]);   // -> 10

//
arr.length;   // 要素数

```





## 雑多な Tips
- プライベートなフィールド名の後ろに`_`をつけない。高度な IDE が主流なイマドキ名前で判別する必要もない。
- 変数は名詞、メソッドは動詞となるよう心掛けて命名する。





## 出典
- [Java 本格入門 ~モダンスタイルによる基礎からオブジェクト指向・実用ライブラリまで](https://amzn.to/3r8JMpf)
