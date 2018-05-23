# dp

根据网上看的动态规划问题（主要是背包问题），做一些自己的总结。
## 01背包问题

有N件物品和一个容量为V的背包。第i件物品的费用是c[i]，价值是w[i]。求解将哪些物品装入背包可使价值总和最大。
特点是：每种物品仅有一件，可以选择放或不放。
```
f[i][v] = max{f[i-1][v], f[i-1][v-c[i]]+w[i]};
```
这是一个很直接的想法。如果考虑不拿第i件物品，就是f[i-1][v]的价值，考虑拿i件物品的话（因为需要拿i件服务，所以背包空间至少需要v-c[i]的空间），就是f[i-1][v-c[i]]+w[i]的价值。

时间复杂度和空间复杂度都为O(NV)

### 优化

可以将空间复杂度优化到O(V)

```
for i = 1...N
  for v = V...0
    f[v] = max{f[v], f[v-c[i]] + w[i]};
```

从后向前遍历能够这样保证所有物品只拿起一次。

## 完全背包问题
> 有N种物品和一个容量为V的背包，每种物品都有无限件可用。第i种物品的费用是c[i]，价值是w[i]。求解将哪些物品装入背包可使这些物品的费用总和不超过背包容量，且价值总和最大。

和01背包比起来，每件物品可以多次拿起

在01背包分析中，知道从后往前可以保证所有物品只拿起一次，在完全背包中，不需要这样的限制，所以可以直接修改代码如下：
```
for i = 1...N
  for v = 0...V
    f[v] = max{f[v], f[v-c[i]]+w[i]};
 ```
 我们还是分析一下完全背包问题的递推公式
 ```
 f[i][v] = max{f[i-1][v], f[i-1][v-k * c[i]]+k * w[i] | 0 <= k * c[i] <= V  };
 ```
 因为可以拿多次，所以需要乘以k
 
### 优化
让我们来分析下为什么完全背包问题可以优化成上面公式这样。
当我们从前往后遍历时，背包空间（即 v 的大小）是慢慢变大的。假设 i = 0, 而我们此时恰好装入物品 0（此时背包空间 v == c[0]）,v继续变大，当 v == 2 * c[0] 时，我们恰好可以装入两个物品0 (在每一步中，会检测剩余空间大小是否能够装下当前物品 f[v-c[i]]), 当前f[v]的值为f[v-c[0]]+w[0] (f[v]==0), v-c[0]为第一次拿起物品0 的时候加上当前拿起的物品 0。由上可知在v改变的过程中，都会检测当前空间大小是否能够装下当前物品 i (即 f[v-c[i]] )，只要能够装下，就将其加入到背包中。
优化后的时间复杂度为O(NV),空间复杂度为O(V)
 
## 多重背包问题
> 有N种物品和一个容量为V的背包。第i种物品最多有n[i]件可用，每件费用是c[i]，价值是w[i]。求解将哪些物品装入背包可使这些物品的费用总和不超过背包容量，且价值总和最大。

递推公式为：
```
f[i][v] = max{f[i-1][v], f[i-1][v-k * v[i]]+k * w[i] | 0 <= k <= n[i]};
```
复杂度是O(V*Σn[i])。

### 方法一
根据递推公式可以得出：1. 当 v < c[i] 时，f[i][v] = f[i-1][v]; 2. 当 v >= c[i] 时，选取合适的k使f[i][v] = max{f[i-1][v], f[i-1][v-k\*c[i]]+k\*w[i] | 0<k<min{n[i], v/c[i]}}
```
for i = 0...N
  for v = 0...V
    if v < c[i]
      f[i][v] = f[i-1][v]
    else
      count = min{n[i], v/c[i]}
      for j = 0...count
        cur = f[i-1][v-j*c[i]]+j*w[i]
        f[i][v] = max{f[i][v], cur}
```
### 方法二
转换成二进制思想。例如：10 = 1+2+4+3，当背包大小为10时，可以分解成1,2,4,3四个数的和，所以只判断四个数就行，而不需要判断0到10所有的数。

