# jlreq for SATySFi

## 使い方
### 基本的な型
以下の型は多くの場所で使われます．

#### `zw-or-length`
主に長さを表す型ですが，通常の長さによる指定の他，文書フォントサイズの`n`倍という指定が可能です．
```
type zw-or-length = 
  | Length of length
  | ZW of float
```
と定義されており，`Length(l)`で長さ`l`という指定，`ZW(n)`でフォントサイズの`n`倍という指定です．

#### `nfss`
フォントを表します．現状では，
```
  | Current
  | CurrentType of zw-or-length
  | Roman of zw-or-length
  | Sans of zw-or-length
  | Italic of zw-or-length
  | Font of zw-or-length * (|
    cjk : string * float * float;
    latin : string * float * float;
  |)
```
です．`Current`はフォントの変更を行いません．`CurrenType(n)`はフォントサイズを`n`に変更します．`Roman(n)`，`Sans(n)`，`Italic(n)`はそれぞれ立体，イタリック，サンセリフ/ゴシックです．`n`はフォントのサイズを表します．`Font`ではフォント自身を直接指定します．

## 文書作成用の関数たち．

### `document : 'a -> config ?-> 'b ?-> block-boxes -> document`
メインとなる関数です．`'a`は
```
(|
  title : inline-text;
  author : inline-text;
  date : inline-text;
  show-title : bool;
|)
```
で，文書の属性を指定します．`'b`は現状では
```
(|
  maketitle : ctx -> inline-text -> inline-text -> inline->text -> block-boxes
|)
```
です．`maketitle`ではタイトル出力時の形式を指定します．`maketitle ctx title author date`で呼び出されるので，整形結果を返してください．

`config`は文書の基本版面に関する指定を行う型で，
```
(|
  twoside : bool;
  paper-size : page;
  horizontal-layout : kihon-hanmen-horizontal;
  vertical-layout : kihon-hanmen-vertical;
  header-sep : zw-or-length;
  header-height : zw-or-length;
  line-gap : length;
  font-size : length;
  cjk-font : config-cjkfont;
  latin-font : config-latinfont;
|)
```
です．各中身を以下順番に説明します．

#### `twoside`
`false`が指定されると偶奇ページを同じレイアウトにします．

#### `paper-size`
紙面サイズです．

#### `horizontal-layout`
横方向の基本版面の指定です．`kihon-hanmen-horizontal`は
```
type kihon-hanmen-horizontal = 
  | HorizontalAuto
  | HorizontalCenter of zw-or-length
  | Gutter of (|
      line-length : zw-or-length;
      gutter : zw-or-length;
   |)
  | GutterFore-edge of (|
      gutter : zw-or-length;
      fore-edge : zw-or-length;
    |)

```
と定義されています．`HorizontalAuto`を指定すると紙面の0.75倍がテキスト幅となるように中央配置されます．`HorizontalCenter(l)`はテキスト幅が`l`となるように中央配置されます．`Gutter(|line-length = l1; gutter = l2;|)`とすると，テキスト幅が`l1`，のどの空きが`l2`となります．`GutterFore-edge(|gutter = l1; fore-edge = l2;|)`とすると，のどの空きが`l1`，小口の空きが`l2`となります．この指定はJLReqでは基本的に行わないと記述されています．

#### `vertical-layout`
縦方向の基本版面の設定です．`kihon-hanmen-vertical`は
```
type kihon-hanmen-vertical =
   | VerticalAuto
   | VerticalCenter of zw-or-length
   | Top of (|
       block-length : zw-or-length;
       top-space : zw-or-length;
     |)
   | TopBottom of (|
       top-space : zw-or-length;
       bottom-space : zw-or-length;
     |)
```
と定義されています．`VerticalAuto`を指定すると紙面の0.75倍がテキスト高さとなるように中央配置されます．`VertialCenter(l)`はテキスト高さが`l`となるように中央配置されます．`Top(|block-length = l1; top-space = l2;|)`とすると，テキスト高さが`l1`，天の空きが`l2`となります．`TopBottom(|top-space = l1; bottom-space = l2;|)`とすると，天の空きが`l1`，地の空きが`l2`となります．この指定はJLReqでは基本的に行わないと記述されています．

