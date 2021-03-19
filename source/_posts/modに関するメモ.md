---
title: modに関するメモ
date: 2021-03-20 02:28:09
category: memo
mathjax: true
---
### 合同式 $ca \equiv cb \ \ (mod \ m)$

1. $gcd(c, m) = 1$のとき(cとmが互いに素)
   $$a \equiv b \ \ (mod \  m)$$
2. $gcd(c, m) = G > 1$のとき
   $$a \equiv b \ \ (mod \ \frac{m}{G})$$

特に$ca \equiv cb \ \ (mod \ cm)$ $\Leftrightarrow$ $a \equiv b \ \ (mod \ m)$

[証明]
$$ca \equiv cb \ \ (mod \ m)$$
$$\Leftrightarrow \ c(a - b) \equiv 0 (mod \ m)$$
不定方程式に変形ができて
$$\Leftrightarrow \ c(a - b) = mt \ \  ($t \in Z$)$$
$$\Leftrightarrow \ a - b = m\frac{t}{c}$$

1. $gcd(c, m) = 1$のとき(cとmが互いに素)
   左辺$a - b$は整数より$\frac{t}{c}$は整数であるから
   $$a \equiv b \ \ (mod \  m)$$
2. $gcd(c, m) = G > 1$のとき
   $\Leftrightarrow \ \frac{c}{G}(a - b) = \frac{m}{G}t$と変形できて上の1.に帰着される。
   $$a \equiv b \ \ (mod \ \frac{m}{G})$$

### モジュラ逆数 $a^{-1} \equiv x \ \ (mod \ m)$の存在条件
aとmが互いに素であること。
このとき$a*a^{-1} \equiv 1 \ \ (mod \ m)$ならば$a^{-1}$は$a$のモジュラ逆数である。

### ユークリッドの互除法(多項式)

$f(t), g(t)$を多項式としてその最大公約数を求める。
ここで$f_0(t) = f(t), f_1(t) = g(t), k = 1$として

1. $f_k(t) = 0$ならば$f_{k-1}(t)$を出力して終了。
2. $f_{k+1}(t) =$$f_{k-1}(t)$%$f_k(t)$としてkに1加えて1.に戻る。

例1: 不定方程式$115x + 366y = 37$の整数解を1つ求める。
定理: $ax + by = 1$が整数解をもつ$\leftrightarrows$aとbが互いに素である。

$$366 = 115 * 3 + 21$$
$$115 = 21 * 5 + 10$$
$$21 = 10 * 2 + 1$$
これらを変形して
$$21 = 366 - 115 * 3$$
$$10 = 115 - 21 * 5$$
$$1 = 21 - 10 * 2$$
下から遡っていくと
$$1 = 21 - 10 * 2 = 21 - (115 - 21 * 5) * 2$$
$$ = 21 * 11 - 115 * 2 = (366 - 115 * 3) * 11 - 115 * 2$$
$$ = 366 * 11 - 115 * 35$$
よって$366 * 11 - 115 * 35 = 1$が得られた。両辺37倍して$366 * 407 - 115 * 1297 = 37$

### 1次合同式 $ax \equiv b \ \ (mod \ m)$

1. $gcd(a, m) = 1$のとき(cとmが互いに素)
   1つの解をもつ。
2. $gcd(a, m) = G > 1$のとき
   Gがbの約数であるときのみd個の解をもつ。
(1次合同式$ax \equiv b \ \ (mod \ m)$は不定方程式$ax - b = my$を解くことと同値である。)