# [Codeforces Round #742 (Div. 2)](https://codeforces.com/contest/1567)

## A. Domino Disaster

描述：

Alice有一个包含2行和n列的网格。她使用大小为1×2的n个多米诺骨牌完全覆盖网格（Alice可以垂直或水平放置它们），并且每个单元格应该由一个多米诺骨牌覆盖。
现在，她决定向鲍勃展示一排网格。帮助Bob找出网格的另一行是什么样子

分析：

略。

代码：

```cpp
#include <algorithm>
#include <iostream>
using namespace std;
char s[105], ans[105];
void solve()
{
    int n; scanf("%d%s",&n,s);
    for(int i = 0; s[i]; i++)
    {
        if(s[i] == 'R' || s[i] == 'L') ans[i] = s[i];
        else if(s[i] == 'U') ans[i] = 'D';
        else ans[i] = 'U';
    }
    ans[n] = '\0';
    printf("%s\n",ans);
}

int main()
{
    int __; scanf("%d",&__);
    while(__--) solve();
    // system("pause");
}
```

## B. MEXor Mixup

描述：

给定两个整数a和b，（a>0 and $b\ge0$) 。

称一个数组arr符合要求，当且仅当$MEX(arr) = a $ and $XOR(arr) = b$。

求符合要求的数组的最小长度。

共T组数据。

分析：

要构造出一个MEX为a的数组，我们显然至少需要a个元素（从0到a-1）。

此时我们得到的数组的异或和为$XOR(0,1,2,...,a-1)$

通过异或的性质，我们不难得知给数组添加上一个整数$c = b \oplus XOR(0,1,...,a-1)$，就能使整个数组的异或和为b。

但是如果c恰好等于a，那么我们就需要用两个整数来构造c。

如果$XOR(0,1,...,a-1)$已经等于b了，那么我们就不要画蛇添足了。

代码：

```cpp
#include <algorithm>
#include <iostream>
#include <queue>
using namespace std;
int aa[300100];
void init()
{
    for(int i = 1; i <= 300001; i++)
    aa[i] = aa[i-1] ^ i;
}

void solve()
{
    int a,b; scanf("%d%d",&a,&b);
    int sum = aa[a-1];
    if(sum == b) printf("%d\n",a);
    else if((b ^ sum) == a) printf("%d\n",a+2);
    else printf("%d\n",a+1);
}

int main()
{
    init();
    int __; scanf("%d",&__);
    while(__--) solve();
    // system("pause");
}
```

## C. Carrying Conundrum

描述：

爱丽丝有点蠢，在进行加法的竖式运算的时候会将进位放在比原本的进位更高一位的地方。

不妨将这种运算定义为一种新运算。

比如：2039+2976=15005

现在给定一个整数n，求有多少种不同的**正整数**对<a,b>进行运算可以得到n。

T组数据。

分析：

记个位数为第1位，十位数为第2位，依次类推。

不能发现，奇数位只能影响奇数位，偶数位只能影响偶数位。

于是我们将原本的a按照奇偶位数拆分成两个数字x和y。

这时候x和y的表现就和普通加法一样了。同时去掉同时为(0, n)的情况即可。

所以答案就是$(x+1)*(y+1)-2$

代码：

```cpp
#include <algorithm>
#include <iostream>
#include <queue>
using namespace std;
#define N 100
#define endl "\n";
int a[N];

void solve()
{
    int n; scanf("%d",&n);
    int cnt = 0;
    while(n) { a[cnt++] = n%10; n/=10; }
    long long x = 0;
    for(int i = cnt-1; i >= 0; i-=2)
    {
        x *= 10;
        x += a[i];
    }
    long long y = 0;
    for(int i = cnt-2; i >= 0; i-=2)
    {
        y *= 10;
        y += a[i];
    }
    long long ans = 0;
    ans = (y+1) * (x+1) - 2;
    printf("%lld\n",ans);
}

int main()
{
    int __; scanf("%d",&__);
    while(__--) solve();
    // system("pause");
}
```

## D. Expression Evaluation Error

描述：

