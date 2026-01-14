### flowchart_mod
ブロックから複数の接続線を容易に引き出すため、`flowchart` モジュールを改変した `flowchart_mod` を用います。`flowchart_mod` では、次の機能が追加されます。

* 接続線の位置の指定
* 複数のブロックをまとめて描画

ブロック `block` での接続線の位置（接続位置）`connpos` を `block{connpos}` で変更できます。接続位置の省略時値は 0.5 で、ブロックの辺の中央に相当します。接続位置が 0 は辺の左や下、1 は辺の右や上に相当します。なお、長方形のブロック（`rectangle`）では端点の位置が適切に変更されますが、ブロックの形によっては（`circle` や `diamond` などでは）適切な接続位置にならないことがあります。

接続位置を変更する場合は、次の点に注意します。

* ブロックから出る接続線の向きを明示的に指定します。
* 遅延描画（以前の描画）が、接続位置の省略時値を変更している可能性を考慮します。

関数 `blockconnector` の引数 `term` を `false` に設定した接続線（ここでは `---` 演算子）を用いると、終端の（接続先で接続線に垂直な向きのずれを補う）接続線を描かず、その後、接続先の接続位置を直前の接続線の位置に更新します。

`flowchart_mod` を用いた図の例を示します。
```cpp {cmd=env args=[asyco -M 1mm] output=html}
import flowchart_mod;

unitsize(2cm);

block A = rectangle("A", (-0.5, 0), minwidth=2cm, minheight=2cm);
block B = rectangle("B", (1, 1), minwidth=2cm, minheight=1.2cm);
block C = rectangle("C", (1, 2), minwidth=2cm, minheight=1.2cm);
block D = rectangle("D", (1, 3), minwidth=2cm, minheight=1.2cm);

draw(A, B, C, D);

add(new void(picture pic, transform t) {
  blockconnector operator --  = blockconnector(pic, t, fontsize(8pt));
  blockconnector operator --- = blockconnector(pic, t, fontsize(8pt), false);
  A{0.8} -- Label("0.8", 0, W) -- Right -- Up -- B{0.2}
    -- Label("0.2", 0, S) -- Up -- Arrow --- C;
  A{0.5} -- Label("0.5", 0, W) -- Right -- Up -- Arrow -- B{0.5};
  A{0.2} -- Label("0.2", 0, W) -- Right -- Up -- B{0.8}
    -- Label("0.8", 0, S) -- Up --- C -- Up -- Arrow --- D;
});
```

`A{0.2}` で始まる行で、`--- C` の代わりに `-- C` を用いると、`C` の接続位置が更新されないため、`A{0.8}` で始まる行の `--- C` で更新された `C` の接続位置 0.2 で `C` の接続線が描かれます。また、`--- D` の代わりに `-- D` を用いると、接続線に垂直な向きの（横方向のずれを補う）接続線が `D` に描かれます。
``` cpp
  A{0.2} -- Label("0.2", 0, W) -- Right -- Up -- B{0.8}
    -- Label("0.8", 0, S) -- Up -- C -- Up -- Arrow -- D;
```

```cpp {cmd=env args=[asyco -M 1mm] output=html .hide}
import flowchart_mod;

unitsize(2cm);

block A = rectangle("A", (-0.5, 0), minwidth=2cm, minheight=2cm);
block B = rectangle("B", (1, 1), minwidth=2cm, minheight=1.2cm);
block C = rectangle("C", (1, 2), minwidth=2cm, minheight=1.2cm);
block D = rectangle("D", (1, 3), minwidth=2cm, minheight=1.2cm);

draw(A, B, C, D);

add(new void(picture pic, transform t) {
  blockconnector operator --  = blockconnector(pic, t, fontsize(8pt));
  blockconnector operator --- = blockconnector(pic, t, fontsize(8pt), false);
  A{0.8} -- Label("0.8", 0, W) -- Right -- Up -- B{0.2}
    -- Label("0.2", 0, S) -- Up -- Arrow --- C;
  A{0.5} -- Label("0.5", 0, W) -- Right -- Up -- Arrow -- B{0.5};

  blockconnector operator --  = blockconnector(pic, t, red + fontsize(8pt));
  A{0.2} -- Label("0.2", 0, W) -- Right -- Up -- B{0.8}
    -- Label("0.8", 0, S) -- Up -- C -- Up -- Arrow -- D;
});
```

