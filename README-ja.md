# jlreq for SATySFi

## 使い方
基本的な使い方は，標準添付のStdJaなどと同様です．つまり
```
  @require jlreq
  
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
* `\ref-page: `\ref`と同様ですがこちらはページ数．
* `\footnote`: `\footnote{脚注本文}`で使って脚注を出力します．
* `\figure`: ``\figure ?:(`label`) {キャプション} {図}``により使い，図の配置を行います．


`show-toc = true`と変更すると目次が出ます．デフォルトでは目次はタイトルの後，本文の前に出ますが，次のようにして前付きをつけることもできます．
```
  document(|
   ....
  |) ?* ?* ?:'<
    +p{前付きです．}
  > '<
    % ここから本文．
    +section{はじめに}<
      +p{最初の段落}
    >
  >
```

そのほか，いろいろと挙動のカスタマイズが可能です．設定は長さ（length型，`10pt`や`2mm`など）やboo値（`true`または`false`）で指定することも多いですが，jlreq特有の設定方法として以下の二つがあります．

* `jlreq長さ`: 以下で`jlreq長さ`として出てきた項目に対しては，次のどちらかで設定をします．
    - `Length(<長さ>)`: `<長さ>`そのもの．`<長さ>`はlength型．
    - `ZW(<実数値>)`: フォントサイズの`<実数値>`倍．
* `フォント`: フォントの設定です．文字サイズや太字にするかを指定します．
    - `Curren` :現在のフォント．
    - `CurrenType(n)`: 現在のフォントですが，フォントサイズを`n`にします．`n`はjlreq長さで指定します．
    - `Roman(n)`，`Sans(n)`，`Italic(n)`: 立体，イタリック，サンセリフ/ゴシックです．`n`はフォントのサイズで，やはりjlreq長さで指定します．
    - `Font`: フォントの直接指定．


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
のようにします．
以下の項目が設定可能です．

* `paper`: 紙サイズです．`A0Paper`，`A1Paper`，`A2Paper`，`A3Paper`，`A4Paper`，`A5Paper`，`USLegal`，`USLetter`の他，`UserDefinedPaper(<横幅>,<縦の長さ>)`のように具体的な値を指定することもできます．デフォルト`A4Paper`．
* `font-size`: フォントのサイズを長さで指定します．
* `line-gap`: 行間を長さで指定します．
* `two-side`: `true`または`false`です．`true`とすると奇数ページと偶数ページで異なるデザインとなります．デフォルト`false`．
* `column`: 整数値を指定します．`column = n;`とするとn段組になります．現在のところはnは1または2のみがサポートされています．
* `column-gap`: 段間をjlreq長さで指定します．
* `horizontal-layout`: 横方向のレイアウトです．以下のどれかで設定します．
    - `HorizontalAuto`: 紙面の0.75倍がテキスト幅となるように中央配置．
    - `HorizontalCenter(<テキスト幅; jlreq長さ>)`: テキスト幅にあわせて中央配置．
    - `Gutter(|line-length = <テキスト幅; jlreq長さ>; gutter = <のどの空き; jlreq長さ>;|)`: テキスト幅とのどの空きから決定します．
    - `GutterFore-edge(|gutter = <のどの空き; jlreq長さ>; fore-edge = <小口の空き; jlreq長さ>;|)`: のどと小口の空きから指定します．
* `vertical-layout`: 縦方向のレイアウトです．以下のどれかで設定します．
    - `VerticalAuto`: 紙面の0.75倍がテキスト高さとなるように中央配置．
    - `VertialCenter(<テキスト高さ; jlreq長さ>)`: テキスト高さが`l`となるように中央配置．
    - `Top(|block-length = <テキスト高さ; jlreq長さ>; top-space = <天の空き; jlreq長さ>;|)`: テキスト高さと天の空きから決定します．
    - `TopBottom(|top-space = <天の空き; jlreq長さ>; bottom-space = <地の空き; jlreq長さ>;|)`: 天と地の空きから決定します．
