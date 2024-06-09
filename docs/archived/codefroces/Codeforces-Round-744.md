# [Codeforces Round #744 (Div. 3)](https://codeforces.com/contest/1579)

## A. Casimir's String Solitaire

分析：不难发现，当且仅当B的个数等于A和C的个数的和时，可行。

代码：

```cpp
#include <algorithm>
#include <iostream>
using namespace std;
const int N  = 500;
char s[N];

void solve()
{
    scanf("%s",s);
    int a, b; a = b = 0;
    for(int i = 0; s[i]; i++)
    {
        if(s[i] == 'B') b++;
        else a++;
    }
    if(b == a) printf("YES\n");
    else printf("NO\n");
}

int main()
{
    int t; scanf("%d",&t);
    while(t--) solve();
    // system("pause");
}
```

## B. Shifting Sort

分析: 不难想出, 每次寻找第i大的元素, 然后把这个元素滑动到第i位即可. 考虑到n不大于50, 暴力查找模拟即可.

code:

```cpp
#include <algorithm>
#include <iostream>
using namespace std;
const int N  = 500;
int a[N], b[N];
int l[N], r[N], d[N], cnt;
void solve()
{
    int n; scanf("%d",&n);
    cnt = 0;
    for(int i = 1; i <= n; i++) { scanf("%d",a+i); b[i] = a[i]; }
    sort(b+1, b+1+n);
    for(int i = 1; i <= n; i++)
    {
        int j;
        for(j = i; j <= n; j++)
        {
            if(b[i] == a[j]) break;
        }
        if(i != j) 
        {
            l[cnt] = i;
            r[cnt] = j;
            d[cnt] = j-i;
            for(int k = j; k > i; k--) a[k] = a[k-1];
            a[i] = b[i];
            cnt++;
        }
    }
    printf("%d\n",cnt);
    for(int i = 0; i < cnt; i++)
    {
        printf("%d %d %d\n",l[i],r[i],d[i]);
    }
}

int main()
{
    int t; scanf("%d",&t);
    while(t--) solve();
    // system("pause");
}
```

## C. Ticks

分析: 模拟题, 没啥好说的.

```cpp
#include <algorithm>
#include <iostream>
using namespace std;
const int N  = 32;
char s[N];
int a[N][N], cnt;
int c[N][N];
int n, m, k; 
void check(int x, int y, int k)
{
    if(x <= k) return;
    if(y <= k || m-y < k) return;
    int h = 1;
    while( x>h && y>h && m-y>=h && a[x-h][y-h] && a[x-h][y+h] ) h++;
    h--;
    if(h < k) return;
    for(int i = 0; i <= h; i++)
    {
        if( c[x-i][y-i] == 0) { cnt--; 
        c[x-i][y-i] = 1; }
        if( c[x-i][y+i] == 0) { cnt--; 
        c[x-i][y+i] = 1; }
    }
}

void solve()
{
    scanf("%d %d %d",&n,&m,&k);
    cnt = 0;
    for(int i = 1; i <= n; i++)
    {
        scanf("%s",s);
        for(int j = 1; j <= m; j++)
        {
            if(s[j-1] == '*') { a[i][j] = 1; cnt++; }
            else a[i][j] = 0;
            c[i][j] = 0;
        }
    }
    for(int i = n; i; i--)
        for(int j = 1; j <= m; j++)
            if(a[i][j]) check(i, j, k);
    if(cnt == 0) printf("YES\n");
    else printf("NO\n");
}

int main()
{
    int t; scanf("%d",&t);
    while(t--) solve();
    // system("pause");
}
```

## D. Productive Meeting

分析:

观察下列这组数据:

5

8 2 0 1 1

我们发现, 当不是最大元素去匹配时, 可能出现非最优的情况. 

于是我们考虑每次匹配最大元素和次大元素, 用优先队列维护即可.

为了保证正确性, 每次匹配只可以匹配一次.

code:

```cpp
#include <algorithm>
#include <iostream>
#include <queue>
using namespace std;
const int N = 1<<18;
int a[N];
struct node{
    int w, idx;
    bool operator <(const node& o) const{
        return w < o.w;
    }
};
priority_queue<node> q;
int x[N], y[N], cnt;
void solve()
{
    int n; scanf("%d",&n);
    cnt = 0;
    for(int i = 1; i <= n; i++) 
    {
        scanf("%d",&a[i]);
        if(a[i]) q.push({a[i], i});
    }
    while(q.size() > 1)
    {
        node u = q.top(); q.pop();
        node v = q.top(); q.pop();
        x[cnt] = u.idx;
        y[cnt] = v.idx;
        cnt++;
        u.w--; v.w--;
        if(v.w) q.push(v);
        if(u.w) q.push(u);
    }
    while(q.size()) q.pop();
    printf("%d\n",cnt);
    for(int i = 0; i < cnt; i++) printf("%d %d\n",x[i],y[i]);
}

int main()
{
    int t; scanf("%d",&t);
    while(t--) solve();
    // system("pause");
}
```

## E1. Permutation Minimization by Deque

分析:

直接用deque模拟即可.

