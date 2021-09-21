---
title: 離散畳み込みを高速フーリエ変換(FFT)で計算
date: 2020-12-31 17:01:51
category: 数学
mathjax: true
katex: true
---
### 離散畳み込み
関数$f$, $g$の畳み込み$f*g$は次のように定義される。

$$
(f*g)(t) = \int f(\tau)g(t - \tau)
$$

関数の値をN点でサンプリングすると

$$
(f*g)(n) = \sum_{m = 0}^{N-1} f\left[m\right]g\left[n-m\right] \ \ \  (n = 0...N-1)
$$

これを離散畳込みという。 素直に計算すると, N回積をとってN-1回その項の総和を取るので計算量は$O(N)$となる。
また, これを0...N-1全てのnについて$(f*g)(n)$を求めると$O(N^2)$になる。
高速フーリエ変換(FFT)を用いるとこれが$O(NlogN)$で計算できる。

### 離散畳み込みの応用例
1. 多項式乗算の高速化
n次のxに関する多項式$f$, $g$についてその積を$h = fg$とする。
このとき$f$, $g$, $h$の$n$次の係数をそれぞれ$a_n$, $b_n$, $c_n$として次が成り立つ。
$$c_n = \sum_{k = 0}^na_kb_{n - k}$$
これは畳み込みそのものである。

2. 相関関数の計算
信号$x(t)$, $y(t)$に対しての相関関数$r_{xy}(\tau)$は
$$r_{xy}(\tau) = \int_\infty^\infty x(t)y(t + \tau)dt$$
離散化して
$$r_{xy}[m] = \sum_{n = 0}^{N-1}x[n]y[n+m]\ \ \  (m = 0...N-1)$$

### DFT(離散フーリエ変換)
離散的信号$g\left[n\right]$に対して, 離散フーリエ変換$G\left[k\right]$は次の式で定義される。
$$
G\left[k\right] = \mathcal{F}[g[n]] = \sum_{n = 0}^{N-1}g\left[n\right]e^{-\frac{j2\pi nk}{N}} \ \ \  (k = 0...N-1)
$$
ここで表記を簡単にするために回転因子$W_N = e^{-\frac{j2\pi}{N}}$を用いると
$$
G\left[k\right] = \sum_{n = 0}^{N-1}g\left[n\right]W_N^{nk} \ \ \  (k = 0...N-1)
$$

### FFT(高速フーリエ変換)
高速フーリエ変換(FFT)とは離散フーリエ変換(DFT)を計算量$O(NlogN)$で計算するためのアルゴリズムである.
FFTは回転因子$W_N$の次の２つの性質によって成り立つ。

$$
  ^\forall m \in \mathbb{Z}, \ W_N^k =  W_N^{k+mN} \tag{1}
$$

$$
  W_N^k =  W_N^l W_N^{k - l} \tag{2}
$$

計算の都合上, データ数を$N = 2^m$とする。
また, データを偶数番目$g_0\left[n\right]$と奇数番目$g_1\left[n\right]$に分ける。 すなわち

$$
g_0\left[n\right] = g\left[2n\right] \ \ \ (n = 0...\frac{N}{2}-1)
$$

$$
g_1\left[n\right] = g\left[2n+1\right]\ \ \  (n = 0...\frac{N}{2}-1)
$$

こうしてDFTの定義式を変形すると

{% mathjax %}
\begin{eqnarray}
G\left[k\right] &=& \sum_{n = 0}^{N-1}g\left[n\right]W_N^{nk}\\
&=& \sum_{n = 0}^{\frac{N}{2}-1}g_0\left[n\right]W_N^{2nk} + \sum_{n = 0}^{\frac{N}{2}-1}g_1\left[n\right]W_N^{(2n+1)k}
\end{eqnarray}
{% endmathjax %}

ここで$W_N = e^{-\frac{j2\pi}{N}}$より$W_N^{2nk} = W_{\frac{N}{2}}^{nk}$であることと, 先の性質から

