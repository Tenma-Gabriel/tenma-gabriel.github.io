# [Codeforces Round #757 (Div. 2)](https://codeforces.com/contest/1614)

## A. Divan and a Store

### 分析：

排序后从小到大贪心即可。

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
int a[N];
void solve()
{
    int n, l, r, k; cin >>  n >> l >> r >> k;
    rep(i, 1, n) cin >> a[i];
    sort(a+1, a+1+n);
    int cnt = 0;
    rep(i, 1, n) 
    {
        if(a[i] >= l && a[i] <= r && k >= a[i]) k -= a[i], cnt++;
    }
    cout << cnt << endl;
}

int main()
{
    int T; cin >> T;
    while(T--) solve();
    // system("pause");
}
```

## B. Divan and a New Project

### 分析：

不难观察出可以将$x_0$ 放在零点(当然放其他的点也等价), 然后贪心地往零点两边放.

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
const int N = 1e6+100;

struct node{
    int idx;
    int w;
    bool operator<(const node& o) const{
        return w < o.w;
    }
};

node a[N];
int x[N];
void solve()
{
    int n; cin >> n;
    rep(i, 1, n) { cin >> a[i].w; a[i].w = -a[i].w; a[i].idx = i; }
    sort(a+1, a+1+n);
    int cnt = 1;
    LL ans = 0;
    rep(i, 1, n)
    {
        x[a[i].idx] = (i&1)?cnt:-(cnt++);
        ans += 2ll * abs(x[a[i].idx]) * (LL)(-a[i].w);
    }
    cout << ans << endl;
    rep(i, 0, n) cout << x[i] << " ";cout << endl;
}   

int main()
{
    close();
    int T; cin >> T;
    while(T--) solve();
    // system("pause");
}
```



## C. Divan and bitwise operations

### 分析:

