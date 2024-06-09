# [Codeforces Round #756 (Div. 3)](https://codeforces.com/contest/1611)

## A. Make Even

### 分析:

三种情况

1. 本身是偶数, 答案为0
2. 十进制最高位是偶数, 答案为1
3. 十进制非最高位存在偶数, 答案为2

### 代码:

```cpp
#include <bits/stdc++.h>
using namespace std;
//#pragma GCC optimize(2)
#define close(); 	ios::sync_with_stdio(false);
#define endl '\n'
#define rep(i, l, r) for(int i = l; i <= r; i++)
#define dwn(i, r, l) for(int i = r; i >= l; i--)
typedef long long LL;
const int N = 3e5+100;
int a[N];
void solve()
{
    LL n; cin >> n;
    if(n % 2 == 0) cout << 0 << endl;
    else 
    {
        int cnt = 0;
        int x = 0;
        while(n) 
        {
            a[++cnt] = n%10;
            if(a[cnt] % 2 == 0) x = cnt;
            n/=10;
        }
        if(x == 0) cout << "-1" << endl;
        else 
        {
            if(x == cnt) cout << 1 << endl;
            else cout << 2 << endl;
        }
    }
}

int main()
{
    int T; cin >> T;
    while(T--) solve();
    // system("pause");
}
```

## B. Team Composition: Programmers and Mathematicians

### 分析:

显然答案是$\min(\lfloor\frac{a+b}{4}\rfloor, a, b)$

### 代码:

```cpp
#include <bits/stdc++.h>
using namespace std;
//#pragma GCC optimize(2)
#define close(); 	ios::sync_with_stdio(false);
#define endl '\n'
#define rep(i, l, r) for(int i = l; i <= r; i++)
#define dwn(i, r, l) for(int i = r; i >= l; i--)
typedef long long LL;
const int N = 3e5+100;
void solve()
{
    LL a, b; cin >> a >> b;
    cout << min( (a+b)/4, min(a, b) ) << endl;
}

int main()
{
    int T; cin >> T;
    while(T--) solve();
    // system("pause");
}
```

## C. Polycarp Recovers the Permutation

### 分析:

容易发现数组a的最大值n, 一定出现在p数组的两端.

也就是说如果如果数组p的两端不存在n, 那么就是不合法的.

然后我们发现了一种构造做法:

在构造数组a的时候, 把n放在一端, 那么每次拿去构造数组p的元素都是在非n端的元素.

于是只需要把数组p翻转即可得到一种可行的数组a.

### 代码:

```cpp
#include <bits/stdc++.h>
using namespace std;
//#pragma GCC optimize(2)
#define close(); 	ios::sync_with_stdio(false);
#define endl '\n'
#define rep(i, l, r) for(int i = l; i <= r; i++)
#define dwn(i, r, l) for(int i = r; i >= l; i--)
typedef long long LL;
const int N = 3e5+100;
int a[N];
int b[N]; 
void solve()
{
    int n; cin >> n;
    rep(i, 1, n) cin >> a[i];

    if(a[1] == n || a[n] == n)
    {
        dwn(i, n, 1) cout << a[i] << " "; cout << endl;
    }
    else cout << -1 << endl;

}

int main()
{
    close();
    int T; cin >> T;
    while(T--) solve();
    // system("pause");
}
```

## D. Weights Assignment For Tree Edges

### 分析:

首先容易发现一个性质, 对于给定的排列p, 如果存在某个节点的父节点比该节点排名靠后, 那么显然是不合法的.

因为显然父节点到根节点的距离应该更小.

所以我们可以假设当我们计算$w[i]$时, $w[fa[i]]$是已经确定了的.

记$dis[i]$表示节点i到根节点的距离.

于是不难得到方程: 
$$
w[p[i]] = dis[p[i-1]] - dis[fa[p[i-1]]] + 1;
$$

### 代码:

