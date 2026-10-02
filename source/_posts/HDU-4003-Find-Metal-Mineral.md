---
title: HDU 4003 Find Metal Mineral
date: 2026-10-02 13:55:05
categories: 题解
tags: 
- 动态规划 DP
- 树形 DP
- 贪心
---

# HDU 4003 Find Metal Mineral

[题目传送门](https://www.517coding.com/contests/2386/problem/D)

## 闲话

已严肃被卡一天。

事实上是昨天一下午、一晚上，今天一中午。

拿到这题首先就想到定义 $f_{u,i}$ 表示在以 $u$ 为根的子树内，使用 $i$ 个机器的最小花费。

结果发现这道题和之前的树形 DP 不太一样，回来的机器是会给现在的机器数量贡献的，根本不可以转移。

若用 $a$ 表示不回来的机器数量，$b$ 表示回来的机器数量，然后捣鼓出来了最优 $i=\max\{a,b\}$ 的结论。

结果发现还是不好转移啊？写了一个 $O(nk^4)$ 的做法，然后发现写得还不对。

懒得继续调试了。重新定义：$f_{u,i}$ 表示在以 $u$ 为根的子树内，还有 $i$ 个机器可以使用的最小花费。

哦然后发现这个貌似是可以转移的，想都不想直接打一个 $O(nk^4)$​。

[代码](https://www.517coding.com/submissions/4687861)

成功过样例！然后喜提 WA28pts。

```
3 1 1
1 2 10
1 3 1
```

接着严肃 hack 掉自己。

结果发现是转移顺序的问题，可以先选别的下去，再选后面的永远不回来。先选别的永远不回来就会错了。

哦那倒闭吧。

点开题解发现是贪心 + 树形 DP，被气笑了。

## 思路

首先考虑贪心策略。

先给出结论：在以 $u$ 为根的子树中，要么派 $i$ 个机器把边全部扫完，且不回到 $u$；要么只派一个机器把边全部扫完，且回到 $u$。

证明：假设使用了 $a$ 个机器不回到 $u$，$b$ 个机器回到 $u$。

不妨给每一条边都标上一个数字 $c$ 表示这条边被经过的次数，注意到以 $u$ 为根的子树内，这 $b$ 个机器经过的路径上的边上面数字 $c$ 都满足 $c=2k\ (k\in \mathbb{Z}^+)$。可能会有多个机器从它们已经抵达的叶子向它们的 LCA 汇合，导致 LCA 以上的边上的数字 $c=2k$，其中 $k$ 为叶子个数。

然后用一个机器替换掉这 $b$ 个机器，可以发现原来这些 $c$ 都变为 $2$​，所以这一定不劣。

接下来发现这个机器一定是要回到 $u$ 的，于是将这 $a$ 个机器中的其中一个换成这个机器，因为没有改变之后 $u$​ 还存在的机器数量，所以也一定不劣。

另外一种极端情况 $a=0$，此时无需替换，只需保留那一个回来的机器即可。

这道题的难点到此为止，最后定义 $f_{u,i}$ 表示在以 $u$ 为根的子树内，使用 $i$ 个不回来的机器的最小花费。

显然有转移：当以 $v$ 为根的子树使用 $0$ 个不回来的机器时，只需使用 $1$ 个回来的机器即可，$f_{u,i}=f_{v,0}+2w$。

否则 $f_{u,i}=\min\limits_{0\le j\le i-1}\{f_{u,j}+f_{v,i-j}+(i-j)w\}$​。

时间复杂度 $O(nk^2)$。

## 代码

```cpp
#include <bits/stdc++.h>
// #define int long long
using namespace std;
typedef long long LL;
const int N = 1e4 + 2;
const int M = 1e1 + 2;
const int P = 131;
const int MOD = 100003;
const int INF = 0x3f3f3f3f;
const LL LINF = 0x3f3f3f3f3f3f3f3f;
const double EPS = 1e-9;
int n, s, k;
LL f[N][M], ans = LINF;
vector<pair<int, int> > gph[N];

void Dfs(int u, int fa) {
    for (auto [v, w] : gph[u]) {
        if (v == fa) {
            continue;
        }
        Dfs(v, u);
        for (int i = k; i >= 0; i--) { 
            f[u][i] += f[v][0] + 2LL * w;
            for (int j = 0; j < i; j++) {
                f[u][i] = min(f[u][i], f[u][j] + f[v][i - j] + 1LL * w * (i - j));
            }
        }
    }
}

signed main() {
    // freopen("rfrt.in", "r", stdin);
    // freopen("rfrt.out", "w", stdout);
    scanf("%d%d%d", &n, &s, &k);
    for (int i = 1, u, v, w; i < n; i++) {
        scanf("%d%d%d", &u, &v, &w);
        gph[u].push_back({v, w}), gph[v].push_back({u, w});
    }
    Dfs(s, 0);
    for (int i = 0; i <= k; i++) {
        ans = min(ans, f[s][i]);
    }
    printf("%lld", ans);
    return 0; 
}

/*
I will rekill.

Shine when. 

not delete until CSP-S 2026 250+.

☆▽☆
*/
```

## 后记

这道题跳出了平常经典树形 DP 的思维，是一道质量较高的贪心 + DP。

挺推荐这道题作为树形 DP 进阶题的。
