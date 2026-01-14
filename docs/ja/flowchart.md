### flowchart
<!--ブロック図やフローチャートは [draw.io](https://www.drawio.com/) が得意な図ですが Asymptote でも描けます。-->

Asymptote ではブロック図やフローチャートを座標に基づいて描きます。`flowchart` モジュールを用いると、これらの図の描画が簡単になります。

最初に座標に基づいてブロックを配置して描きます。
```cpp {cmd=env args=[asyco -M 1mm] output=html}
import flowchart;

unitsize(2cm);

block S = roundrectangle("Start", (0, 0), minwidth=2cm);
block C = diamond("Condition?", (0, -1));
block A = rectangle("Process A", (0, -2.25), minwidth=2cm, minheight=1.2cm);
block B = rectangle("Process B", (1.5, -2.25), minwidth=2cm, minheight=1.2cm);
block E = roundrectangle("End", (0, -3.25), minwidth=2cm);

draw(S);
draw(C);
draw(A);
draw(B);
draw(E);
```

次に接続線を描きます。
```cpp {cmd=env args=[asyco -M 1mm] continue output=html}
add(new void(picture pic, transform t) {
  blockconnector operator --  = blockconnector(pic, t, fontsize(10pt));
  S -- Arrow -- C -- Label("Yes", 0, SE) -- Arrow -- A -- Arrow -- E;
  C -- Label("No", 0, NE) -- Arrow -- B;
});
```

ブロックに接続しない端点や分岐点には、点を配置して接続します。
```cpp {cmd=env args=[asyco -M 1mm] continue output=html}
block n = block(0, -2.75);

dot(n.center, red); // remove this line to erase the red dot

add(new void(picture pic, transform t) {
  blockconnector operator --  = blockconnector(pic, t, fontsize(10pt));
  B -- Down -- n;
});
```

<!--
ラベルは区間ごとに 1 つまでです。区間の両端にラベルを付加できません。どうしても必要であれば、異なるラベルの接続線を重ねて描きます。両端の矢印は `Arrows` を指定すれば描けます。
-->

---