刚开始读错题了, 看了快一个小时, 一开始以为给的限制是区间异或, 最后发现给的限制是区间或 ( .

那如果是区间或就好做了, 不妨首先将所有的元素初始化为0x7ffffff, 然后用限制去区间**与**, 这显然是保证构造出一个符合限制的数组的最简条件.

这里我建了个线段树去维护.

然后考虑构造出一个符合限制的数组之后, 如何计算答案呢?

考虑按位讨论, 记$cnt_1$表示二进制表示中第$i$项为1 的元素的个数, $cnt_0$则反之. 那么第i位对答案的贡献就是选出 奇数个第i项为1的元素 的方案数 乘以 $2^i$.

于是有下式:
$$
Ans = \sum_{i=0} 2^{cnt_{1}-1} \cdot 2^{cnt_0} \cdot 2^i
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
#define lc (p<<1)
#define rc (lc|1)
#define mid ((l+r)>>1)
const int N = 2e5+100;
const int ALLONE = 0x7fffffff;
const LL p = 1e9+7;
int c[N<<2], lazy[N<<2];
int n;
int a[N];
void build(int p = 1, int l = 1, int r = n)
{
    if(l == r) c[p] = 0x7fffffff, lazy[p] = ALLONE;
    else {
        build(lc, l, mid);
        build(rc, mid+1, r);
        c[p] = 0x7fffffff;
        lazy[p] = ALLONE;
    }
}

void push(int p)
{
    c[lc] &= lazy[p];
    c[rc] &= lazy[p];
    lazy[lc] &= lazy[p];
    lazy[rc] &= lazy[p];
    lazy[p] = ALLONE;
}

void update(int tl, int tr, int x, int p=1, int l=1, int r=n)
{
    if(tl > r || tr < l ) return;
    else if(tl <= l && tr >= r) { c[p] &= x; lazy[p] &= x; return; }
    else 
    {
        push(p);
        update(tl, tr, x, lc, l, mid);
        update(tl, tr, x, rc, mid+1, r);
    }
}

void query(int p = 1, int l = 1, int r = n)
{
    if(l == r) a[l] = c[p];
    else {
        push(p);
        query(lc, l, mid);
        query(rc, mid+1, r);
    }
}

LL qpow(LL a, LL b)
{
    LL rev = 1;
    while(b)
    {
        if(b & 1) (rev *= a) %= p;
        a = a * a % p;
        b >>= 1;
    }
    return rev;
}



void solve()
{
    int m; cin >> n >> m;   
    build();
    rep(i, 1, m)
    {
        int l, r, x; cin >> l >> r >> x;
        update(l, r, x);
    }
    query();
    // rep(i, 1, n) cout << a[i] << " "; cout << endl;
    LL ans = 0;
    for(int k = 0; k < 30; k++)
    {
        int o, v; o = v = 0;
        for(int i = 1; i <= n; i++)
        {
            if(a[i] & (1<<k)) o++;
        }
        v = n-o;
        if(o > 0) (ans += qpow(2, o-1) * qpow(2, v) % p * (1<<k) % p) %= p;
    }
    cout << ans << endl;
}   

int main()
{
    close();
    int T; cin >> T;
    while(T--) solve();
    // system("pause");
}
```

## D1. Divan and Kostomuksha (easy version)

### 分析:

考虑dp. 

记$dp[i]$表示把gcd为$i$的一组数放最前时的答案, 此时这组数的先后顺序不论, 但是一定是排在其他数之前的.

记$b[u]$表示含有因子u的数的个数.

根据定义, 不难想出转移方程: $dp[i] = \max_{j|i} dp[i] + b[i] \cdot (i-j)  $

记数组x最大值为C.

这样我们可以用埃氏筛的方式, $O(C\log C)$的dp完所有状态.

那如何求$b$数组呢? 

考虑给了4s时限, n又只有1e5, 直接暴力算即可.

所以最后的时间复杂度是$O(C\log C + n \sqrt{C} )$

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
#define lc (p<<1)
#define rc (lc|1)
#define mid ((l+r)>>1)
typedef LL ll;
const int N = 2e5+100;
const LL p = 1e9+7;
const int MAXN = 5e6+100;
int a[N];
int b[MAXN];
LL dp[MAXN];
inline void div(int x)
{
    for(int i = 1; i*i <= x; ++i)
    {
        if(x%i==0)
        {
            b[i]++;
            if(i*i!=x) b[x/i]++;
        }
    }
}

void solve()
{
    int n; cin >> n;
    rep(i, 1, n) 
    {
        cin >> a[i];
        div(a[i]);
    }
    // rep(i, 1, 5000000) if(b[i]) cout << i << "-> " << b[i] << endl;
    LL ans = n;
    dp[1] = n;
    rep(i, 1, 5000000)
    {
        ans = max(ans, dp[i]);
        
        for(int j = 2; j*i <= 5000000; j++)
        {
            dp[i*j] = max(dp[i*j], dp[i] + ( b[i*j] ) * ( (LL)i*j-i ) );
        }

    }
    cout << ans << endl;
}   

int main()
{
    close();
    solve();
    // system("pause");
}
```



## D2. Divan and Kostomuksha (hard version)

### 分析：

拿上面的代码来试D2是会T的，于是考虑进一步优化。

记$dp[i] \rightarrow dp[j]$表示$dp[i]$更新状态$dp[j]$

从定义上来看，我们尝试是不停地先将拥有共同因数的数的集合不加区分顺序地排在前面，然后再根据状态转移来继续在这个拥有共同因数的数的集合中进行排序（即将拥有更大的共同因数的数的集合排在前面）。

那么显然 对于状态转移来说，每次只转移质数倍是最优的。

更加形式化地来说，$dp[i] \rightarrow dp[j]$ 不会优于$dp[i] \rightarrow dp[k] \rightarrow dp[j]$

$dp[i] \rightarrow dp[j]$对$dp[j]$的贡献是$dp[i]+b[j]\cdot(j-i)$

$dp[i] \rightarrow dp[k] \rightarrow dp[j]$的贡献则是$ dp[i] + b[k]\cdot(k-i) + b[j]\cdot(j-k)$

注意到$b[j] \le b[k]$

则有 
$$
dp[i] + b[k]\cdot(k-i) + b[j]\cdot(j-k)  \\
= dp[i] + (b[k]-b[j]) \cdot (k-i) + b[j]\cdot(j-i) \\
\ge dp[i]+b[j]\cdot(j-i)
$$
所以拿欧拉筛筛一遍质数就行了.

于是算法复杂度就降到了$O(C\log\log C + n \sqrt{C} )$

感觉复杂度还是很玄学, 于是狠下心来交一发, 过了.

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
#define lc (p<<1)
#define rc (lc|1)
#define mid ((l+r)>>1)
typedef LL ll;
const int N = 2e5+100;
const LL p = 1e9+7;
const int MAXN = 2e7+100;

bool isnp[MAXN];
vector<int> primes; // 质数表
void init(int n)
{
    for (int i = 2; i <= n; i++)
    {
        if (!isnp[i])
            primes.push_back(i);
        for (int p : primes)
        {
            if (p * i > n)
                break;
            isnp[p * i] = 1;
            if (i % p == 0)
                break;
        }
    }
    isnp[1] = 1;
}

int a[N];
int b[MAXN];
LL dp[MAXN];
inline void div(int x)
{
    for(int i = 1; i*i <= x; ++i)
    {
        if(x%i==0)
        {
            b[i]++;
            if(i*i!=x) b[x/i]++;
        }
    }
}

void solve()
{
    int n; cin >> n;
    rep(i, 1, n) 
    {
        cin >> a[i];
        div(a[i]);
    }
    LL ans = n;
    dp[1] = n;
    rep(i, 1, 20000000)
    {
        ans = max(ans, dp[i]);
        
        for(int e: primes)
        {
            if(i * e > 20000000) break;
            dp[i*e] = max(dp[i*e], dp[i] + ( b[i*e] ) * ( (LL)i*e-i ) );
        }

    }
    cout << ans << endl;
}   

int main()
{
    close();
    init(20000000);
    solve();
    // system("pause");
}
```