{% mathjax %}
\begin{eqnarray}
G\left[k\right] &=& \sum_{n = 0}^{\frac{N}{2}-1}g_0\left[n\right]W_{\frac{N}{2}}^{nk} + W_N^k\sum_{n = 0}^{\frac{N}{2}-1}g_1\left[n\right]W_{\frac{N}{2}}^{nk}\\
&=& G_0\left[k\right] + W_N^{nk}G_1\left[k\right]
\end{eqnarray}
{% endmathjax %}

以上からデータ数$N = 2^m$のDFTはデータ数$\frac{N}{2}$のDFTの組み合わせから再帰的に求まることがわかる。

### シグナルフロー図
例えば$N = 2^3 = 8$のFFTは次の図のように計算できる. これをシグナルフロー図という。
手順としては左の$g$から順に右側の黒点を計算していけば良い。
図からわかるようにある一点の$G[k]$を求める場合は$2 + 4 + ... + 2^m \sim 2N$より$O(N)$の計算量が掛かるが, メモ化を行うことで全ての$k$について計算する場合は$O(NlogN)$の計算量となる。

<img src="/2020/12/31/FFT/fig1.png" alt="" title="8点DFT" width="350">

### 離散フーリエ逆変換
離散フーリエ変換の逆演算. つまり離散フーリエ変換を$\mathcal{F}$, 逆変換を$\mathcal{F}^{-1}$で表すと
$$
\mathcal{F}^{-1}\left[\mathcal{F}\left[g\left[n\right]\right]\right] = g\left[n\right]
$$
次のように定義できる。

$$\mathcal{F}^{- 1}\left[G\left[k\right]\right] = g\left[n\right] = \frac{1}{N}\sum_{k = 0}^{N-1}G\left[k\right]W_N^{-nk} = \frac{1}{N}\overline{\sum_{k = 0}^{N-1}\overline{G\left[k\right]}W_N^{nk}}
$$

よって複素共役を取ることでFFTのアルゴリズムから計算ができる。

### 畳み込み with FFT
離散フーリエ変換の性質から
{% mathjax %}
\begin{eqnarray}
(f*g)(n) &=& \sum_{m = 0}^{N} f\left[m\right]g\left[n-m\right]\\
&=& \mathcal{F}^{- 1}\left[\mathcal{F}\left[f\left[m\right]\right]*\mathcal{F}\left[g\left[n-m\right]\right]\right]
\end{eqnarray}
{% endmathjax %}

こうしてFFTから畳み込みが計算できる。

[証明]
上の式の両辺の$\mathcal{F}$をとると

$$
\mathcal{F}\left[(f*g)(n)\right] = \mathcal{F}\left[f\left[m\right]\right]*\mathcal{F}\left[g\left[n-m\right]\right]
$$

$$
\Leftrightarrow \mathcal{F}\left[\sum_{m = 0}^{N} f\left[m\right]g\left[n-m\right]\right] = \mathcal{F}\left[f\left[m\right]\right]*\mathcal{F}\left[g\left[n-m\right]\right]
$$

左辺(left) = 右辺(right)を示す。

{% mathjax %}
\begin{eqnarray}
(left) &=& \mathcal{F}\left[\sum_{m = 0}^{N} f\left[m\right]g\left[n-m\right]\right]\\
&=& \sum_{n = 0}^{N-1} \left( \sum_{m = 0}^{N} f\left[m\right]g\left[n-m\right] \right)W_N^{nk}\\
&=& \sum_{n = 0}^{N-1}\sum_{m = 0}^{N} f\left[m\right]g\left[n-m\right]W_N^{(n - m + m)k}\\
&=& \sum_{m = 0}^{N-1}f\left[m\right]\left( \sum_{n = 0}^{N} g\left[n-m\right]W_N^{(n - m)k} \right)W_N^{mk}\\
&=& \mathcal{F}\left[g\left[n-m\right]\right]\sum_{m = 0}^{N-1}f\left[m\right]W_N^{mk}\\
&=& \mathcal{F}\left[f\left[m\right]\right]*\mathcal{F}\left[g\left[n-m\right]\right]\\
&=& (right)
\end{eqnarray}
{% endmathjax %}

(証明終)