`unitsize` に負の値を設定しても、ブロックの接続位置を指定する値には影響しません。接続線の向きを指定する `Up` は `Down` に変更します。

```cpp {cmd=env args=[asyco -M 1mm] output=html}
import flowchart_mod;

unitsize(2cm, -2cm);

block A = rectangle("A", (-0.5, 0), minwidth=2cm, minheight=2cm);
block B = rectangle("B", (1, 1), minwidth=2cm, minheight=1.2cm);
block C = rectangle("C", (1, 2), minwidth=2cm, minheight=1.2cm);
block D = rectangle("D", (1, 3), minwidth=2cm, minheight=1.2cm);

draw(A, B, C, D);

add(new void(picture pic, transform t) {
  blockconnector operator --  = blockconnector(pic, t, fontsize(8pt));
  blockconnector operator --- = blockconnector(pic, t, fontsize(8pt), false);
  A{0.8} -- Label("0.8", 0, W) -- Right -- Down -- B{0.8}
    -- Label("0.8", 0, N) -- Down -- Arrow --- C;
  A{0.5} -- Label("0.5", 0, W) -- Right -- Down -- Arrow -- B{0.5};
  A{0.2} -- Label("0.2", 0, W) -- Right -- Down -- B{0.2}
    -- Label("0.2", 0, N) -- Down --- C -- Down -- Arrow --- D;
});
```

#### 複雑な図の例
`flowchart_mod` を用いた複雑な図の例を示します。

```cpp {cmd=env args=[asyco -f png -M 1mm -render 4 --img-zoom=0.25x -o asyco_flow] output=html .hide}
import flowchart_mod;

unitsize(1cm);
defaultpen(Helvetica());

block n1 = block(0, 1.5);
block n2 = block(0, -11.75);
block n3 = block(0, -13.5);
block n4 = block(0, -14.5);

block b1 = rectangle(
  pack("MPE", "(VS Code)"), (0, -5.5), minwidth=2.5cm, minheight=11cm);
block b2a = rectangle(
  "Browser", (-1.25, -12.75), minwidth=2cm, minheight=0.8cm);
block b2b = rectangle(
  "pandoc", (1.25, -12.75), minwidth=2cm, minheight=0.8cm);
block b3 = rectangle(
  pack("asyco", "(bash)"), (4, -2.5), fillpen=rgb(1, 1, 0.8),
  minwidth=1.8cm, minheight=5cm);
block b4 = rectangle(
  pack("mepoco", "(bash)"), (4, -10), fillpen=rgb(1, 1, 0.8),
  minwidth=1.8cm, minheight=2cm);
block b5 = rectangle("curl", (7, -3.5));
block b6 = rectangle(Label("curl", grey), (7, -6.5), drawpen=grey);
block b7 = rectangle("asy", (10, -1), minwidth=2.4cm, minheight=2cm);
block b8 = rectangle(
  pack("Asymptote", "http", "server"), (10, -5.5),
  minwidth=2.4cm, minheight=5cm);
block b9 = rectangle("mpost", (10, -10), minwidth=2.4cm, minheight=2cm);

draw(b1, b2a, b2b, b3, b4, b5, b6, b7, b8, b9);

add(new void(picture pic, transform t) {
  blockconnector operator --  = blockconnector(pic, t, fontsize(9pt));
  blockconnector operator --- = blockconnector(pic, t, fontsize(9pt), false);
  Label L = Label("svg / png", 0.5, S);
  n1 -- Down -- Label("md", 0.4, E) -- Arrow --- b1
    -- Down -- Label("html", 0.4, E) -- n2 -- Left -- Down -- Arrow -- b2a
    -- Down --Right -- n3 -- Label("pdf", 0.4, E) -- Arrow -- n4;
  b1 -- Down -- n2 -- Left -- Down -- Arrow -- b2b;
  b2b -- Down -- Left -- n3;

  b1{1-0.5/11} -- Right -- Label("asy", 0.5, N) --- b3
    -- Right -- Arrow --- b7;
  b7{1-1.5/2} -- Left -- L -- Arrow --- b3
    -- Left  -- Label("html", 0.5, S) -- Arrow --- b1;
  b1{1-3.5/11} -- Right -- Label("asy", 0.5, N) --- b3
    -- Right --- b5 -- Right -- Arrow --- b8;
  b8{1-1.5/5} -- Left -- L -- Arrow --- b3
    -- Left  -- Label("html", 0.5, S) -- Arrow --- b1;
  b1{1-9.5/11} -- Right -- Label("mp", 0.5, N) --- b4
    -- Right -- Arrow --- b9;
  b9{1-1.5/2} -- Left -- L -- Arrow --- b4
    -- Left -- Label("html", 0.5, S) -- Arrow --- b1;

  blockconnector operator --  = blockconnector(pic, t, grey + fontsize(9pt));
  blockconnector operator --- = blockconnector(
    pic, t, grey + fontsize(9pt), false);
  b1{1-6.5/11} -- Right -- Label("asy", 0.5, N) --- b6
    -- Right -- Arrow --- b8;
  b8{1-4.5/5} -- Left -- L -- Arrow --- b1;
});
```

