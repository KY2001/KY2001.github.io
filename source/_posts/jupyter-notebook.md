---
title: Pythonでグラフを描く
date: 2020-05-28 12:00
categories: memo
mathjax: true
---

以下Jupyter Notebookを前提としています.
## グラフを描く
ライブラリをインポートする.
```Python
%matplotlib inline
import matplotlib.pyplot as plt
import numpy as np
import scipy
import pandas as pd
import math
```
#### 二次関数 $y = x^2$
```Python
x = np.linspace(0, 10, 1000)
y = x**2
plt.plot(x, y)
```
出力:
{% asset_img 4.jpg %}
#### 正弦波 $y = sin(x)$
```Python
x = np.linspace(0, 2*math.pi, 1000)
y = np.sin(x)
plt.plot(x, y)
```
出力:
{% asset_img 5.jpg %}
以上で取り敢えずグラフがかける.

## 細かい所
#### 1. np.arange()をnp.arrange()とする
#### 2. np.linspace()をnp.linespace()とする
#### 3. np.arange()とnp.linspace(), range()の違い
1. np.linspace()
      閉区間[L, R]についてnp.linspace(L, R, 点の数)と指定する。
      例えば, np.linspace(0, 1, 3) = [0, 0.5, 1], np.linspace(0, 1, 5) = [0, 0.25, 0.5, 0.75, 1]となる.
      つまり点同士の間隔は$(R - L)/(点の数 - 1)$となる。
      グラフを描くという視点では点の数でグラフの滑らかさと配列のサイズが決まるのでこれが使いやすいように感じる.
2. np.arange()
      半開区間[L, R)についてnp.arange(L, R, 点同士の間隔)と指定する.
      例えばnp.arange(0, 5, 1) = [0, 1, 2, 3, 4], np.arange(0, 1, 0.5) = [0, 0.5]となる.
      半開区間なのでRを含まない.
3. range()
      半開区間についてrange(L, R, 点同士の間隔)と指定する.
      一見, np.arange()と同じに見えるが, **点同士の間隔にfloatを指定できない**点や,
      **配列ではなく[ジェネレータ](https://qiita.com/tomotaka_ito/items/35f3eb108f587022fa09)を渡す**点で違いがある.
      取り敢えず, np.arange()を使うほうが良さそう.


#### 4. titleや変数名を表示する.
以下のようにできる.
```Python
x = np.linspace(0, 2*math.pi, 1000)
y = np.sin(x)
plt.title("sin curve") #titleを設定
plt.xlabel('x')        #x軸の変数名
plt.ylabel("y = sin(x)", rotation='0', labelpad=20) #y軸の変数名, rotation=0で横向きに
plt.grid(True) # 網目状のグリッドを表示
plt.rcParams["font.family"] = "Times New Roman" #フォントを変更
plt.plot(x, y)
```
{% asset_img tmp.jpg %}

#### 5. plt.plot
plt.plot(x, y)でグラフが描ける.**xとyは配列もしくはジェネレータ**代入すると動く.
よく使いそうなその他の引数をまとめた.
1. color(c)=: 線の色
英語の先頭のアルファベットを指定する.**黒:'k'**, 青:'b', 緑:'g', 赤:'r', 黄色:'c'(cyan), 紫:'m', 黄色:'y', 白:'w'
2. alpha=: 透過度
グラフの線の透過度を0?1で指定できる。1に近いほど濃くなる.
3. label=: 線の名前(label)を表示
必ずplt.legend()を同時に入力する.
4. linewidth(lw)=: 線の太さ
大きい値を入れるほど太くなる.
```Python
x = np.linspace(0, 2*math.pi, 1000)
y = np.sin(x)
plt.plot(x, y, label='abc')
plt.legend()
```
{% asset_img 6.jpg %}
#### 6. numpy特有の配列に対する操作
numpyは配列全体に様々な操作を行える.例えば, np.array(numpyの配列)では[0, 0, 0] + [1, 1, 1] = [1, 1, 1]: 行列の足し算となるが, 通常のリストでは [0, 0, 0] + [1, 1, 1] = [0, 0, 0, 1, 1, 1]となる.
これが原因で適当にすると思わぬエラーを喰らう.
例えば, 上の正弦波を表示するコードのy = np.sin(x)をmath.sin(x)とするとエラーになる. これはnp.sin(配列)は配列のすべての要素xに対してsin(x)を適応した配列を返すことによる.
イメージではnp.sin([1, 2, 3]) = [sin(1), sin(2), sin(3)]となります。他にもyのすべての値に誤差を足したい場合, 以下のようにするとうまくいかない.
```Python
import random
x = np.linspace(0, 2*math.pi, 1000)
y = np.sin(x) + random.uniform(0, 0.1) #0?0.1のfloat
plt.plot(x, y)
```
{% asset_img 7.jpg %}
これは以下のようにすると治る.
```Python
import random
x = np.linspace(0, 2*math.pi, 1000)
y = np.sin(x)
y = [i + random.uniform(0, 0.1) for i in y]
plt.plot(x, y)
```
{% asset_img 8.jpg %}
つまり, 最初の例では配列のすべての値に等しい誤差を足して, 下の例では配列の一つ一つに異なる誤差を足している.内包表記を使っているが, つまりはfor文ですべての要素に += ranodm.uniform(0, 0.1)をするのと同じ.
#### 7. 操作にかかる時間を測る
操作の前に %timeitと打つと操作と同時にかかった時間を表示する.
