---
title: 標準正規分布を近似積分する
date: 2020-5-25 12:00:00
categories: math
mathjax: true
---
## 正規分布とは
大学の課題で正規分布の区間[-σ, σ], [-2σ, 2σ], [-3σ, 3σ], [-6σ, 6σ]における確率を計算しろと言われたのでやります。
後日, 教授から積分できないので標準正規分布表を使ったり, excelを使ったりしましょうとのメールが[来ました](https://www.weblio.jp/content/%E9%81%85%E3%81%84 "遅ぇよ")。<br>
正規分布はこんな感じのグラフです。<br>
![正規分布](https://mathtrain.jp/wp-content/uploads/2014/12/seikibunpu.png "正規分布")
なんの指示もないので標準正規分布(期待値が0, 標準偏差1の場合の正規分布)と仮定します。
正規分布がどうちゃらとか何もわからないので, ただ数式をいじります。
標準正規分布は以下のように表せます。
$$
f(z) = \frac{1}{\sqrt{2\pi}}e^{-\frac{z^2}{2}}
$$
これを積分しようとすると
$$
P(a, b)=\frac{1}{\sqrt{2\pi}}\int^b_a{e^{-\frac{z^2}{2}}}
$$
となるわけですが, $e^{z^{2}}$なんてのは多分積分できないので他の方法を考える必要があります。<br>     
こういうときは[WolframAlpha](https://ja.wolframalpha.com/input/?i=integral+of+e%5Ez%5E2&lang=en "積分の結果")先生に教えてもらいます。   

# じゃあどうする
いろいろな方法で近似的に求められそう。

## 区分求積
みんな大好き区分求積です。友人に言われるまでこの方法に気が付きませんでした。
やり方は簡単で下の図のように長方形をたくさん作って面積を足していく。それだけ。
![区分求積](https://hiraocafe.com/note/noteimages/kubunkyuseki3.png "区分求積")

今回はC++を用いて計算を行いました。
ソースコードは以下の通りです。

```C++
include "bits/stdc++.h"
#define int long long
#define double long double
using namespace std;
const double PI = acos(-1); // π = arccos(-1)

double P(double a, double b, int n) { //[a, b]の区間をn分割する。
  double ret = 0;
  for (double z = a; z <= b; z += (b - a) / (double)n) {
    ret += exp(-z * z / 2);
  }
  return (ret / (double)(n + 1)) / sqrt(2 * PI) * (b - a) / (double)n;
}

signed main() {
  cout << fixed << setprecision(20) << P(-1, 1, 100000000) << endl;
}
```
(上のフォントと色が絶妙にものすごく嫌いです。)
時間計算量は(多分)O(n)です。$e^{-\frac{z^{2}}{2}}$の計算が少し大変そうですが。<br>
以下計算結果を示します。<br>


| 区間[-$\sigma$, $\sigma$] | 分割数(n) | 実行時間 | 計算結果(正確な桁まで) |
|   :---:    |   :---:     |  :---:     |  :---:   |
| [-1, 1]    | $10^{8}$    | 1639 ms     |0.68268949(8桁)|
| [-2, 2]    | $10^{8}$    | 1636 ms     |0.95449973(8桁)|
| [-3, 3]    | $10^{8}$    | 1637 ms     |0.997300203(9桁)|
| [-6, 6]    | $10^{8}$    | 1639 ms     |0.9999999980(10桁)|

$10^{8}$回の計算の割には精度が微妙な気がする。。。

# もっと精度を上げたい。


## マクローリン展開
みんな大好きマクローリン展開です。
$$
f(x) = \sum_{n = 0}^{\infty}\frac{f^{(n)}(0)}{n!}x^{n}
$$
すべての関数について展開できるわけではありませんが, ここでは細かい話は[置いときます](https://ja.wikipedia.org/wiki/%E7%9F%A5%E3%82%89%E3%81%AA%E3%81%84)。
$e^{-\frac{z^2}{2}}$を[展開](https://ja.wolframalpha.com/input/?i=maclaurin+series+of+e%5E%28-%5Bx%5D%5E2%2F2%29&lang=en "綺麗に落とし込む方法がわからない")すると
$$
P(a, b) = \frac{1}{2\pi}\int^b_a{\sum{\frac{(-1)^{k}(z)^{2k}}{k!2^{k}}}}dz
$$
の形に変形できて, シンプルに積分して以下の結果が得られます。
$$
P(a,\ b)\ =\ \frac{1}{2\pi}\Biggl[\sum_{k=0}^{\infty}{\frac{(-1)^k}{(2k+1)k!2^k}}z^{2k+1}\Biggr]_a^b
$$
以上よりソースコードは以下のようになります。
```C++
#include "bits/stdc++.h"
#define int long long
#define double long double
using namespace std;
const double PI = acos(-1);

double factorial(int n) { //return n!
  double ret = 1;
  for (int i = 2; i <= n; i++) ret *= i;
  return ret;
}

double calc(double x, int n) { //return P(-∞, x)
  double temp = 0;
  for (int k = 0; k < n; k++) {
    temp += pow(-1.0, k) * pow(x, 2 * k + 1) / factorial(k) / ((double)(2 * k + 1) * pow(2, k));
  }
  double a = temp / sqrt(2 * PI);
  return temp / sqrt(2 * PI);
}

double P(double a, double b, int n) { //return P(a, b)
  return calc(b, n) - calc(a, n);
}

signed main() {
  cout << fixed << setprecision(20) << P(-1, 1, 10000) << endl;
}
```
上のコードではループ毎に$k!$を計算しているので, 計算量O($n^{2}$)です。
前の項を用いればそのような処理は必要ないので, 改善したコードが以下のようになります。
```C++
#include "bits/stdc++.h"
#define int long long
#define double long double
using namespace std;
const double PI = acos(-1);

double calc(double x, int n) { //return P(-∞, x)
  double temp   = 0;
  double fact   = 1;
  double parity = 1;
  double two    = 1;
  double X = x;
  for (int k = 0; k < n; k++) {
    temp += parity * X / fact / ((double)(2 * k + 1) * two);
    fact *= (k + 1);
    two *= 2;
    parity *= -1;
    X *= x * x;
  }
  double a = temp / sqrt(2 * PI);
  return temp / sqrt(2 * PI);
}

double P(double a, double b, int n) { //return P(a, b)
  return calc(b, n) - calc(a, n);
}

signed main() {
  cout << fixed << setprecision(20) << P(-1, 1, 100) << endl;
}
```
これでO(n)となって3000倍ほど速くなりました。<br>
結果は以下の通りです。

| 区間[-$\sigma$, $\sigma$] | 項数 | 実行時間 | 計算結果(正確な桁まで) |
|   :---:    |   :---:     |  :---:     |  :---:   |                                                                        
| [-1, 1]    | $100$    | 2 ms     |0.682689492137085(15桁)|                                                                                                  
| [-2, 2]    | $100$    | 2 ms     |0.954499736103641(15桁)|                                                                                              
| [-3, 3]    | $100$    | 2 ms     |0.9973002039367398(16桁)|                                                                                                   
| [-6, 6]    | $100$    | 2 ms     |0.999999998026(12桁)|

ということで, 区分求積の1000倍近い速度で2倍近い精度が得られました。
もっと項数を増やしたかったのですが, C++のdouble型の制度の限界が来てしまいました。

## いろんな級数展開でもできそう。
積分が絡むと厳しいのでフーリエ級数展開とかは厳しそうですが, 他の級数でもできそう。
