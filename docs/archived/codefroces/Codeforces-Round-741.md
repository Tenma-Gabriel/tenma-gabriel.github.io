# [Codeforces Round #741 (Div. 2)](https://codeforces.com/contest/1562)

[传送门](https://codeforces.com/contest/1562)

## A.  The Miracle and the Sleeper

不难证明答案等于 $r\%(\max(l, \lfloor\frac{r}{2}\rfloor+1))$

```cpp
#include <algorithm>
#include <iostream>
using namespace std;

void solve()
{
    int l, r; cin >> l >> r;
    int x = max(l, r/2+1);
    cout << r%x << endl;
}

int main()
{
    int T; cin >> T;
    while(T--) solve();
    // system("pause");
}
```



## B. Scenes From a Memory

考虑至少含有一个 $\{1,4,6,8,9\}$ 的元素的十进制表达式皆符合题意。

考虑只含$\{2,3,5,7\}$的两位数是合数时符合题意，换言之，两位数中只有$\{23,53,73\}$不符合题意。

考虑三位数必然符合题意，因为如果存在一个三位数x不符合题意，将x进行拆分成两位数必然得到$\{23,53,73\}$中的数字，且不存在$\{1,4,6,8,9\}$，不妨暴力列出，发现无解。

综上，当k大于等于3，必定有解；当k=1时，若$n\in\{1.4,6,8,9\}$有解；当k=2时，当$n\in\{23,53,73\}$无解。

```cpp
#include <algorithm>
#include <iostream>
using namespace std;
char s[100];
int b[10];
void solve()
{
    for(int i = 0; i < 10; i++) b[i] = 0;
    int k; scanf("%d %s",&k,&s);
    for(int i = 0; i < k; i++)
        if(s[i] == '1' || s[i] == '4' || s[i] == '6' || s[i] == '8' || s[i] == '9') 
        {
            printf("1\n%c\n",s[i]); 
            return;
        }
    for(int i = 0; i < k; i++)
    {
        for(int j = 1; j <= 9; j++) 
            if(b[j])
            {
                int x = j*10+s[i]-'0';
                if(x != 23 && x != 53 && x != 37 && x != 73) 
                {
                    printf("2\n%d\n",x);
                    return;
                }
            } 
        b[s[i]-'0'] = 1;
    }
    printf("-1\n");
}

int main()
{
    int T; cin >> T;
    while(T--) solve();
    // system("pause");
}
```



## C. Rings

考虑两种情况：

1. 字符串至少包含一个0。这样我们就可以依照这个0的位置向前构造（2倍），或向后构造（1倍）

2. 字符串不包含0，全为1。这个时候当且仅当长度n为偶数时有解（因为当a和b全为1串的情况下，当且仅当a的长度可以整除b的长度的时候，a可以整除b，列个除法竖式即可直观看出）

```cpp
#include <algorithm>
#include <iostream>
using namespace std;
char s[20100];
void solve()
{
    int n; scanf("%d %s",&n,s);
    bool f = 0;
    int cnt = 0;
    for(int i = 0; i < n; i++)
    {
        if(s[i] == '0') {f = 1; cnt = i+1; break;}
    }
    if(f)
    {
        if(cnt <= n/2) printf("%d %d %d %d\n",cnt,n,cnt+1,n);
        else printf("%d %d %d %d\n",1,cnt,1,cnt-1); 
    }
    else
    {
        if(n % 2 == 0)
        {
            printf("%d %d %d %d\n",1,n,1,n/2);
        }
        else 
        {
            printf("%d %d %d %d\n",1,n-1,1,n/2);
        }
    }
}

int main()
{
    int T; cin >> T;
    while(T--)
    {
        solve();
    }
    // system("pause");
}
```



## D1. Two Hundred Twenty One (easy version)

记a为元素数组，如果是正电荷则$a[i]$为+1，如果是负电荷则为-1.

记b为一个数组，其中$b[i]$表示移除元素$a[i]$以后的剩余下来的数组的*Signed Sum*，特别的，不删除任何元素的*Signed Sum*记为$b_0$

记$f(l,r)$表示数组a的区间$[l,r]$的*Signed Sum*

记a数组的长度为 n。

考虑存在以下几个性质：

性质1：

```cpp
if a[i] == a[i+1]
then abs( b[i] -b[i+1] ) == 0
else abs( b[i] -b[i+1] ) == 2
```

证明：

i. 如果$a_i = a_{i+1}$，由定义易得$b_i=b_{i+1}$. 

ii. 由定义我们有
$$
a[i] != a[i+1]\\
b[i] = f(1,i-1) \pm a[i+1] \mp f(i+2, n) \\
b[i+1] = f(1,i-1) \mp a[i] \mp f(i+2, n)
$$
 所以 $|b_i - b_{i-1}| = |\pm a_{i+1} \mp a_{i}| = 2 \space,\space a_i \ne a_{i+1}$ 



性质2：$n$和$|b_0|$的奇偶性相同。可以由数学归纳法简单证明。这意味着$b[1...n]$的每个元素的奇偶性都相同。



性质3：如果$n$是奇数且$n>1$，则 $b[1]$和$b[n]$异号或者至少有一个为0。证明如下：-1 -1 1 -1 -1

我们有
$$
b[1] = -b[0] + a[1] \\
b[n] = b[0] - a[n]
$$
由上面两个式子不难想到，当$b[0]\ge 2$时，$b[1],b[n]$必然异号；当$b[0]=1$时，不难发现$b[1],b[n]$必然异号或其中一个为0.

由以上三个性质可得，对于当n为奇数的时候，根据性质1和性质3，必然存在$b[k] = 0, \space k\in[1,n]$；当n为偶数时，根据性质2，$\forall k\in[1,n],b[k] \% 2 =1 $。

综上，如果n为奇数，总可以一步达到可行解；当n为偶数，如果当前状态下的*Signed Sum = 0*, 那么无需操作，否则两步可以到达可行解。 

代码如下：

```cpp
#include <algorithm>
#include <iostream>
using namespace std;
const int N = 3e5+100;
char s[N];
int n, m, a[N], f[N];

void work(int l, int r)
{
    int sum = f[r]-f[l-1];
    if(sum == 0) { printf("0\n"); return; }
    if(n % 2 == 0) printf("2\n");
    else printf("1\n");
}

void solve()
{
    scanf("%d%d",&n,&m);
    scanf("%s",s);
    for(int i = 0; i < n; i++) if(s[i] == '+') a[i+1] = 1; else a[i+1] = -1;
    for(int i = 1; i <= n; i++) f[i] = f[i-1] + ( (i%2)?a[i] : -a[i] );
    while(m--)
    {
        int l, r; scanf("%d %d",&l,&r);
        work(l,r);
    }
}

int main()
{
    int T; cin >> T;
    while(T--) solve();
    // system("pause");
}
```



## D2. Two Hundred Twenty One (hard version)

不妨回想D1的题解中所证明的性质1和3，这表明$b[k],k\in[1,n]$是必定存在零点的，且是类连续的（$|b[i] - b[i+1]| = 2 \space or \space 0)$。

