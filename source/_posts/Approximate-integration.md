---
title: 標準正規分布を近似積分する
date: 2020-05-25 12:00
categories: math
mathjax: true
---
## 正規分布
<img src="https://mathtrain.jp/wp-content/uploads/2014/12/seikibunpu.png" alt="" title="8点DFT" width="350">
標準正規分布は以下のように表せる.
$$
f(z) = \frac{1}{\sqrt{2\pi}}e^{-\frac{z^2}{2}}
$$
これを積分しようとすると
$$
P(a, b)=\frac{1}{\sqrt{2\pi}}\int^b_a{e^{-\frac{z^2}{2}}}
$$
となるが, $e^{z^{2}}$は積分できなそうので他の方法を考える必要がありそう.<br>  

## 区分求積
<img src="https://hiraocafe.com/note/noteimages/kubunkyuseki3.png" alt="" title="8点DFT" width="350">

今回はC++で計算を行う.

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
時間計算量はO(n)です.$e^{-\frac{z^{2}}{2}}$の計算が少し大変そうだが.<br>
以下計算結果.(long doubleの平均的な精度は16桁程度らしい.)<br>


| 区間[-$\sigma$, $\sigma$] | 分割数(n) | 実行時間 | 計算結果(正確な桁まで) |
|   :---:    |   :---:     |  :---:     |  :---:   |
| [-1, 1]    | $10^{8}$    | 1639 ms     |0.68268949(8桁)|
| [-2, 2]    | $10^{8}$    | 1636 ms     |0.95449973(8桁)|
| [-3, 3]    | $10^{8}$    | 1637 ms     |0.997300203(9桁)|
| [-6, 6]    | $10^{8}$    | 1639 ms     |0.9999999980(10桁)|


## マクローリン展開
$$
f(x) = \sum_{n = 0}^{\infty}\frac{f^{(n)}(0)}{n!}x^{n}
$$
$e^{-\frac{z^2}{2}}$を[展開](https://ja.wolframalpha.com/input/?i=maclaurin+series+of+e%5E%28-%5Bx%5D%5E2%2F2%29&lang=en)すると
$$
P(a, b) = \frac{1}{2\pi}\int^b_a{\sum{\frac{(-1)^{k}(z)^{2k}}{k!2^{k}}}}dz
$$
以下の結果が得られる.
$$
P(a,\ b)\ =\ \frac{1}{2\pi}\Biggl[\sum_{k=0}^{\infty}{\frac{(-1)^k}{(2k+1)k!2^k}}z^{2k+1}\Biggr]_a^b
$$
よって
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
上のコードではループ毎に$k!$を計算しているので, 計算量O($n^{2}$)。
前の項を用いればそのような処理は必要ないので, 改善したコードが以下のようになる。
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
これで$O(n)$となって3000倍ほど速くなる。<br>

| 区間[-$\sigma$, $\sigma$] | 項数 | 実行時間 | 計算結果(正確な桁まで) |
|   :---:    |   :---:     |  :---:     |  :---:   |                                                                        
| [-1, 1]    | $100$    | 2 ms     |0.682689492137085(15桁)|                                                                                                  
| [-2, 2]    | $100$    | 2 ms     |0.954499736103641(15桁)|                                                                                              
| [-3, 3]    | $100$    | 2 ms     |0.9973002039367398(16桁)|                                                                                                   
| [-6, 6]    | $100$    | 2 ms     |0.999999998026(12桁)|

区分求積の1000倍近い速度でより良い精度が得られた。
