@def date = Date(2021, 01, 22)
@def title = "JuliaからJavaScriptへ入門"



JuliaをベースにしてJavaScriptに入門する。主にJuliaと異なる部分に言及する。

\toc



## 概要
- ECMAScriptという標準規格があり、ブラウザやNode.jsなどで独自に実装されている
- 安全でないレガシー機能を制限するstrict mode
  - ファイルか関数の先頭に`"use strict";`という文字列を置くと有効
- 実行コンテキスト
  - Script - 基本の実行コンテキスト、デフォルトで非strict mode
  - Module - モジュールとして実行する、独自の機能を持つ。デフォルトでstrict mode
- セミコロン`;`で文を区切る
- コメント
  - 単行 - `// コメント`
  - 複数行 - `/* コメント */`

以下、コードの検証は`Node.js v15.6.0`にて行う。





## 変数
- 変数名はcase-sensitiveで使えるのは
  - `a~Z`
  - `0~9`
  - `_`
  - `$`
  - 一応、一部の日本語（漢字含む）が使えないこともない

```javascript
// const (ES2015)
// 宣言のみは不可、要初期化
// プリミティブな値以外ではミュータブル
const 変数名1 = 値1,
      変数名2 = 値2;

// let (ES2015) varは基本的に使わない
let 変数名;       // 宣言
let 変数名 = 値;  // 初期化
変数名 = 値;      // 再代入

// var  事実上deprecated？
var 変数名;
var 変数名1 = 値1;
var 変数名1 = 値2;   // 再代入だけでなく再定義が可能、ほかにも巻き上げ問題がある
```





## 型

### プリミティブ型

| 型          | ラッパーオブジェクト | Juliaの何に相当？ | 説明                                 |
| ----------- | -------------------- | ----------------- | ------------------------------------ |
| `boolean`   | `Boolean`            | `Bool`            | 真偽値                               |
| `number`    | `Number`             | `Number`          | 数値、Juliaでは抽象型だが            |
| `bigint`    |                      | `BigInt`          | ES2020より                           |
| `string`    | `String`             | `String`          | 文字列                               |
| `undefined` |                      |                   | 未定義の値。返値のない式の結果など。 |
| `null`      |                      |                   |                                      |
| `Symbol`    | `Symbol`             |                   |                                      |


### ラッパーオブジェクト
プリミティブ型の値をラップしたオブジェクト。この仕組みにより、プリミティブ型の値に対してインスタンスメソッドを呼び出せる。プリミティブ型の値に対してメソッド呼び出しなどを行うと自動的にラッパーオブジェクトへの変換が行われる。
```javascript
const str = "string";
const wrapObj = new String("string");
typeof(str) === typeof(wrapObj);   // -> false

// プリミティブ値の取り出し
const wrap = new String("primitive");
typeof(wrap.valueOf());   // -> 'string'
```

### オブジェクト
オブジェクト、配列、関数、正規表現、Dateなど

### 明示的型変換
```javascript
// 文字列→数値
Number("123");   // -> 123
Number("not number");   // -> NaN (数値以外が含まれる)
Number.parseInt("32 years old", 10);   // -> 32 10進数としてパース
Number.parseFloat("19.5 points");   // -> 19.5
```





## 演算子
```javascript
// 比較
a == b;   // 暗黙的な型変換を伴う
a === b;   // 型変換なし、明示的な型変換が必要でないならこちらを使う

// 文字列連結
"str1" + "str2";

// べき乗 (ES2016)
2**3;   // -> 8

// インクリ・デクリメント
++変数;   // 前置、インクリメントしてから変数の値を返す
変数--;   // 後置、変数の値を返してからデクリメント

// 分割代入 (ES2015)
const arr = ["aaa", "bbb"];
const [str1, str2] = arr;   // juliaとは違い角括弧を使う

/*
Nullish coalescing演算子 (ES2020)
	Boolしか受け付けないjuliaの短絡評価と異なり、jsでは左辺の値を真偽値に型変換する。
	これは値が未定義な場合に規定値を指定するためによく利用されるが、
	0などの値が定義されていても未定義として規定値を返してしまう。
	一方この演算子は、左辺がnullまたはundefinedなら右辺を評価する。
*/
null ?? "the result is null";

// 複合式
式1, 式2, 式3;   // juliaでの(式;式)
```