```cpp
#include <bits/stdc++.h>
using namespace std;
//#pragma GCC optimize(2)
#define close(); 	ios::sync_with_stdio(false);
#define endl '\n'
#define rep(i, l, r) for(int i = l; i <= r; i++)
#define dwn(i, r, l) for(int i = r; i >= l; i--)
typedef long long LL;
const int N = 3e5+100;
int p[N], to[N];
LL dis[N], w[N];
int b[N]; 
void solve()
{
    int n; cin >> n;
    int rt = 0;
    rep(i, 1, n) dis[i] = w[i] = 0;
    rep(i, 1, n) { cin >> b[i]; if(b[i] == i)  rt = i; }
    rep(i, 1, n) { cin >> p[i]; to[p[i]] = i; }
    bool f = 1;
    if(p[1] != rt) f = 0;
    else 
    {
        rep(i, 2, n)
        {
            if(to[b[p[i]]] > to[p[i]]) { f = 0; break; }
            w[p[i]] = dis[p[i-1]] - dis[b[p[i]]] + 1;
            dis[p[i]] = dis[b[p[i]]] + w[p[i]];
        }
    }
    if(f == 0) cout << -1 << endl;
    else rep(i, 1, n) cout << w[i] << " "; cout << endl;
}

int main()
{
    close();
    int T; cin >> T;
    while(T--) solve();
    // system("pause");
}
```

## E1. Escape The Maze (easy version)

### 分析:

记d[x]表示节点x距离朋友节点的最近距离, dp[x]表示节点x的深度(即节点x到根节点的距离).

不难发现, 对于满足$dp[x] \ge d[x]$的节点x, 是一定会被朋友抓的, 是不可走的.

于是我们只需要一遍dfs求出dp和d数组, 然后再dfs一遍看看能不能走到叶节点即可.

### 代码:

代码比较憨, 比赛打一半的时候因为~~秋促~~场外走神了.

```cpp
#include <bits/stdc++.h>
using namespace std;
//#pragma GCC optimize(2)
#define close(); 	ios::sync_with_stdio(false);
#define endl '\n'
#define rep(i, l, r) for(int i = l; i <= r; i++)
#define dwn(i, r, l) for(int i = r; i >= l; i--)
typedef long long LL;
const int N = 3e5+100;
const int INF = 0x3f3f3f3f;
vector<int> G[N];
int d[N], dp[N];
void dfs(int x, int fa)
{
    if(x != 1) dp[x] = dp[fa]+1;
    for(int e: G[x]) 
    {
        if(e == fa) continue;
        dfs(e, x);
    }
}

void dfs2(int x, int fa)
{
    if(d[x] == 0) return;
    int t = INF;
    for(int e: G[x]) 
    {
        if(e == fa) continue;
        dfs2(e, x);
        t = min(t, d[e]);
    }
    d[x] = t+1;
}

bool dfs3(int x, int fa)
{
    if(d[x] <= dp[x]) return 0;
    if(G[x].size() == 1 && x != 1) return 1;
    bool f = 0;
    for(int e: G[x])
    {
        if(e == fa) continue;
        f |= dfs3(e, x);
    }
    return f;
}

void solve()
{
    int n, k; cin >> n >> k;
    rep(i, 1, n) dp[i] = 0, d[i] = INF, G[i].clear();
    rep(i, 1, k) { int t; cin >> t; d[t] = 0; }
    rep(i, 1, n-1)
    {
        int u, v; cin >> u >> v;
        G[u].push_back(v);
        G[v].push_back(u);
    }
    dfs(1, 0);
    dfs2(1, 0);
    if(dfs3(1, 0)) cout << "YES\n";
    else cout << "NO\n";

}

int main()
{
    close();
    int T; cin >> T;
    while(T--) solve();
    // system("pause");
}
```

## E2. Escape The Maze (hard version)

### 分析：

就是问说E1里第二遍dfs的时候，被多少个不可走的节点挡回了。

把E1的代码小修小补一下就能过了

### 代码：

