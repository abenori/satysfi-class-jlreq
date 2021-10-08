# jlreq for SATySFi

## インストール
[Satyrographos](https://github.com/na4zagin3/satyrographos)を使ってインストールができます．

```sh
opam install satysfi-class-jlreq
satyrographos install
```

## 使い方
基本的な使い方は，標準添付のStdJaなどと同様です．つまり
```
@require: class-jlreq/jlreq
  
document(|
  title = {タイトル};
  author = {著者};
  date = {日付};
  show-title = true; % タイトル表示
  show-toc = false; % 目次非表示
|) '<
  % ここから本文．
  +section{はじめに}<
    +p{最初の段落}
  >
>
```
のようにします．文書の書き方は，The SATySFi bookなどをご覧ください．
次の命令が定義されています．

* `+p`: 段落を記述する．
* `+pn`: インデントのない段落を記述する．
* `+part`: 部の見出しです．``+part ?:(`ラベル`) ?:{柱用見出し} ?:{副題} {見出し} <本文>``のように使います．ラベルは相互参照のために`\ref`の引数などに入れます．柱用見出しは柱に出す文字列で，省略された場合は見出しそのものを使います．
* `+section`，`+subsection`: `+part`より下位のレベルの見出しです．使い方は`+part`と同様．
* `+paragraph`: `+subsection`の一つ下のレベルの見出しです．``+paragraph ?:(`ラベル`) ?:{柱用見出し} {見出し} <本文>``で使います．
* `\ref`: `+section`などを参照します．文書内に``+\section ?:(`label`)....``という記述がある場合，``\ref(`label`);`とすることで該当見出しの番号を文書内に挿入することができます．
* `\ref-page`: `\ref`と同様ですがこちらはページ数．
* `\footnote`: `\footnote{脚注本文}`で使って脚注を出力します．
* `\figure`: ``\figure ?:(`label`) {キャプション} {図}``により使い，図の配置を行います．
* `\hspace`: jlreq長さ（少し下を参照）を引数にとり，文字送り方向空きを挿入します．

`show-toc = true`と変更すると目次が出ます．デフォルトでは目次はタイトルの後，本文の前に出ますが，次のようにして前付をつけることもできます．（二つの`?*`は，ここにある二つのオプション引数が省略されていることを示します．）
```
document(|
 ....
|) ?* ?* ?:'<
  +p{前付です．}
> '<
  % ここから本文．
  +section{はじめに}<
    +p{最初の段落}
  >
>
```

そのほか，いろいろと挙動のカスタマイズが可能です．設定は長さ（length型，`10pt`や`2mm`など）やbool値（`true`または`false`）などのようなSATySFiで定義されているデータ型で多くの場合は指定しますが，次のような型も使います．

* フォント：[satysfi-fssパッケージ](https://github.com/na4zagin3/satysfi-fss)のスタイルを指定します．`[bold;italic]`のように指定します．
* `jlreq長さ`: ``~(jlreq-length @`10pt`)``のように指定します．``~(jlreq-length @`1zw`)``のように指定することもでき，この場合は全角一文字分長さを表します．これの利用には`@require: jlreq0`が必要です．


### 基本版面の設定
紙サイズや行長のような基本版面の設定は，`document`関数の第二引数を使い
```
document(|
 ....
|) ?:(|JLReq.default-config-document with % jlreqのデフォルト設定をもとにする
  paper = A5Paper;% 紙サイズをA5にする．
  font-size = 11pt;% フォントサイズを11ptにする．
|) `<
  ...
  >
>
```
のようにします．（上の前付がある例では省略されていました．）
以下の項目が設定可能です．

* `paper`: 紙サイズです．`A0Paper`，`A1Paper`，`A2Paper`，`A3Paper`，`A4Paper`，`A5Paper`，`USLegal`，`USLetter`の他，`UserDefinedPaper(<横幅>,<縦の長さ>)`のように具体的な値を指定することもできます．デフォルト`A4Paper`．
* `font-size`: フォントのサイズを長さで指定します．デフォルト`10pt`．
* `line-gap`: 行間を長さで指定します．デフォルト`7pt`．
* `two-side`: `true`または`false`です．`true`とすると奇数ページと偶数ページで異なるデザインとなります．デフォルト`false`．
* `column`: 整数値を指定します．`column = n;`とするとn段組になります．現在のところはnは1または2のみがサポートされています．デフォルト`1`．
* `column-gap`: 段間をjlreq長さで指定します．デフォルト``~(jlreq-length @`2.0zw`)``．
* `horizontal-layout`: 横方向のレイアウトです．以下のどれかで設定します．デフォルト`HorizontalAuto`．
    - `HorizontalAuto`: 紙面の0.75倍がテキスト幅となるように中央配置．
    - `HorizontalCenter(<テキスト幅; jlreq長さ>)`: テキスト幅にあわせて中央配置．
    - `Gutter(|line-length = <テキスト幅; jlreq長さ>; gutter = <のどの空き; jlreq長さ>;|)`: テキスト幅とのどの空きから決定します．
    - `GutterFore-edge(|gutter = <のどの空き; jlreq長さ>; fore-edge = <小口の空き; jlreq長さ>;|)`: のどと小口の空きから指定します．
* `vertical-layout`: 縦方向のレイアウトです．以下のどれかで設定します．デフォルト`VerticalAuto`．
    - `VerticalAuto`: 紙面の0.75倍がテキスト高さとなるように中央配置．
    - `VertialCenter(<テキスト高さ; jlreq長さ>)`: テキスト高さが`l`となるように中央配置．
    - `Top(|block-length = <テキスト高さ; jlreq長さ>; top-space = <天の空き; jlreq長さ>;|)`: テキスト高さと天の空きから決定します．
    - `TopBottom(|top-space = <天の空き; jlreq長さ>; bottom-space = <地の空き; jlreq長さ>;|)`: 天と地の空きから決定します．
* `header-sep`: ヘッダやフッタと本文との空きです．jlreq長さで指定します．デフォルト``~(jlreq-length @`1.0zw`)``．
* `header-height`: ヘッダやフッタの高さをjlreq長さで指定します．デフォルト``~(jlreq-length @`1.0zw`)``．
* `font`: フォントの設定を行います．satysfi-fssパッケージにおけるフォントセットを指定します．

## ヘッダとフッタ
ヘッダとフッタに出力されるページ数や柱の指定を行います．ヘッダとフッタをあわせて，ページスタイルと呼びます．[PageStyle](https://github.com/abenori/satysfi-pagestyle)の機能を使っていますのでそちらをご覧ください．

## 定理
プリアンブルに次のように書くことで，定理を出力する命令`+theorem`を定義することができます．
```
@require: jlreq/theorem
let-mutable theorem-counter <- 0
let-block ctx +theorem = JLReqTheorem.theorem-scheme (|JLReqTheorem.default-config-theorem with
  font = [italic]; 本文フォントをイタリックに
|) {定理} theorem-counter ctx
```

これは次のように使います．
```
+theorem ?:(`sugoi-teiri`) ?:({すごい定理}) <
  +p{
    本当にすごい定理なんだけどここに書くには余白が狭すぎる．
  }
>
```

なお，引数のブロックの最初は`+p`でなければなりません．もし直下に`+enumerate`を書きたい場合は
```
+theorem ?:(`sugoi-teiri`) ?:({すごい定理}) <
  +p{}
  +enumerate{
    * 本当にすごい定理なんだけどここに書くには余白が狭すぎる．
  }
>
```
のようにダミーの`+p`を挟みます．

定義時にできる設定は以下の通り．
* `before-space`: 定理の上の空きをjlreq長さで指定します．
* `after-space`: 定理の下の空きをjlreq長さで指定します．
* `font`: 定理自身のフォントを指定します．
* `heading-font`: 定理の見出し部分のフォントを指定します．
* `label-format`: 定理番号の出力方法を指定します．例えば<節番号>.<定理番号>というようにしたければ，`+section`の番号を保持するカウンター`section-counter`を使い，`label-format = (fun cnt -> (arabic !JLReq.section-counter) ^ `.` ^ (arabic cnt));`とします．
* `after-label-space`: 定理見出しと定理本文の間の空きをjlreq長さで指定します．

## 証明
証明を書くためのブロックコマンド`+proof`が定義されています．
```
+proof <
  +p{
    まずXを証明する．これは定義から明らかである．
    XよりYが従うので，このことより直ちにZが成り立つことがわかる．
  }
  +p{
    一方補題からZはAと同値であった．
    よってAが成り立つことが示された．
  }
>
```



## 見出し
新しい見出しを定義したり，すでに定義されている見出しを再定義したりすることができます．
見出しの種類は別行見出しと同行見出しがあります．
定義の大きな形はどちらでも同じです．
例えば新しい見出し`+\chapter`を別行見出しとして定義するには，プリアンブルに
```
let-mutable chapter-counter <- 0 % +chapter用のカウンタ
let-block ctx +chapter = JLReqHeading.block-heading-scheme (|
  <各種設定>
|) chapter-counter ctx
```
とします．既存の命令である`+part`を書き換える場合は
```
let-block ctx +part = JLReqHeading.block-heading-scheme (|JLReq.default-config-part with
  <+partとの差分の各種設定>
|) JLReq.part-counter ctx
```
とします．`JLReq.default-config-part`は`+part`を定義する際に使っている設定で，`JLReq.part-counter`は`+part`用のカウンタです．どちらもjlreq内で定義されています．


### 別行見出し
設定できる項目は次の通り．
* `font`：全体のフォントを指定します．
* `label-font`：ラベルのフォントを指定します．
* `subtitle-font`：副題のフォントを指定します．
* `gyodori`：前後の空きを指定します．次のどちらかです．
    - `GyodoriCenter(n)`：`n`（実数値）行取りし，その中央に配置します．
    - `BeforeLines(n,l)`：`n`（実数値）行取りし，さらにその前に`l`（実数値）行空きを入れます．
    - `BeforeLength(n,l)`：`n`（実数値）行取りしますが，前方の空きを`l`（長さ）に固定します．
    - `AfterLines(n,l)`：`n`（実数値）行取りし，さらにその後に`l`（実数値）行空きを入れます．
    - `AfterLength(n,l)`：`n`（実数値）行取りしますが，後方の空きを`l`（長さ）に固定します．
    - `Absolute(l1,l2)`：前方の空きを`l1`（長さ），後方の空きを`l2`（長さ）に固定します．
* `label-format`：ラベルの形式を指定します．カウンタの値を受け取り`string`を返します．`+subsection`では<節番号>.<小節番号>という形式での出力をしていますが，これは`label-format = (fun n -> (arabic !section-counter) ^ `.` ^ (arabic n));`というように定義をしています．
* `reference-label-format`：`\ref`などによる引用の際のラベルの形式を指定します．カウンタの値を受け取り`string`を返す関数です．
* `subtitle-format`：副題の形式をjlreq長さで指定します．
* `indent`：見出しの字下げ量をjlreq長さで指定します．
* `end-indent`：見出しの地上げ量をjlreq長さで指定します．
* `after-label-space`：ラベル直後の空きをjlreq長さで指定します．
* `second-heading-text-indent`：見出し文字列が二行に渡った際に，二行以降の字下げ量をjlreq長さで指定します．`second-heading-text-indent = (b,l)`とすると字下げ量を`l`にします．`b`が`true`ならばラベル頭を起点とした字下げ量になります．`false`ならば見出し文字列の頭を起点とします．
* `subtitle-indent`：副題のインデントです．
    - `SubTitleNoBreak`: 見出し文字列の直後，副題の直前では改行をしません．この場合インデント量は`second-heading-text-indent`のものが使われます．
    - `SubTitleIndent(b,l)`: 副題直前で改行され，副題の字下げ量が`l`となります．`b`の意味は`second-heading-text-indent`と同様です．    
* `reset-counters`：この関数が呼ばされた際にリセットするカウンタの一覧をリストで渡します．例えば`+section`ではより下位の`+subsection`のカウンタをリセットするために，ここに`+subsection`のカウンタである`subsection-counter`が渡されています．
* `clear-mark-levels`：柱を空にする見出しのレベルをリストで並べます．通常低いレベルの見出しです．
* `mark-format`：柱の書式を指定します．ラベルと見出しの二引数を受け取り（ともにinline-text），inline-textを返す関数です．

### 同行見出し
`level`, `font`, `indent`, `after-label-space`, `after-space`, `label-format`, `reference-label-format`, `mark-format`, `clear-mark-levels`, `reset-counters`が指定でき，`after-space`以外は`blockheading-scheme`と同じ意味を持ちます．`after-space`は見出し文字列と本文との間の空きをjlreq長さで指定します．
## 脚注
脚注の書式を変更するには
```
let-inline ctx \footnote = JLReqFootnote.footnote-scheme (JLReq.default-config-footnote with
  <設定>
|) JLReq.footnote-counter ctx
```
とします，設定は以下の通り．

* `reference-mark-type`：合印の配置方法です．`Interlinear`または`Inline`を指定します．`Interlinear`を指定すると該当項目の真上に配置します．`Inline`を指定すると該当項目後ろの行中に配置します．
* `reference-mark-format`：合印のフォーマットです．カウンタを受け取り実際の出力を返します．
* `reference-mark-font`：合印のフォントを指定します．
* `font`：脚注のフォントです．
* `line-gap`：脚注の行間です．jlreq長さで指定します．
* `indent`：脚注のインデントをjlreq長さで指定します．
* `second-indent`：脚注の二行目以降のインデントをjlreq長さで指定します．一行目からの相対位置です．

## `document`の第三引数
本ドキュメントの頭の方にある「前付がある場合」に省略されていた第三引数を使うと，いくつかの出力を制御できます．`document`関数をオプション引数の省略なしで使うと次のようになります．
```
document(|
  title = {タイトル};
  author = {著者};
  date = {日付};
  show-title = true;
  show-toc = true;
|) ?:(|JLReq.default-config-document with
  <設定>
|) ?:(|JLReq.default-format with
  <出力を制御する設定>
|) ?:'<
  <前付>
> '<
  <本文>
>
```
以下が設定できます．
* `maketitle`; タイトル出力を制御します．`maketitle <context> <タイトル> <著者> <日付>`の形で呼び出され，戻り値の`block-boxes`がタイトルとして出力されます．`<タイトル> <著者> <日付>`はすべて`inline-text`です．
* `table-of-contents`: 目次出力です．`table-of-contents <context> <目次本体>`の形で呼び出され，戻り値の`block-boxes`が目次として出力されます．`<目次本体>`には目次自身が`block-boxes`として入っています．

## 変数
すでに出てきていますが，jlreqが定めている変数をまとめておきます．
* `JLReq.default-config-document`: `document`に渡すデフォルトの設定が入っている．
* `JLReq.default-format`: `document`に渡す第三引数のデフォルト値．
* `JLReq.default-config-part`: `+part`のデフォルト設定が入っている．`JLReq.default-config-section`なども同様．
* `JLReq.part-counter`: `+part`の番号が入っているカウンタ．`JLReq.section-counter`なども同様．mutable．
* `JLReq.default-config-footnote`: `\footnote`のデフォルト設定が入っている．
* `JLReq.footnote-counter`: `\footnote`用カウンタ．mutable．

## ページ数制御
文書途中でページ数の変更などを行うことができます．[PageNumber](https://github.com/abenori/satysfi-pagenumber)の機能を使っていますので，そちらをご覧ください．

## 履歴
* 0.0.1 (2020/03/22) 
    - 最初のバージョン．
* 0.0.2 (2021/04/25) 
    - 定理環境の仕様を変更．
    - >=3段組も指定できるようにした．
    - jlreq長さの指定方法を変更．
    - フォント管理をsatysfi-fssに委ねた．
* 0.0.3 ()
    - ページスタイルとページ数管理をpagestyle，pagenumberに分離．
    - `+proof`を追加．