## 関数
- `return;`で値を返さない。結果は`undefined`
- 関数呼出時に渡した値が定義されている引数の数より
  - 少ないと、それらは`undefined`として実行される
  - 多いと、余分な値は無視して実行される
- デフォルト値が定義できる（ES2015）

### 定義
```javascript
function 関数名(引数) {
    コード...;
    return 返値;
}


// 関数式による定義、無名関数オブジェクトを変数に代入
const 変数名 = function() {
    コード...;
    return 返値;
};
// 関数式で関数名を使うとその関数の内側からのみ関数名を使えるので再帰などで便利
const myRecursive = function callMyself(n) {
    if (n===0 || n<0) {
        return console.log("finished");
    }
    console.log(n);
    callMyself(n-1);
};
myRecursive(5);   // -> 5,4,3,2,1,finished
callMyself(5);   // -> not defined


/*
アロー関数（ES2015）
	常に無名関数
	thisが静的に決まる（後述）
	newが使えない。非コンストラクタ
	argumentsで引数添字アクセス不可
*/
const 変数名 = (引数) => {
    コード...;
    return 返値;
};
const 変数名 = () => {処理};   // 無引数
const 変数名 = x => {処理};   // 1引数
const 変数名 = (x, y) => {処理};   // 2引数
const 変数名 = x => x*x;   // 単式、その結果が返値
```

### 可変長引数関数
```javascript
// ...は前置
function func(x, ...args){
    console.log(x);
    console.log(args);
}

// Spread構文による引数展開時も前置
const myints = [1,2,3,4,5];
func(1, ...myints)

// arguments
// 関数の中でのみ使えるArray-likeオブジェクトで引数に添字アクセスできる
```

### 分割代入
```javascript
const person = {
    age: 24
};

function howOld({ age }){
    return age;
}

howOld(person);
```

### メソッド
```javascript
const オブジェクト名 = {
    メソッド名: function() {
        コード...;
    },
    メソッド名: () => {
        コード...;
    }
}

const オブジェクト名 = {};
オブジェクト名.メソッド名 = function() {
    コード...;
};

const オブジェクト名 = {
    メソッド名() {
        return "Hello, world!";
    }
};
```





## 条件分岐
条件式の結果は`boolean`へ暗黙のうちに型変換される。

### if文
```javascript
if (条件式) {
    コード...;
} else if (条件式) {
    コード...;
} else {
    コード...;
}
```

### switch文
式の結果と厳密に一致するラベル以降の`case`節を実行する。一致するラベルの`case`節のみ実行したい場合は節の最後で`break;`する
```javascript
switch (式) {
    case ラベル1:
        コード...;
    case ラベル2:
        コード...;
    default:
        // 一致するラベルがなければここが実行される
        コード...;
}
```





## ループ

### while文
```javascript
let 反復変数 = 1;

// 条件式を評価してから処理を実行
while (条件式) {
    コード...;
    反復変数を操作;
}

// 処理を実行してから条件式を評価
do {
    コード...;
    反復変数を操作;
} while (条件式);
```

### for文
イテラブルオブジェクトは配列や文字列など
```javascript
// 反復変数はconstで初期化
for (反復変数初期化式; 条件式; 反復変数操作) {
    コード...;
}

// われらがfor文 (ES2015)
for (反復変数 of イテラブルオブジェクト) {
    コード...;
}
```