* `header-sep`: ヘッダやフッタと本文との空きです．jlreq長さで指定します．
* `header-height`: ヘッダやフッタの高さをjlreq長さで指定します．
* `cjk-font`: 和文フォントを指定します．次のどちらかです．
    - `CJKFont-preset-ipaex(<実数値>)`: ipaexを`<実数値>`倍して使います．
    - `CJKFont(|mincho = (<明朝フォント名>,<拡大率>,<ベースライン調整率>); gothic = (<ゴシックフォント名>,<拡大率>,<ベースライン調整率>;|)`: 直接指定します．
*  `latin-font`: 欧文フォントを指定します．次のどちらかです．
    - `LatinFont-preset-lmodern(<実数値>)`: lmodernを`<実数値>`倍して使います．
    - `LatinFont(|roman = (<ローマンフォント名>,<拡大率>,<ベースライン調整率>); italic = (<イタリックフォント名>,<拡大率>,<ベースライン調整率>); sans = (<サンセリフフォント名>,<拡大率>,<ベースライン調整率>);|)` 直接指定します．

## ヘッダとフッタ
ヘッダとフッタに出力されるページ数や柱の指定を行います．ヘッダとフッタをあわせて，ページスタイルと呼びます．まずページスタイルををあらかじめ`JLreqPageStyle.page-style-scheme`を使い次のように作成しておきます．
```
    let page-style-headings = JLReqPageStyle.page-style-scheme (|
      nombre = [% ノンブルの指定
        (|
          position = PageStyleBottomCenter; % 場所はフッタの真ん中
          nombre = (fun pb -> embed-string (arabic pb#page-number)); % ノンブルの出力．ページ数をそのまま出力する．
          font = Roman(ZW(0.8)); % フォント指定
        |);
      ];
      running-head = [% 柱の指定
        (|
          position = PageStyleTopCenter; % 柱の場所はヘッダの中心
          odd = PageStyleFirstMark(JLReq.default-config-subsection#level); % 奇数ページには+subsectionの見出しを出力
          even = PageStyleBotMark(JLReq.default-config-section#level); % 偶数ページには+sectionの見出しを出力
          font = Roman(ZW(0.8));
        |)
      ];
    |)
```
ページスタイルの適用は，プリアンブルにて
```
  register-page-style page-style-headins
```
とします．また，文書中で変更したい場合は，次のように定義した`\set-page-style`を使いこのページスタイルを適用します．
```
   let-inline ctx \set-page-style ps = JLReqPageStyle.register-page-style-inline ps
   
   ....
   <本文内>
   +p{...
      \set-page-style(page-style-headings);% このページ以降上で定義したヘッダとフッタが出力される
      ...
   }
```

上のように，`JlreqPageStyle.page-style-scheme`ではノンブルと柱を独立に設定します．複数のノンブル，柱を設定することができ，各々リストで渡します．各設定では以下を使います．
* `position`: ノンブル，柱共通です．`PageStyleBottomCenter`，`PageStyleBottomLeft`，`PageStyleBottomRight`，`PageStyleTopCenter`，`PageStyleTopLeft`，`PageStyleTopRight`の六カ所から選びます．
* `font`: ノンブル，柱共通です．フォントを指定します．
* `nombre`: ノンブル用です．ノンブルの出力を指定します．`(|page-number : int;|)`という型を受け取り，出力する`inline-boxes`を返す関数を設定します．
* `odd`: 柱用です．奇数ページの柱を指定します．`+section`のような見出しの中身の出力を設定できます．見出しにはレベルが設定されていて，このレベルを使い設定を行います．`+section`のデフォルトの設定は`JLReq.default-config-section`に入っていて，そのレベルは`JLReq.default-config-section#level`で参照できます．`+part`，`+subsection`．`+paragraph`もすべて同様です．ここの設定は，`PageStyleFirstMark(<見出しレベル>)`，`PageStyleBotMark(<見出しレベル>)`，`PageStyleTopMark(<見出しレベル>)`のいずれかを指定します．それぞれ，該当ページの最初の見出し，最後の見出しおよび前ページの最後の見出しに対応します．または，`PageStyleFormat(f)`とすると，`f`の戻り値が出力されます．ただし，`f`は`(|page-number; int;|)`を受け取り`inline-text`を返す関数です．
* `even`: 偶数ページの柱です．ただし，`document`関数で`two-side = false;`が指定されている場合は，この項目は無視され，ページによらず`odd`で指定した柱が出力されます．