给定一个十进制数s，将其分为n份。然后将n个数全部转成十一进制数。

问，转换成十一进制后n个数的和最大的方案。（$n\le100$)

T组数据。

分析：

我们称一个数**特殊**，当且仅当，我们可以从这个数上面拆分下一个数，使得，原数和拆分后的两个数在十一进制下的和相同。

比如：
$$
11_{11} = 10_{11}+1_{11}\\
400_{11} = 100_{11}+100_{11}+100_{11}+100_{11}\\
23_{11} = 10_{11}+10_{11}+1_{11}+1_{11}+1_{11}
$$
观察不难发现，只有形如$1[0]^*$的数是不特殊(平凡）的。

且不特殊的数可以被拆分成若干个平凡数。

依据题意，我们应该优先将特殊的数拆解成平凡的数。

那么当没有特殊数时怎么办呢？

这时候再拆分必然造成损失，但是我们应该贪心地最小化损失。

观察：
$$
10_{11} - (9_{11}+1_{11}) = 1\\
100_{11} - (90_{11} + 10_{11}) = 11\\
100_{11} - (99_{11} + 1_{11}) = 12\\
1000_{11} - {(999_{11} + 1_{11})} = 242\\
1000_{11} - {(900_{11} + 100_{11})} = 121
$$
从上面的例子可以看出，当我们要拆分平凡数x的时候，将其拆分成$x-x/10, x/10$时的损失是最小的，且x越小，损失越小。

由以上的性质，不难想出贪心策略。

```cpp
#include <algorithm>
#include <iostream>
#include <queue>
#define endl "\n"
using namespace std;
int ans[106];

bool isSpi1(int x)
{
    if(x % 10 > 0) return 0;
    while(x % 10 == 0) x /= 10;
    return x == 1;
}

bool isSpi2(int x)
{
    for(int i = 2; i < 10; i++)
    {
        if(x % i == 0) return 0;
    }
    return 1;
}

int cut(int& x)
{
    int b = 10;
    while(x % b == 0) b*=10;
    x -= b/10;
    return b/10;
}

void solve()
{
    int s, n; scanf("%d%d",&s,&n);
    ans[1] = s; ans[0] = s+1;
    for(int i = 2; i <= n; i++)
    {
        bool f = 0;
        for(int j = 1; j < i; j++)
        {
            if(!isSpi1(ans[j])  && ans[j] > 1) { ans[i] = cut(ans[j]); f = 1; }
        }
        if(!f) 
        {
            int mn = 0;
            for(int j = 1; j < i; j++)
            if(ans[j] > 1 && ans[j] < ans[mn]) mn = j;
            ans[i] = ans[mn]/10; 
            ans[mn] -= ans[i];
        }
    }
    for(int i = 1; i <= n; i++) printf("%d ",ans[i]); printf("\n");
}
int main()
{
    int __; scanf("%d",&__);
    while(__--) solve();
    // system("pause");
}
```

## E. Non-Decreasing Dilemma

描述：

给定一个数列a，长度为n。

进行m次操作，共有两种操作：

1 x y: 将$a_x$更新为y

2 l r: 计算有多少不递减的连续子序列在数组a的区间[l,r]中，并将结果输出。

分析：

考虑一条连续的不递减的长度为k的数组，总共有$(k+1)*k/2$个不递减的连续子序列。

再考虑一个区间$[l,r]$，我们只需要知道区间的连续前缀长度pre，连续后缀长度suf，是否整段连续isDiv，已经被前缀和后缀夹在中间的部分的连续子序列数量sum，就可以$O(1)$求出该区间的答案。

考虑合并两个区间时，也只需要维护以上四个变量即可在O(1)内合并。

于是考虑使用线段树。

```cpp
#include <algorithm>
#include <iostream>
using namespace std;
#define N 1<<20
#define M 1<<18
typedef long long LL;
struct node
{
    LL pre, suf, sum, len;
    bool isDiv;
}c[N];
int n, a[M];

void merge(int p, int mid)
{
    int x = p<<1, y = (p<<1)+1;
    if(a[mid] <= a[mid+1])
    {
        if(c[x].isDiv) c[p].pre = c[x].pre;
        else c[p].pre = c[x].pre + c[y].pre;
        if(c[y].isDiv) c[p].suf = c[y].suf;
        else c[p].suf = c[x].suf + c[y].suf;
        if(c[x].isDiv || c[y].isDiv) c[p].isDiv = 1; else c[p].isDiv = 0;
        c[p].len = c[x].len + c[y].len;
        c[p].sum = c[x].sum + c[y].sum;
        if(c[x].isDiv && c[y].isDiv) 
        {
            LL s = c[x].suf + c[y].pre;
            c[p].sum += s*(s+1)/2;
        }
    }
    else
    {
        c[p].pre = c[x].pre; c[p].suf = c[y].suf;
        c[p].isDiv = 1;
        c[p].len = c[x].len + c[y].len;
        c[p].sum = c[x].sum + c[y].sum;
        if(c[x].isDiv) c[p].sum += c[x].suf*(c[x].suf+1)/2;
        if(c[y].isDiv) c[p].sum += c[y].pre*(c[y].pre+1)/2;
    }
}

void build(int p = 1, int l = 1, int r = n)
{
    if(l == r) { c[p] = {1,1,0,1,0}; }
    else
    {
        int mid = l+r>>1;
        build(p<<1, l, mid);
        build((p<<1)+1, mid+1, r);
        merge(p, mid);
    }
}

void update(int x, int p=1, int l=1, int r=n)
{
    if(l==r) return;
    else if(x<l || x>r) return;
    else 
    {
        int mid = l+r>>1;
        if(x<=mid) update(x, p<<1, l, mid);
        else update(x, (p<<1)+1, mid+1, r);
        merge(p, mid);
    }
}

node query(int tl, int tr, int p = 1, int nl = 1, int nr = n)
{
    if(nl>=tl && nr<=tr) return c[p];
    else if(nl > tr || nr < tl) return {0, 0, 0, 0, 0};
    else
    {
        int mid = nl+nr>>1;
        auto u = query(tl, tr, p<<1, nl, mid);
        auto v = query(tl, tr, (p<<1)+1, mid+1, nr);
        node rev = {0,0,0,0,0};
        if(u.suf == 0) return v;
        else if(v.pre == 0) return u;
        else 
        {
            if(a[mid] <= a[mid+1])
            {
                if(u.isDiv) rev.pre = u.pre;
                else rev.pre = u.pre + v.pre;
                if(v.isDiv) rev.suf = v.suf;
                else rev.suf = u.suf + v.suf;
                if(u.isDiv || v.isDiv) rev.isDiv = 1; else rev.isDiv = 0;
                rev.len = u.len + v.len;
                rev.sum = u.sum + v.sum;
                if(u.isDiv && v.isDiv) 
                {
                    LL s = u.suf + v.pre;
                    rev.sum += s*(s+1)/2;
                }
            }
            else
            {
                rev.pre = u.pre; rev.suf = v.suf;
                rev.isDiv = 1;
                rev.len = u.len + v.len;
                rev.sum = u.sum + v.sum;
                if(u.isDiv) rev.sum += u.suf*(u.suf+1)/2;
                if(v.isDiv) rev.sum += v.pre*(v.pre+1)/2;
            }
        }
        return rev;
    }
}

int main()
{
    int m; scanf("%d%d",&n,&m);
    for(int i = 1; i <= n; i++) scanf("%d",a+i);
    build();
    for(int i = 1; i <= m; i++)
    {
        int f; scanf("%d",&f);
        if(f == 1)
        {
            int x, v; scanf("%d%d",&x,&v);
            a[x] = v;
            update(x);
        }
        else
        {
            int l, r; scanf("%d%d",&l,&r);
            node u = query(l,r);
            LL ans =  0;
            if(u.isDiv) ans = u.sum + u.suf*(u.suf+1)/2 + u.pre*(u.pre+1)/2;
            else ans = u.len * (u.len+1) / 2;
            printf("%lld\n",ans);
        }
    }
    // system("pause");
}
```