## object
jsの基本オブジェクト、ミュータブル。`Symbol`以外オブジェクトはプロパティ名に設定する際に`toString`メソッドで文字列に暗黙的変換されるため要注意。
```javascript
// 空のobject
const 変数名 = {};

// キーとして識別できる名前ならシングル・ダブルクオートを省略可
const myName = "my name";
const yourName = "your name";
const obj = {
    key: "value",
    "key-2": myName,
    [式]: "式の結果をキーにする",
    yourName   // 定義済み変数が単独である場合は"yourName": yourNameと同義
};

// 読み出しはPropertyDicts.jlのようにドットも使える
console.log(obj.yourName);
console.log(obj["key-2"]);

// 分割代入で各プロパティを同名の変数に代入する
const score = {
    eng: "89",
    math: "92",
    lastYear: {
        eng: "67",
        math: "80"
    }
};
const { eng, math } = score;

// プロパティの参照・操作
score.bio = "77";
score["poli-econ"] = "82";
delete(score.math);
"bio" in score;   // -> true
score.hasOwnProperty("physics");   // -> false
Object.keys(score);   // 静的メソッド（クラス項を参照）
Object.values(score);
Object.entries(score);
Object.assign({}, score, {history:"77"});   // 第二引数以降を第一引数にマージ（破壊的）
const score2 = {history:"77", ethic:"92"};
const newScore = {...score, ...score2};   // マージ法２、重複時後ろのオブジェクトが優先

// コピー
const person = {
    name:"taro",
    age:22,
    score: {
        eng: 70,
        math: 80
    }
};
// 通常の代入はコピーを作らない、ディープコピーは自前で用意する必要あり
const person2 = person;
person2.age = 24;
console.log(person.age);   // -> 24 変更はオリジナルにも及ぶ
// 直下プロパティはコピー、ネストしたオブジェクトは否
const person3 = Object.assign({}, person);
person3.name = "motoko";
person3.score.eng = 0;
person.name;   // -> "taro"
person.score.eng;   // -> 0  ネストオブジェクトのプロパティは変更される

// Optional Chaining (ES2020)
score.aaa.bbb;   // undefではなくエラー
score?.aaa?.bbb;   // -> undefined
```

### JSON
関数、Symbol、RegExp、Map、SetなどはJSONに変換できない
```javascript
const json = '{ "name": "taro", "age": 22 }';
JSON.parse(json);

const jsonArr = '[1,2,3]';
JSON.parse(jsonArr);   // -> 配列としてパースされる

const obj = {name:"taro", age:22};
JSON.stringify(obj);   // -> JSON string
JSON.stringify(obj, replace);   // 関数または配列replaceによって変換しつつ、4spacesインデントで作成
JSON.stringify(obj, null, space);   // インデントに使う空白の数あるいは"\t"を指定

const tojson = {
    name: "taro",
    toJSON() {
        return "json";
    }
};
JSON.stringify(tojson);   // -> '"json"' オブジェクトにtoJSONメソッドがあればそれを使う
```





## 様々なオブジェクト

### 文字列
正規表現は後日
```javascript
// リテラル
const myStr = 'Hello, world!';   // シングルクォートもダブルと同じ
const newLiteral = `
	hello,
	${yourName}!
`;   // テンプレートリテラル(ES2015)、改行や値の埋め込みができる

// 結合
const strs = "You're " + "welcome";
const myAge = 20;
const valueInString = "I'm " + myAge + " years old.";   // 暗黙的型変換

// 文字コード
strs.charCodeAt(0).toString(16);   // 10進数で文字コードを取得し、16進数に直し文字列で返す
String.fromCharCode(
    0x3069,
    0x3046,
    0x3082
);   // -> 'どうも'


// いつもの文字列操作
const week = "月,火,水,木,金";
week.length;
week.split(",");
week.split(",").join("-");
// 切り出し
week.slice(4);   // -> '水,木,金'
week.slice(-3);   // -> '木,金'
week.slice(4,8);   // -> '水,木,'
week.substring(-3);   // -> .slice(0)、負の添字は0として扱われる
week.substring(8,4);   // -> .slice(4,8)、開始位置と終了位置は順不同
// 探索
week.indexOf("火,水");   // -> 2
week.indexOf("土");   // -> -1  存在しない
week.lastIndexOf(",");   // -> 7
week.startsWith("火,水");   // -> false
week.endsWith("水,木,金");   // -> true
week.includes("水");   // -> true
// 置換
week.replace("木,金", "...");   // -> '月,火,水,...'

/*
タグ付きテンプレート関数 (ES2015)
	juliaの@r_strマクロのように呼び出すと特殊な挙動
	$第一引数に{値}でsplitされた文字列の配列が、それぞれの値が可変長引数として渡される
*/
関数名`index: ${1}, value: ${255}`   // 関数名(["index: ",", value: ",""], 1, 255)
```



### 配列
- 添字は0から
- `length`などのプロパティ
- `isArray`などのメソッド（`typeof arr`の結果は`object`になってしまう）
- TypedArray - ES2015、StaticArrays.jlのようなもの？