```cpp
#include <bits/stdc++.h>
using namespace std;
//#pragma GCC optimize(2)
#define close(); 	ios::sync_with_stdio(false);
#define endl '\n'
#define rep(i, l, r) for(int i = l; i <= r; i++)
#define dwn(i, r, l) for(int i = r; i >= l; i--)
typedef long long LL;
const int N = 3e5+100;
const int INF = 0x3f3f3f3f;
vector<int> G[N];
int d[N], dp[N];
int ans = 0;
void dfs(int x, int fa)
{
    if(x != 1) dp[x] = dp[fa]+1;
    for(int e: G[x]) 
    {
        if(e == fa) continue;
        dfs(e, x);
    }
}

void dfs2(int x, int fa)
{
    if(d[x] == 0) return;
    int t = INF;
    for(int e: G[x]) 
    {
        if(e == fa) continue;
        dfs2(e, x);
        t = min(t, d[e]);
    }
    d[x] = t+1;
}

bool dfs3(int x, int fa)
{
    if(d[x] <= dp[x]) { ans++; return 0; }
    if(G[x].size() == 1 && x != 1) return 1;
    bool f = 0;
    for(int e: G[x])
    {
        if(e == fa) continue;
        f |= dfs3(e, x);
    }
    return f;
}

void solve()
{
    int n, k; cin >> n >> k;
    rep(i, 1, n) dp[i] = 0, d[i] = INF, G[i].clear();
    rep(i, 1, k) { int t; cin >> t; d[t] = 0; }
    rep(i, 1, n-1)
    {
        int u, v; cin >> u >> v;
        G[u].push_back(v);
        G[v].push_back(u);
    }
    dfs(1, 0);
    dfs2(1, 0);
    if(dfs3(1, 0)) cout << -1 << endl;
    else cout << ans << endl;;
    ans = 0;
}

int main()
{
    close();
    int T; cin >> T;
    while(T--) solve();
    // system("pause");
}
```

## F. ATM and Students

### 分析：

题干要求我们求出一个最长连续子序列保证其中所有的前缀和不小于k。

考虑讲问题转化如下:

双指针对数组a求最大连续子序列和（一般算法是如果连续子序列和小于0就弃掉另起炉灶，但是这里要加个系数k，即连续子序列和小于-k才弃掉另起炉灶）。

然后求的时候我们会把数组a分成几个连续子序列的集合，每个连续子序列的前缀和都不小于k。

那么答案就是这些连续子序列的最大长度。

证明如下：

1. 如果答案连续子序列是横跨若干个我们求出来的连续子序列的，那么至少存在一个前缀小于-k，即不合法。
2. 显然我们求出的若干个连续子序列都符合答案要求， 即所有前缀和不小于-k。

> note: 前排大佬好像都用的二分Orz

### 代码：

```cpp
#include <bits/stdc++.h>
using namespace std;
//#pragma GCC optimize(2)
#define close(); 	ios::sync_with_stdio(false);
#define endl '\n'
#define rep(i, l, r) for(int i = l; i <= r; i++)
#define dwn(i, r, l) for(int i = r; i >= l; i--)
typedef long long LL;
const int N = 3e5+100;
const int INF = 0x3f3f3f3f;
LL a[N];

void solve()
{
    int n;
    LL k; 
    cin >> n >> k;
    rep(i, 1, n) cin >> a[i];
    LL sum = k;
    int p = 1, q = 1;
    sum += a[1];
    int x, y, cnt; x= y= cnt= 0;
    while( p <= n && q <= n )
    {
        while(sum >= 0) 
        {
            if(q-p+1>cnt) 
            {
                cnt = q-p+1;
                x = p; y = q;
            }
            ++q;
            if(q > n) break;
            sum += a[q];
        }
        while(sum < 0)
        {
            sum -= a[p]; ++p;
            if(p > n || p > q) break;
        }
    }
    if(x == y && x == 0) cout << -1 << endl;
    else cout << x << " " << y << endl;
}

int main()
{
    close();
    int T; cin >> T;
    while(T--) solve();
    // system("pause");
}
```