#### `header-sep`
ヘッダと本文との空きを指定します．

#### `header-height`
ヘッダの高さを指定します．

#### `line-gap`
行間を指定します．

#### `font-size`
基本となるフォントサイズを指定します．

#### `cjk-font`
和文フォントを指定します．`config-cjkfont`は
```
type config-cjkfont =
  | CJKFont-preset-ipaex of float
  | CJKFont of (|
    mincho : string * float * float;
    gothic : string * float * float;
  |)
```
と定義されています．`CJKFont-preset-ipaex(n)`とすると，`font-size`で指定した値の`n`倍のサイズのipaexを使います．`CJKFont`は直接指定です．

#### `latin-font`
欧文フォントを指定します．`config-latinfont`は

```
type config-latinfont = 
  | LatinFont-preset-lmodern of float
  | LatinFont of (|
    roman : string * float * float;
    italic : string * float * float;
    sans : string * float * float;
  |)
```

と定義されています．`LatinFont-preset-lmodern(n)`とすると，`font-size`で指定した値の`n`倍のサイズのLatin Modernを使います．`LatinFont`は直接指定です．

### `+p : [inline-text] block-cmd`
段落です．

### `+pn : [inline-text] block-cmd`
インデントのない段落です．

### `+part : [string?; inline-text?; inline-text?; inline-text; block-text] block-cmd`
部を表します．`+part ?:label ?:runninghead ?:subtitle heading main-text`で使います．`label`は相互参照のためのラベルです．`runninghead`は柱などに出力する見出しを指定します．省略された場合は指定された見出しそのものが使われます．`subtitle`は副題です．`heading`が見出し文字列，`main-text`がその節の中身です．

### `+section : [string?; inline-text?; inline-text?; inline-text; block-text] block-cmd`
節を表します．`+part`と使い方は同じ．

### `+subsection : [string?; inline-text?; inline-text?; inline-text; block-text] block-cmd`
`+section`の一つ下のレベルの見出しです．`+part`と使い方は同じ．

### `+paragraph : [string?; inline-text?; inline-text; inline-text;] block-cmd`
`+subsection`の一つ下のレベルの見出しです．`+paragraph ?:label ?:runninghead ?:heading ?:main-text`で使います．`label`は相互参照のためのラベルです．`runninghead`は柱などに出力する見出しを指定します．省略された場合は指定された見出しそのものが使われます．`heading`が見出し文字列，`main-text`がその節の中身です．

### `\ref : [string] inline-cmd`
`label`で登録した情報を参照します．

### `\ref-page : [string] inline-cmd`
`\ref`と同様ですがこちらはページ数．

### `\footnote : [inline-text] inline-cmd`
脚注です．

### `\figure : [string?; inline-text; block-text] inline-cmd`
図の配置を行います．`\figure ?:label caption innner`で使います．`label`は相互参照のためのラベルです．`caption`ではキャプションを指定します．`innner`で図を出力します．

## マーク機構
TeXでいうmarkの機構を実現するために以下の関数があります．

### `JLReqMark.set-mark : int -> inline-text -> inline-boxes`
`JLReqMark.set-mark index text`の戻り値が埋め込まれた場所で`index`番目のマークを`text`に設定します．`hook-page-break`での設定です．

### `JLReqMark.get-first-mark : int -> int -> inline-text option`
`JLReqMark.set-mark index pageno`で，`pageno`ページで設定された`index`番目の最初のマークを取得します．`index`番目のマークが一切設定されていなければ`None`が返ります．

### `JLReqMark.get-last-mark : int -> int -> inline-text option`
`JLReqMark.set-mark index pageno`で，`pageno`ページで設定された`index`番目の最後のマークを取得します．`index`番目のマークが一切設定されていなければ`None`が返ります．

## スタイル調整用関数
例えば以下の`JLReqHeading.blockheading-scheme`を使い，
```
let-mutable section-counter <- 0
let-block ctx +section = JLReqHeading.blockheading-scheme (|
<設定>
|) ctx section-counter
```
とすれば，新しいスタイルの`+section`を使うことができます．（内部では`+section`はこのように作られています．）