#### 基本
```javascript
// アクセス
const arr = ["A","B","C","D","E"];
arr.slice(2);   // -> [ 'C', 'D', 'E' ]
arr.slice(2,4);   // -> [ 'C', 'D' ]
arr.slice(-2);   // -> [ 'D', 'E' ]  後ろから2番目以降

// 結合
arr.concat(["F","G"]);   // 配列以外の値も渡せる
[...arr, "F", "G"];   // ES2015

// 分割代入 (ES2015)
const nums = [1,2,3];
const [one, two, three] = nums;
```

#### 追加、削除（破壊的）
- juliaの末尾!のようには破壊的であるか名前で判別できない
- 配列を便利に扱えるライブラリがある
```javascript
arr.push("F");
arr.unshift("Z");   // 先頭に追加
arr.pop();
arr.shift();   // 先頭を削除
arr.splice(1,2,"b","c");   // -> [ 'A', 'b', 'c', 'D', 'E' ]  削除&追加
arr.splice(1,1);   // arr[1]を削除
arr.length = 0;   // 指定した要素数になるよう切り詰める
```

#### 検索
```javascript
// 特定の要素を持っているか
lang.includes("JavaScript");   // ES2016
lang.some(l => l==="JavaScript");   // コールバック関数を使う方法

// 添字検索
// objectリテラルは新たなオブジェクトを作るためindexOf({ k: "v" })としても厳密に等しくならないので-1
const lang = ["Julia", "JavaScript", "Go", "Rust"];
lang.indexOf("Rust");   // -> 3  方法１
lang.indexOf("Python");   // -> -1  存在しない
lang.findIndex(x => x==="Rust");   // 方法２

// 要素検索
lang.find(l => {
    return l.startsWith("J");
});   // -> 'Julia'  ES2015
```

#### 反復処理
```javascript
const myArray = [1,2,3];

// foreach
myArray.forEach((v, i, self) => {
    console.log(`value: ${v}, index: ${i}`);
});

// map
myArray.map((v, i, self) => {
    return v**3;
});   // -> [1,8,27]

// フィルター
myArray.filter((v, i, self) => {
    return v < 3;
});   // -> [1,2]
```



### マップ (ES2015)
- キーにあらゆるオブジェクトを使える（`Object`は文字列とシンボルのみ）
- プロパティ継承による意図しない結果を得る危険を回避可
- JSONへの変換、およびライブラリ等の関数の対応は`Object`に軍配が上がる
```javascript
const score = new Map(
    [
        ["mathematics", "84"],
        ["japanese", "68"]
    ]
);

// アクセス、操作
score.get("japanese");
score.set("english", "73");
score.size;
score.has("physics");   // -> false
score.delete("mathematics");
score.clear();

// イテレーション
const map = new Map([["A","a"],["B","b"],["C","c"]]);
Array.from(map.keys());   // keyのイテレータから配列を作る
map.entries();   // [key,value]形式のイテレータ、マップ自身もイテレータなのでfor-ofで回せる
```



### Set
```javascript
const subject = new Set([
        "mathematics",
        "japanese",
        "english",
        "japanese"
]);

subject.size;   // -> 3
subject.add("new subject");
subject.has("english");
```



### ほか
以上のオブジェクトのほかにも日付を表す`Date`や数学用の関数などが定義された`Math`などがある。





## スコープ
- julia同様、レキシカルスコープ（関数は呼出元ではなく定義したスコープを参照）

### クロージャ
```javascript
const makeClosure = () => {
    let count = 0;
    function closure() {
        count += 2;
        return count;
    }
    return closure;
};

const myFunc = makeClosure();

myFunc();   // -> 2
myFunc();   // -> 4
```

### `this`キーワード
あるオブジェクトに対してメソッドとして呼び出した場合`this`は対象のオブジェクトを指す。
- 実行コンテキスト
  - スクリプトモード - 最上位スコープで`window`オブジェクト
  - モジュールモード - 〃で`undefined`
  - グローバルオブジェクト用の`globalThis`もある
- 関数およびメソッド
  - 呼び出し元に依存。関数として実行した場合対象オブジェクトがないため`undefined`。
- アロー関数
  - `this`が何を指すか定義時に静的に決まる。

### モダンコードでは気にしなくてよい事
#### 即時関数
グローバルスコープの名前空間の汚染を避けることができるが、ES2015以降はブロックスコープで`let`や`const`を使えば解決する。
```javascript
(function() {
    コード...;
})();
```

