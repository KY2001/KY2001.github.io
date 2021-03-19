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

[証明]
$ca \equiv cb \ \ (mod \ m)$ 
$\Leftrightarrow \ c(a - b) \equiv 0 (mod \ m)$
$\Leftrightarrow \ c(a - b) = mt$
$\Leftrightarrow \ a - b = m\frac{t}{c}$

1. $gcd(c, m) = 1$のとき(cとmが互いに素)
   左辺$a - b$は整数より$\frac{t}{c}$は整数であるから
   $$a \equiv b \ \ (mod \  m)$$
2. $gcd(c, m) = G > 1$のとき
   $\Leftrightarrow \ \frac{c}{G}(a - b) = \frac{m}{G}t$と変形できて上の1.に帰着される。
   $$a \equiv b \ \ (mod \ \frac{m}{G})$$

### 1次合同式 $ax \equiv b \ \ (mod \ m)$
1. $gcd(a, m) = 1$のとき(cとmが互いに素)
   1つの解をもつ。
2. $gcd(a, m) = G > 1$のとき
   Gがbの約数であるときのみd個の解をもつ。
(1次合同式$ax \equiv b \ \ (mod \ m)$は不定方程式$ax - b = my$を解くことと同値である。)