### `JLReqHeading.blockheading-scheme 'a -> int ref -> context -> string ?-> inline-text ?-> inline-text ?-> inline-text -> block-text -> block-boxes`
別行見出しを作成します．`JLReqHeading.blockheading-scheme config counter ctx`とすると，`+section`と同様の書式の関数となります．`counter`はこの見出しの番号のカウンタです．`config`の型は
```
(|
  font : nfss;
  label-font : nfss;
  subtitle-font : nfss;
  gyodori : blockheading-gyodori;
  label-format : int -> inline-text;
  reference-label-format : int -> string;
  subtitle-format : inline-text -> inline-text;
  indent : zw-or-length;
  end-indent : zw-or-length;
  after-label-space : zw-or-length;
  second-heading-text-indent : bool * zw-or-length;
  subtitle-indent : blockheading-subtitle-indent;
  reset-counters : (int ref) list;
  mark-index : int;
  clear-mark-indices : int list;
  mark-format : inline-text -> inline-text -> inline-text;
|)
```
で，細かいスタイルなどをここで調整します．

* `font`：全体のフォントを指定します．
* `label-font`：ラベルのフォントを指定します．
* `subtitle-font`：副題のフォントを指定します．
* `gyodori`：前後の空きを指定します．．型`blockheading-gyodori`は

    ```
    type blockheading-gyodori = 
      | GyodoriCenter of float
      | BeforeLines of float * float
      | BeforeLength of float * length
      | AfterLines of float * float
      | AfterLength of float * length
      | Absolute of length * length
    ```

    と定義されています．
    - `GyodoriCenter(n)`：`n`行取りし，その中央に配置します．
    - `BeforeLines(n,l)`：`n`行取りし，さらにその前に`l`行空きを入れます．
    - `BeforeLength(n,l)`：`n`行取りしますが，前方の空きを`l`に固定します．
    - `AfterLines(n,l)`：`n`行取りし，さらにその後に`l`行空きを入れます．
    - `AfterLength(n,l)`：`n`行取りしますが，後方の空きを`l`に固定します．
    - `Absolute(l1,l2)`：前方の空きを`l1`，後方の空きを`l2`に固定します．

* `label-format`：ラベルの形式を指定します．カウンタの値を受け取り`inline-text`を返します．
* `reference-label-format`：`\ref`などによる引用の際のラベルの形式を指定します．
* `subtitle-format`：副題の形式を指定します．
* `indent`：見出しの字下げ量を指定します．
* `end-indent`：見出しの地上げ量を指定します．
* `after-label-space`：ラベル直後の空きを指定します．
* `second-heading-text-indent`：見出し文字列が二行に渡った際に，二行以降の字下げ量を指定します．`second-heading-text-indent = (b,l)`とすると字下げ量を`l`にします．`b`が`true`ならばラベル頭を起点とした字下げ量になります．`false`ならば見出し文字列の頭を起点とします．
* `subtitle-indent`：副題のインデントです．型`blockheading-subtitle-indent`は

    ````
    type blockheading-subtitle-indent = SubTitleNoBreak | SubTitleIndent of bool * zw-or-length
    ````
    
    と定義されています．`SubTitleNoBreak`を指定すると見出し文字列の直後，副題の直前では改行をしません．この場合インデント量は`second-heading-text-indent`のものが使われます．`SubTitleIndent(b,l)`とすると，副題直前で改行され，副題の字下げ量が`l`となります．`b`の意味は`second-heading-text-indent`と同様です．
    
* `reset-counters`：この関数が呼ばされた際にリセットするカウンタの一覧をリストで渡します．例えば`+section`ではより下位の`+subsection`のカウンタをリセットするために，ここに`+subsection`のカウンタである`subsection-counter`が渡されています．

* `mark-index`：個々に指定された番号のマークに乱し文字列を設定します．
* `clear-mark-indices`：マークを空文字に設定する番号のリストを指定します．
* `mark-format`：マークに設定する形式を指定します．`mark-format label heading`の形で呼ばれ，戻り値がマークに設定されます．`label`はラベル，`heading`は見出し文字列です．