#### 巻き上げ
`var`は宣言あるいは初期化の前に参照しても例外が発生しない。`var`では最も近い関数あるいはグローバルスコープの先頭で宣言されたことになる。値の代入は巻き上げられない。

`function`キーワードを使った関数の定義も**宣言**であるため`var`と同じく巻き上げられる。





## クラス

### プロトタイプオブジェクト
関数は新しいオブジェクトをつくる。（その一種としてクラスを含む）関数オブジェクトは`prototype`プロパティを持つ。これは新しいオブジェクトに継承され、自身のプロパティを新しいオブジェクトからも参照できるようになる。プロトタイプチェーンという仕組みのもと自身と自身の元になったクラスのプロパティを探索できる。

### クラスの定義
オブジェクトを実際に作成（インスタンス化）する際、インスタンスはそのクラスのメソッドを継承する。
```javascript
class クラス名 {
    // 省略しても空のコンストラクタが自動設定、値は返さない
    constructor(引数...) {
        コード...;
    }
    // インスタンスが継承する
    プロトタイプメソッド() {
        コード...;
    }
}

const Score = class {
    constructor(eng, math, jap) {
        this.eng = eng;
        this.math = math;
        this.jap = jap;
        this.incrementMath = () => {
            this.math++;
        };
    }
    incrementEng() {
        this.eng++;
    }
};
```

### インスタンスメソッドとプロトタイプチェーン
オブジェクトはまず自身のプロパティを参照し、なければプロトタイプオブジェクトのプロパティを見に行く（メソッド名衝突時インスタンスメソッドが優先）。
```javascript
const myScore = new Score(77, 78, 79);
myScore.eng;   // -> 77

Score.prototype.constructor === Score;   // コンストラクタ===プロトタイプオブジェクト

const yourScore = new Score(77, 78, 79);
myScore.incrementEng === yourScore.incrementEng;    // -> true プロトタイプメソッド
myScore.incrementMath === yourScore.incrementMath;  // -> false インスタンスごとのメソッド
```

### アクセッサープロパティ
プロパティの参照・代入時に呼ばれる特殊なメソッドを定義。外から直接操作されたくないプロパティは`._name`のように命名する習慣がある。
```javascript
const Score = class {
    constructor(eng, math) {
        this._eng = eng;
        this._math = math;
        this._total = eng+math;
    }
    get eng() {
        return this._eng;
    }
    set eng(num) {
        this._eng = num;
        this._total = this._math + num;
    }
    get total() {
        return this._total;
    }
    set total(arg) {
        console.log("You can't assign value to total property!");
    }
    static lastYearAverage() {
        return 177;
    }
};

const score = new Score(50, 60);
score.eng;         // -> 50
score.total;       // -> 110
score.eng = 70;
score.total;       // -> 130
score.total = 150; // You can't assign value to total property!
score.total;       // -> 130
```

### 静的メソッド
インスタンス化なしで使えるメソッド。メソッドの前に`static`キーワードをつけるだけ。
```javascript
Score.lastYearAverage();   // -> 177
```

### 継承
プロトタイプチェーンが働き親クラスのプロパティ・メソッドを引き継ぐ。組み込みクラスも継承できる。
```javascript
class ParentClass {
    constructor(...args) {
        this.a = "parent's property";
        this.b = "common property";
    }
    static parentMethod() {
        console.log("This method is defined in parent");
    }
}

// 子クラスでコンストラクタの定義を省略しても、次のようなコンストラクタが自動で設定
class ChildClass extends ParentClass {
    constructor(...args) {
        super(...args);   // 同じ引数で親コンストラクタが呼び出される
        this.b = `over written ${this.b}`;   // 親コンストラクタ以降はプロパティ継承済み
        this.c = "child's property"
    }
}

// 子クラスで作成したインスタンスは親クラスのインスタンスでもある。
const childInstance = new ChildClass();
childInstance instanceof ParentClass;   // -> true
ChildClass.parentMethod();   // -> static methodも継承
```





## 例外処理
次の通りカッコの有無を除けばjuliaとほぼ同じ構文。組み込みの例外もあり、それらを投げるようにする。
```javascript
try {
    throw new Error("エラー発生");
    コード...;
} catch (error) {
    // exception handling
}
```





## 非同期処理&ECMAScriptモジュール
いったんスキップ





## 出典
- [JavaScript Primer](https://jsprimer.net) - 第一部: 基本文法