`unitsize` の縦方向に負の値を設定した図の例を示します。

```cpp {cmd=env args=[asyco -f png -M 1mm -render 4 --img-zoom=0.25x -o asycat_flow] output=html .hide}
import flowchart_mod;

unitsize(1cm, -1cm);
defaultpen(Helvetica());

block n1a = block(-0.8, -1);
block n1b = block(0.8, -1);
block n2 = block(0, 9.75);
block n3 = block(0, 11.5);
block n4 = block(0, 12.5);

block b1 = rectangle(
  pack("asycat", "(bash)"), (0, 1), fillpen=rgb(1, 1, 0.8), minwidth=2.5cm);
block b2 = rectangle(
  pack("MPE", "(VS Code)"), (0, 6), minwidth=2.5cm, minheight=6cm);
block b3a = rectangle(
  "Browser", (-1.25, 10.75), minwidth=2cm, minheight=0.75cm);
block b3b = rectangle(
  "pandoc", (1.25, 10.75), minwidth=2cm, minheight=0.75cm);
block b4 = rectangle(
  pack("asyco", "(bash)"), (4, 4.5), minwidth=1.8cm, minheight=2cm);
block b5 = rectangle(
  pack("mepoco", "(bash)"), (4, 7.5), minwidth=1.8cm, minheight=2cm);

draw(b1, b2, b3a, b3b, b4, b5);
add(new void(picture pic, transform t) {
  blockconnector operator --  = blockconnector(pic, t, fontsize(9pt));
  blockconnector operator --- = blockconnector(pic, t, fontsize(9pt), false);
  n1a -- Down -- Label("asy\ ...", 0.4, E) -- Arrow --- b1;
  n1b -- Down -- Label("mp\ ...", 0.4, E) -- Arrow --- b1;
  b1{0.5} -- Down -- Label("md", 0.4, E) -- Arrow --- b2
    -- Label("html", 0.4, E) -- Down -- n2 -- Right -- Down -- Arrow -- b3a
    -- Down -- Left -- n3 -- Label("pdf", 0.4, E) -- Arrow -- n4;
  b2 -- Down -- n2 -- Left -- Down -- Arrow -- b3b -- Down -- n3;

  b4{0.7} -- BeginArrow -- Left -- Label("asy", 0.5, N) --- b2;
  b4{0.3} -- Left -- Label("html", 0.5, S) -- Arrow --- b2;
  b5{0.7} -- BeginArrow -- Left -- Label("mp", 0.5, N) --- b2;
  b5{0.3} -- Left -- Label("html", 0.5, S) -- Arrow --- b2;
});
```

---