于是直接二分找零点即可。

还有个小问题是如何$O(1)$求给定区间$[l,r]$的$b[k]$, 可以用下面这个公式：
$$
b[k] = -1^{isOdd(l)} * (sum[k-1] + sum[l-1]) + -1 ^ {isOdd(k+1) \oplus isOdd(k-l)} *(sum[r]-sum[k])
$$
这个公式是由定义直接得出的，没理解的可以去看看代码。

代码：

```cpp
#include <algorithm>
#include <iostream>
using namespace std;
const int N = 3e5+100;
char s[N];
int n, m, a[N], f[N];
int u, v;
int func(int x)
{
    int p1 = f[x-1] - f[u-1]; p1 = u%2? p1: -p1;
    int p2 = f[v] - f[x]; p2 = (((x-u)%2) ^ ((x+1)%2) )? p2: -p2;
    return p1 + p2;
}

void work(int l, int r)
{
    int sum = f[r]-f[l-1];
    if(sum == 0) { printf("0\n"); return; }
    int flag = 0;
    if((r-l+1)%2==0) { r--; flag = r+1; sum = f[r] - f[l-1]; sum = l%2 ? sum: -sum; }
    u = l, v = r;
    printf("b (%d,%d): ",l,r); for(int i = l; i <= r; i++) printf("%d ",func(i)); cout << endl;
    while(l < r)
    {
        int mid = l+r>>1;
        // printf("%d %d %d\n",func(l),func(r),func(mid));
        if( (func(mid) > 0 && func(l) < 0) || (func(mid) < 0 && func(l) > 0) ) r = mid-1;
        else if( (func(mid) > 0 && func(r) < 0) || (func(mid) < 0 && func(r) > 0) ) l = mid+1;
        else if( func(mid) == 0 ) l = r = mid;
        else if( func(l) == 0 ) r = l;
        else if( func(r) == 0 ) l = r;
    } 
    if(flag) printf("2\n%d %d\n",l,flag);
    else printf("1\n%d\n",l);
}

void solve()
{
    scanf("%d%d",&n,&m);
    scanf("%s",s);
    for(int i = 0; i < n; i++) if(s[i] == '+') a[i+1] = 1; else a[i+1] = -1;
    for(int i = 1; i <= n; i++) f[i] = f[i-1] + ( (i%2)?a[i] : -a[i] );
    
    while(m--)
    {
        int l, r; scanf("%d %d",&l,&r);
        work(l,r);
    }
}

int main()
{
    int T; cin >> T;
    while(T--) solve();
    system("pause");
}
```

