# [Codeforces Round #765 (Div. 2)](https://codeforces.com/contest/1625)

## A. Ancient Civilization

### 分析

按位讨论, 对于第i位, 如果1多就选1, 0多就选0. 最后就可以把答案凑出来.

### 代码

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
    int n, l; cin >> n >> l;
    rep(i, 0, l-1) a[i] = 0;;
    rep(i, 1, n){
        int tmp; cin >> tmp;
        rep(j, 0, l-1)
        {
            if(tmp & (1<<j)) a[j]++;
            else a[j]--;
        }
    }
    LL ans = 0;
    rep(i, 1, l) 
    {
        ans += (a[i-1] > 0) * (1<<(i-1));
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

## B. Elementary Particles

### 分析

题意是两个字段有任意一个位置的元素相同即可, 那么我们直接考虑单个元素相同的情况即可.

于是对于两个相同的元素, 分别在位置$i$ 和位置$j$ , 那么显然我们可以构造出的最大子段长度是$len = i+(n-j)$.

观察这个式子发现, 相同的元素应该尽量的近, 那么我们只考虑最近的相同元素即可. 考虑题中给的$a_i$的值域比较小, 直接开个桶就行了.

### 代码

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
const int L = N-1;
int b[N];
int a[N];
void solve()
{
    int n; cin >> n;
    int ans = 0;
    rep(i, 1, n) {
        cin >> a[i];
        if( b[a[i]] ) 
        {
            ans = max(ans, b[a[i]]+n-i);
        }
        b[a[i]] = i;
    }
    rep(i, 1, n) b[a[i]] = 0;
    cout << (ans>0?ans:-1) << endl;
}

int main()
{
    close();
    int T; cin >> T;
    while(T--) solve();
    // system("pause");
}
```

## C. Road Optimization

### 分析

容易想到一个$O(nkM)$的dp, 其中n是限速标志个数, k是最大可移走的数量, M是速度的值域. 

具体来说就是记$dp_{i,j,o}$ 为走完以第$i$个路牌为起点的路段时还剩下$j$个可移走的数量且此时的速度是$o$. 

状态转移方程是:
$$
dp_{i,j,o} = \min
\left\{

	\begin{array}{**lr**}
	dp_{i-1,j-1,o} + (d[i+1]-d[i])\cdot o  \\
	(\min_{c=1}^M dp_{i-1, j, c}) + (d[i+1]-d[i])\cdot a[i] &  &  a[i] = o
	\end{array}
\right.
$$
注意到$o$, 即速度的取值只有$n-1$种, 所以可以优化为$O(n^2k)$, 其中$k\le n-1$, 所以是$O(n^3)$.

然后把第一维用滚动数组滚掉节省空间即可.

### 代码

```cpp
#include <bits/stdc++.h>
using namespace std;
//#pragma GCC optimize(2)
#define close(); 	ios::sync_with_stdio(false);
#define endl '\n'
#define rep(i, l, r) for(int i = l; i <= r; i++)
#define dwn(i, r, l) for(int i = r; i >= l; i--)
typedef long long LL;
const int N = 512;
const int M = 1e4+100;

int dp[N][N];
int a[N], d[N];
int main()
{
    close();
    
    int n, l, k; cin >> n >> l >> k;
    rep(i, 1, n) cin >> d[i];
    rep(i, 1, n) cin >> a[i]; 
    d[n+1] = l;
    memset(dp, 0x3f, sizeof dp);
    dp[k][1] = (d[2]-d[1])*a[1];
    rep(i, 2, n)
    {
        rep(j, 0, k)
        {
            int x = 0x3f3f3f3f;
            rep(o, 1, i-1) x = min(x, dp[j][o]);
            rep(o, 1, i)
            {
                dp[j][o] = min( dp[j+1][o]+ a[o]*(d[i+1]-d[i]), (a[i]==a[o]?x+ a[o]*(d[i+1]-d[i]):0x3f3f3f3f) ) ;
            }
        }
    }

    int ans = 0x3f3f3f3f;
    rep(i, 0, k)
    {
        rep(j, 1, n) 
        {
            ans = min(ans, dp[i][j]);
        }
    }

    cout << ans << endl;
    // system("pause");
}
```

## D. Binary Spiders

### 分析

我们约定最低位为第0位, 最高位为第29位. 

记k的最高位为第$x$位, 记一个数的第$x+1$位到第$29$位为$pre$, 记第$0$位到第$x$位为$suf$.

我们不难发现, 如果两个数的pre不想等的话, 那么这两个数异或的值必然大于k. 因此我们不妨根据pre进行分组. 这样不同组的数之间的异或必然大于k.

考虑分组我们可以用map实现. 

同时根据鸽巢原理, 每组如果选超过2个数, 那么异或出来的第x位必然存在0. 

因此我们用trie求组内的异或最大值, 如果大于等于k, 那么把这两个选出来. 否则选择任一一个.

最终的时间复杂度是$O(n\log n)$.

### 代码

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

struct Trie{
    int top;
    int size;
    int a[N<<5];
    int nxt[N<<5][2];

    void init() 
    { 
        rep(i, 0, top) a[i] = nxt[i][0] = nxt[i][1] = 0;
        top = 1;
        size = 0;
    }

    void ins(int x)
    {
        int cur = 1;
        dwn(i, 30, 0)
        {
            bool sign = ((x>>i)&1);
            if( nxt[cur][sign] == 0 ) nxt[cur][sign] = ++top;
            cur = nxt[cur][sign];
        }
        a[cur] = x; size++;
    }

    int query(int x)
    {
        int cur = 1;
        if(size == 0) return -1;
        dwn(i, 30, 0)
        {
            bool sign = ((x>>i)&1);
            cur = nxt[cur][sign^1]?nxt[cur][sign^1]:nxt[cur][sign];
        }
        return a[cur]^x;
    }

}trie;

int main()
{
    close();
    int n, k; cin >> n >> k;
    if(k == 0) 
    {
        cout << n << endl;
        rep(i, 1, n) cout << i << " "; cout << endl;
        return 0;
    }
    int bits = 0;
    rep(i, 0, 30) if((k>>i)&1) bits = i;
    bits++;
    vector<int> v(n+10);
    map<int, vector<int>> mp;
    rep(i, 1, n) 
    {
        cin >> v[i];    
        int s = 0;
        dwn(j, 30, bits)
        {
            if((v[i]>>j)&1) s^=(1<<j);
        }
        mp[s].push_back(i);
    }
    vector<bool> vis(n+10);
    trie.init();
    int cnt = 0;
    for(auto b: mp)
    {
        for(int x: b.second)
        {
            if(trie.query(v[x]) >= k) 
            {
                for(int y: b.second)
                {
                    if((v[x] ^ v[y]) >= k)  
                    {
                        vis[x] = vis[y] = 1;
                        cnt+=2;
                        trie.init();
                        break;
                    }
                }
            }
            else trie.ins(v[x]);
            if(trie.size == 0) break;
        }
        if(trie.size != 0)
        {
            cnt++;
            vis[b.second[0]] = 1;
            trie.init();
        }
    }
    if(cnt < 2) { cout << -1 << endl; return 0; }
    cout << cnt << endl;
    int c = 0;
    rep(i, 1, n) if(vis[i]) { cout << i <<" "; c++; }
    assert(c == cnt);
    // system("pause");
}
```
