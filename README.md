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
有N种物品和一个容量为V的背包，每种物品都有无限件可用。第i种物品的费用是c[i]，价值是w[i]。求解将哪些物品装入背包可使这些物品的费用总和不超过背包容量，且价值总和最大。

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