## E. Rescue Niwen!

不难看出题意中将原字符串展开的字符串学列是将原字符串先展开后缀，再将每个后缀展开前缀。

于是很直观地想到，在原字符串上跑后缀数组后，答案序列必然在得到的rank数组上的上升子序列。

同时注意到加入后缀$i$以后，答案长度并不是加上$n-i+1$，而是$n-i+1-lcp(i,j)$，其中lcp是最长共同前缀，j表示该序列的上一个后缀。

所以我们还需要快速求lcp，幸运的是我们可以通过后缀数组求得$lcp(i-1,i),i\in [2,n]$，然后结合ST表做到$O(1)$查询。

注意到暴力枚举是$O(2^n)$的复杂度, 所有上升子序列是不可接受的()。于是考虑用dp。

记$f[i]$表示以后缀i结尾的最优答案，状态转移方程为$f[i] = \max(\max_{j=1}^{i-1}(f[j]+n-i+1-lcp(i,j)) , n-i+1) $。

dp的复杂度是$O(n^2)$的，可以接受。

后缀数组和lcp都可以套板子，所以看起来代码很长，但其实还可以。

```cpp
#include <iostream>
#include <algorithm>
#include <cstring>
using namespace std;
const int N = 2e4+100;
struct Suffix_Array{
    int n, m, sa[N], rk[N<<1], oldrk[N<<1];
    int id[N], cnt[N], rkid[N], height[N];

    bool cmp(int x, int y, int w)
    {
        return oldrk[x] == oldrk[y] && oldrk[x+w] == oldrk[y+w];
    }

    void init()
    {
        for(int i = 0; i <= m+1; i++) height[i] = id[i] = cnt[i] = rkid[i] = sa[i] = 0;
        for(int i = 0; i <= 2*m+2; i++) rk[i] = oldrk[i] = 0; 
    }

    void solve(char* s) //Note:真正的字符串是从s+1开始的
    {
        n = strlen(s+1);
        m = max(n, 256); //值域大小
        init();
        //初始化 sa和rk数组
        for(int i = 1; i <= n; i++) ++cnt[rk[i] = s[i]];
        for(int i = 1; i <= m; i++) cnt[i] += cnt[i-1];
        for(int i = n; i > 0; i--) sa[cnt[rk[i]]--] = i;

        //以下是倍增
        //w表示已推出的子串长度
        for(int w = 1, p; w < n; w <<= 1)
        {
            p = 0;
            for(int i = n; i > n-w; i--) id[++p] = i;
            for(int i = 1; i <= n; ++i)
                if(sa[i] > w) id[++p] = sa[i] - w;
            //以上是后半截rk[i+w作为第二关键字排序
            memset(cnt, 0, sizeof(int)*(m+1));
            for(int i = 1; i <= n; i++) ++cnt[rkid[i] = rk[id[i]]];
            for(int i = 1; i <= m; i++) cnt[i] += cnt[i-1];
            for(int i = n; i >= 1; --i) sa[cnt[rkid[i]]--] = id[i];
            //按照前半截rk[i]作为第一关键字排序
            swap(rk, oldrk);
            m = 0;
            for(int i = 1; i <= n; i++)
                rk[sa[i]] = (m += (cmp(sa[i], sa[i-1], w) ^ 1));
        }
        return;
    }
    void GetHeight(char *s) {
    for (int i = 1, k = 0; i <= n; ++ i) {
        if (rk[i] == 1) k = 0;
        else {
        if (k > 0) k --;
        int j = sa[rk[i] - 1];
        while (i + k <= n && j + k <= n && 
                s[i + k] == s[j + k]) {
            ++ k;
        }
        }
        height[rk[i]] = k;
    }
    }
}w;
char s[N];
struct LCP{
    static const int logn = 21;
    static const int maxn = 2000001;
    int f[maxn][logn + 1], Logn[maxn + 1];
    int *height;
    int *rk;
    int n;

    void init(Suffix_Array& o){
        height = o.height;
        rk = o.rk;
        n = o.n;
    }

    void pre_lcp()
    {
        Logn[1] = 0;
        Logn[2] = 1;
        for (int i = 3; i < maxn; i++) {
            Logn[i] = Logn[i / 2] + 1;
        }
        // memset(f, 0x3f, sizeof f); //多次循环这里时间度爆炸 要注意
        for (int j = 1; j <= logn; j++)
        for (int i = 1; i + (1 << j) - 1 <= n; i++)
        f[i][j] = 0x3f3f3f3f;

        for(int i = 1; i <= n; i++) f[i][0] = height[i];

        for (int j = 1; j <= logn; j++)
        for (int i = 1; i + (1 << j) - 1 <= n; i++)
        f[i][j] = min(f[i][j - 1], f[i + (1 << (j - 1))][j - 1]);

    }

    int lcp(int l, int r){ //l和r是指以第l个字符开始的后缀和第r个字符开始的后缀
        if(l == r) return n-l+1;
        
        l = rk[l];
        r = rk[r];
        if(l>r) swap(l, r);
        l++;
        int x = Logn[r - l + 1];
        return min(f[l][x], f[r - (1 << x) + 1][x]);
    }
}t;
int f[N], top = 0;
void solve()
{
    int n; scanf("%d %s",&n,s+1);
    w.solve(s);
    w.GetHeight(s);
    t.init(w);
    t.pre_lcp();
    int *a = w.rk;
    int ans = 0;
    for(int i = 1; i <= n; i++)
    {
        int tmp = 0;
        for(int j = 1; j < i; j++)
        {
            if(a[i] > a[j]) tmp = max(tmp, f[j] + n-i+1 - t.lcp(i,j));
        }
        f[i] = max(tmp, n-i+1);
        ans =  max(f[i], ans);
    }
    printf("%d\n",ans);
}
int main()
{
    int T; cin >> T;
    while(T--)
    {
        solve();
    }
    // system("pause");
}
```

