---
title: Pythonでグラフを描く
date: 2020-05-28 12:00
categories: メモ
mathjax: true
---

## グラフを描く
各種ライブラリをインポートする。
```Python
import matplotlib.pyplot as plt
import numpy as np
import scipy
import pandas as pd
import math
import japanize_matplotlib
```
#### 二次関数 $y = x^2$
```Python
x = np.linspace(0, 10, 1000)
y = x ** 2
plt.plot(x, y)
```
出力:
{% asset_img 4.jpg %}

#### 正弦波 $y = sin(x)$
```Python
x = np.linspace(0, 2 * math.pi, 1000)
y = np.sin(x)
plt.plot(x, y)
```
出力:
{% asset_img 5.jpg %}
以上でグラフがかける。

#### 細かい設定
以下のようにできる。
```Python
x = np.linspace(0, 2 * math.pi, 1000)
y = np.sin(x)
plt.figure(dpi=300)
plt.title("グラフのタイトル")                              
plt.xlabel("x軸の変数名")     
plt.ylabel("y軸の変数名")
plt.xlim(x軸の値の下限, 上限)
plt.ylim(y軸の値の下限, 上限)
plt.grid(True)               
plt.plot(x, y)
plt.show()
```
{% asset_img tmp.jpg %}

#### plt.plot
plt.plot(x, y)でグラフが描ける。**xとyは配列もしくはジェネレータ**代入すると動く。
よく使いそうなその他の引数をまとめる。
1. color(c)=: 線の色
英語の先頭のアルファベットを指定する。**黒:'k'**, 青:'b', 緑:'g', 赤:'r', 黄色:'c'(cyan), 紫:'m', 黄色:'y', 白:'w'
2. alpha=: 透過度
グラフの線の透過度を0?1で指定できる。1に近いほど濃くなる。
3. label=: 線の名前(label)を表示
必ずplt.legend()を同時に入力する。
4. linewidth(lw)=: 線の太さ
大きい値を入れるほど太くなる。
```Python
x = np.linspace(0, 2*math.pi, 1000)
y = np.sin(x)
plt.plot(x, y, label="abc")
plt.legend()
```
{% asset_img 6.jpg %}

## np.arange()とnp.linspace(), range()の違い
1. np.linspace()
      閉区間[L, R]についてnp.linspace(L, R, 点の数)と指定する。
      例えば, np.linspace(0, 1, 3) = [0, 0.5, 1], np.linspace(0, 1, 5) = [0, 0.25, 0.5, 0.75, 1]となる.
      つまり点同士の間隔は$(R - L)/(点の数 - 1)$となる。
      グラフを描くという視点では点の数でグラフの滑らかさと配列のサイズが決まるのでこれが使いやすいように感じる。
2. np.arange()
      半開区間[L, R)についてnp.arange(L, R, 点同士の間隔)と指定する。
      例えばnp.arange(0, 5, 1) = [0, 1, 2, 3, 4], np.arange(0, 1, 0.5) = [0, 0.5]となる。
      半開区間なのでRを含まない。
3. range()
      半開区間についてrange(L, R, 点同士の間隔)と指定する。
      一見, np.arange()と同じに見えるが, **点同士の間隔にfloatを指定できない**点や,
      **配列ではなく[ジェネレータ](https://qiita.com/tomotaka_ito/items/35f3eb108f587022fa09)を渡す**点で違いがある。
      取り敢えず, np.arange()を使うほうが良さそう。

#### グラフの保存
```Python
plt.savefig("ファイル名.png", format="png", dpi=300)
```
保存範囲を指定する場合は
```Python
import import matplotlib.transforms as mt
plt.savefig("ファイル名.png", format="png", dpi=300, bbox_inches= mt.Bbox([[xmin, ymin], [xmax, ymax]]))
```
#### 操作にかかる時間を測る
操作の前に %timeitと打つと操作と同時にかかった時間を表示する。

#### テキストファイルの入出力
```Python
with open("パス", mode="r+", encoding="utf8") as f:
      line = f.readline()[:-1]
```
"r+"は読み書き両方できる。readline()の[:-1]は行末の改行('\n')を削除している。