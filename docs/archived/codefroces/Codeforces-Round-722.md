# Codeforces Round #722 (Div. 2)题解

## A. Eshag Loves Big Arrays

分析：最小的元素是不可能被消除的，其余比最小元素严格大的元素都可以和最小元素组成一对然后被消除。因此最后只会有最小元素留下来。

```cpp
#include <algorithm>
#include <iostream>
using namespace std;
const int N = 3e5+100;
int a[N];
 
int main()
{
    int t; cin >> t;
    while(t--)
    {
        int n; cin >> n;
        for(int i = 0; i < n; i++)
            cin >> a[i];
        sort(a, a+n);
        int cnt = 1;
        for(int i = 1; i < n; i++)
        {
            if(a[i]==a[i-1]) cnt++;
            else break;
        }
        cout << n-cnt << endl;
    }
    // system("pause");
}
```

## B. Sifid and Strange Subsequences

分析: 

负数和零都是必选的. 问题在于正数怎么选? 

考虑题目限制要求的是**"任意两个元素之差绝对值的最小值" < $MAX$**. 

我们注意到**"任意两个元素之差绝对值的最小值"**只会出现在排序后的相邻元素之间.

于是我们决定按升序排完序后, 从前往后走, 维护**"相邻两元素的差的绝对值的最小值"**. 当遇到正数的时候, 如果正数小于等于这个值, 那么加入, 否则break.

```cpp
#include <algorithm>
#include <iostream>
using namespace std;
const int N = 3e5+100;
int a[N];

int main()
{
    int t; cin >> t;
    while(t--)
    {
        int n; cin >> n;
        for(int i = 0; i < n; i++)
            cin >> a[i];
        sort(a, a+n);
        int ans = 0, sub = 0x3f3f3f3f;
        for(int i = 0; i < n; i++)
        {
            if(a[i] <= 0) ans++;
            if( a[i] <= 0 && i > 0 ) sub = min(sub, a[i]-a[i-1]);
            if( a[i] > 0 )
            {
                if(a[i] <= sub) ans++;
                break;
            }
        }
        cout << ans << endl;
    }
    // system("pause");
}
```

## C. Parsa's Humongous Tree

分析:

一个结论是最优结果可以通过 节点上的值只取最大值或者最小值 来达到.

不会证, 但是大家可以试着把取三个节点, 然后把区间画成线段, 把==**各种可能**==的形式都画出来, 再通过==几何形式==来考察比较容易看规律. 比如下图:

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210525133947800.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FyY3RpY19DbGFt,size_16,color_FFFFFF,t_70#pic_center)


可以看出这种情况的最优解是$C.r-A.l$, 可以通过节点取极值达到.

得到这个结论后就很简单了, 直接记忆化大法师就行了.

```cpp
#include <algorithm>
#include <vector>
#include <cstdio>
using namespace std;
typedef long long INT;
const int N = 2e5+100;
vector<int> G[N];
void add(int u, int v)
{
    G[u].push_back(v);
    G[v].push_back(u);
}
INT w[N][2], dp[N][2]; //0-left, 1-right
void init(int n) 
{
    for(int i = 0; i <= n; i++)
    {
        dp[i][0] = dp[i][1] = 0;
        G[i].clear();
    }
}
INT dfs(int i, int s, int fa = 0)
{
    if(i != 1 && G[i].size() == 1) return 0;
    if(dp[i][s]) return dp[i][s];
    INT rev = 0;
    for(int p: G[i])
    {
        if(fa == p) continue;
        INT a, b;
        a = dfs(p, s^1, i) + abs(w[i][s] - w[p][s^1]);
        b = dfs(p, s, i) + abs(w[i][s] - w[p][s]);
        rev += max(a, b);
    }
    return dp[i][s] = rev;
}
int main()
{
    int t; scanf("%d",&t);
    while(t--)
    {
        int n; scanf("%d",&n);
        init(n);
        for(int i = 1; i <= n; i++) scanf("%lld%lld", &w[i][0], &w[i][1]);
        for(int i = 1; i < n; i++)
        {
            int u, v; scanf("%d%d",&u,&v);
            add(u, v);
        }
        INT ans = 0;
        ans = max(dfs(1, 0), dfs(1, 1));
        printf("%lld\n",ans);
    }
    // system("pause");
}
```

## D. Kavi on Pairing Duty

分析: 

不妨设$f_i$为$n=i$是的可行解的数量.

显然$f_1=1$

现在再来考虑$i>1$的情况

$f_i$应该有以下两大类组成部分:

1. 用之前的组合方式转移来的部分.
2. 使得每个线段长为 i的因数, 这样是符合题意, 却又不算在第一种情况中的.

具体情况如下图

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021052513395981.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FyY3RpY19DbGFt,size_16,color_FFFFFF,t_70#pic_center)


故此可以得出递推式: $f_i = \sum_{j=1}^{i-1}{f_j }+ \pi(i),\space\space i>1$, 其中$\pi(i)$是$i$的因数个数.

这时候还有一个问题, 如何求$\pi(i), i\in [1, n]$

不难想到利用类似埃氏筛的思路, 枚举因数去标记其倍数. 函数代码如下:

```cpp
INT pi[N];
void init(int n)
{
    for(int i = 1; i <= n; i++)
    for(int j = 1; i*j <= n; j++)
    pi[i*j]++;
}
```

我们有$O(n(1+\frac{1}{2} + \frac{1}{3} +  \frac{1}{4} + ...+ \frac{1}{n})) = O(n\log n)$

因此该函数的复杂度是$O(n\log n)$的.

最后是ＡＣ代码：

```cpp
#include <iostream>
#include <algorithm>
using namespace std;
typedef long long INT;
const int N = 1e6+100;
INT f[N], pi[N];
void init(int n)
{
    for(int i = 1; i <= n; i++)
    for(int j = 1; i*j <= n; j++)
    pi[i*j]++;
}
int main()
{
    int n; cin >> n;

    init(n);

    f[1] = 1;
    INT sum = 1;
    for(int i = 2; i <= n; i++)
    {
        f[i] = sum + pi[i];
        sum += f[i]; 
        sum %= 998244353ll;
        f[i] %= 998244353ll;
    }
    cout << f[n] << endl;
    // system("pause");
}
```