附上一张deque的基本操作.
![在这里插入图片描述](https://img-blog.csdnimg.cn/146d2be2b1414303a110e875e57b3bf5.jpg?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQXJjdGljX0NsYW0=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

code:
```cpp
#include <algorithm>
#include <iostream>
#include <queue>
using namespace std;
const int N  = 32;
deque<int> q;
void solve()
{
    int n; scanf("%d",&n);
    int tmp; scanf("%d",&tmp); q.push_back(tmp);
    for(int i = 2; i <= n; i++)
    {
        scanf("%d",&tmp);
        if(tmp < q.front()) q.push_front(tmp);
        else q.push_back(tmp);
    }
    for(int i = 1; i <= n; i++)
    {
        printf("%d ",q.front()); q.pop_front();
    }
    printf("\n");
}

int main()
{
    int t; scanf("%d",&t);
    while(t--) solve();
    // system("pause");
}
```

## E2. Array Optimization by Deque

分析:
我们发现, 把ai放在前头时, 所增加的逆序对是之前所有元素中比ai小的. 把ai放在后头时,所增加的逆序对是之前所有元素比ai大的个数.

贪心地操作即可. 贪心的正确性基于, 无论ai放在前面还是后面, 对之后的逆序对的贡献都是一样.

用树状数组维护即可.

```cpp
#include <algorithm>
#include <iostream>
#include <queue>
using namespace std;
const int N = 1<<18;
int a[N];
int n; 
struct node{
    int w, idx;
    bool operator <(const node& o) const{
        return w < o.w;
    }
}b[N];

int c[N];

void add(int x, int val)
{
    for(int i = x; i <= n; i += i&-i)
    c[i] += val;
}

int query(int x)
{
    int rev = 0;
    for(int i = x; i > 0; i -= i&-i)
    rev += c[i];
    return rev;
}

void solve()
{
    scanf("%d",&n);
    for(int i = 1; i <= n; i++)
    {
        scanf("%d",&a[i]);
        b[i] = {a[i], i};
    }
    sort(b+1, b+n+1);
    int cnt = 1; a[b[1].idx] = 1;
    for(int i = 2; i <= n; i++)
    {
        if(b[i].w > b[i-1].w) cnt++;
        a[b[i].idx] = cnt;
    }
    long long ans = 0;
    for(int i = 1; i <= n; i++)
    {
        int front = a[i]==1? 0 : query(a[i]-1);
        int end = query(n) - query(a[i]);
        ans += min(front, end);
        add(a[i],1);
    }
    printf("%lld\n",ans);
    for(int i = 1; i <= n; i++)
    {
        add(a[i], -1);
    }
}

int main()
{
    int t; scanf("%d",&t);
    while(t--) solve();
    // system("pause");
}
```

## F. Array Stabilization (AND version)

分析:

考虑每个元素ai会被依次右边的元素$a_{i-d*j}, j\in\{1,2,3,...\}$更新.

我们记$f[i]$表示ai右数第$f[i]*d$个元素是0, 当ai本身就为0时, f[i]显然为0.

状态转移方程为
$$
if(a[i] == 1)\space f[i] = f[i-d]+1\\
if(a[i]==0)\space f[i] = 0
$$
dp一遍即可.

因为是个环,记得取余.

当一个位置还未求出来的时候被再次访问, 则表示无解.

code:

```cpp
#include <algorithm>
#include <iostream>
#include <queue>
using namespace std;
const int N = 1<<21;
int a[N];
int n, d; 
int dp[N];
bool vis[N];
int ans = 0;
int dfs(int x)
{
    if(vis[x]) return dp[x] = -0x3f3f3f3f;
    if(dp[x] != -1) return dp[x];
    if(a[x] == 0) return 0;
    vis[x] = 1;
    int nxt = (x + n - d)%n;
    dp[x] = dfs(nxt)+1;
    vis[x] = 0;
    return dp[x];
}   

void solve()
{
    scanf("%d %d",&n,&d);
    for(int i = 0; i < n; i++) 
    {
        scanf("%d",a+i);
        dp[i] = -1;
    }
    int ans = 0;
    for(int i = 0; i < n; i++)
    {
        if(dfs(i) < 0) { printf("-1\n"); return; }
        else ans = max(ans, dfs(i));
    }
    printf("%d\n",ans);
}

int main()
{
    int t; scanf("%d",&t);
    while(t--) solve();
    // system("pause");
}
```

## G. Minimal Coverage

分析:

考虑二分答案+dp验证.

记最大的元素为maxx

不难发现, 答案在于[maxx, maxx*2]这个区间中.

然后考虑如何检验给定答案是否合法.

记f[i]表示距离最大位置距离为i的方案是否存在, 也就是说取值仅有$\{ture, false\}$两种可能性.

这里的最大位置的意思是,某个合法的方案中的右边界.

code:

```cpp
#include <algorithm>
#include <iostream>
#include <string.h>
using namespace std;
const int N = 2048;
int a[1<<16];
int n; 
bool s[N], t[N];
bool check(int x)
{
    for(int i = 0; i <= x; i++) s[i] = 1; //显然初始的时候距离小于等于x的都合法.
    for(int i = 1; i <= n; i++)
    {
        memset(t, 0, sizeof t);
        int tmp = a[i];
        for(int j = tmp; j <= x; j++) t[j-tmp] = s[j]; //第j个线条往左放
        for(int j = x-tmp; j>=0; j--) t[j+tmp] |= s[j];//往右放
        swap(s, t);
    }
    int flag = 0;
    for(int i = 0; i <= x; i++) flag |= s[i]; //存在一种可能性即合法
    return flag;
}

void solve()
{
    scanf("%d",&n);
    int maxx = 0;
    for(int i = 1; i <= n; i++) 
    {
        scanf("%d",a+i);
        maxx = max(maxx, a[i]);
    }

    int l = maxx, r = maxx*2;
    while( l < r )
    {
        int mid = l+r>>1;
        if(check(mid)) r = mid;
        else l = mid+1;
    }
    cout << l << endl;
}


int main()
{
    int T; scanf("%d",&T);
    while(T--) solve();
    // system("pause");
}
```

