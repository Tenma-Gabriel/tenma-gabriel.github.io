补题， 顺便写个题解。
# A. Do Not Be Distracted!
分析：
略。

```cpp
#include <iostream>
#include <algorithm>
#include <string>
using namespace std;
string s;
int main()
{
    int t; cin >> t;
    while(t--)
    {
        int n; cin >> n >> s;
        bool vis[26];
        for(int i = 0; i < 26; i++) vis[i] = 0;
        vis[s[0]-'A'] = 1;
        bool f = 1;
        for(int i = 1; i < n; i++)
        {
            if(s[i] != s[i-1]) 
            {
                if(vis[s[i]-'A'] == 1) 
                {
                    f = 0;
                    break;
                }
                vis[s[i]-'A']  = 1;
            }
        }
        if(f) cout << "YES\n";
        else cout << "NO\n";
    }
    // system("pause");
}
```
# B. Ordinary Numbers
分析：
略。
```cpp
#include <iostream>
#include <algorithm>
using namespace std;
int main()
{
    int t; cin >> t;
    while(t--)
    {
        int n; cin >> n;
        int ans = 0;
        int x = n;
        int i = 0;
        int a[20];
        while(n)
        {
            a[i] = n%10;
            n/=10;
            i++;
        }

        ans += (i-1)*9 + a[i-1] - 1;
        int y = a[i-1];
        for(int j = 1; j < i; j++) { y*=10; y+=a[i-1]; }

        if(x>=y) ans++;

        cout << ans << endl;
    }
    // system("pause");
}
```

# C. Not Adjacent Matrix
分析：
构造。
特判n=2时不存在答案。
构造方法：矩阵上半部分放奇数，下半部分放偶数。
正确性：多打几个表越看越对（逃

```cpp
#include <iostream>
#include <algorithm>
using namespace std;
int a[12000];
void print(int n)
{
    for(int i = 0; i < n; i++)
    {
        for(int j = 0; j < n; j++)
        {
            cout << a[i*n + j] << " ";
        }
        cout << endl;
    }
}

int main()
{
    int t; cin >> t;
    while(t--)
    {
        int n; cin >> n;
        if(n == 2) { cout << -1 << endl; continue; }
        int cnt = 0;
        for(int i = 1; i <= n*n; i+=2) a[cnt++] = i;
        for(int i = 2; i <= n*n; i+=2) a[cnt++] = i;
        print(n);
    }
    // system("pause");
}
```
# D. Same Differences
分析：
我们有$a_j−a_i=j−i$
对表达式变形后我们得出  $a_j-j=a_i-i$
用桶记录下元素和下标的差值即可.

```cpp
#include <iostream>
#include <algorithm>
using namespace std;
const int N = 4e5+100;
int b[N];
int main()
{
    int t; cin >> t;
    while(t--)
    {
        int n; cin >> n;
        long long ans = 0;
        for(int i = 0; i <= n*2; i++) b[i] = 0;
        for(int i = 0; i < n; i++) 
        {
            int tmp; cin >> tmp;
            ans += b[tmp-i+n];
            b[tmp-i+n]++;
        }
        cout << ans << endl;
    }
    // system("pause");
}
```

# E. Arranging The Sheep
分析:
左DP一次, 右DP一次, 合并答案.
和[这题](https://www.luogu.com.cn/problem/P1091)挺像的, 感兴趣的可以把那题也做了.

```cpp
#include <iostream>
#include <algorithm>
#include <string>
using namespace std;
const int N = 1e6+10;
typedef long long LL;
LL fl[N], fr[N];
string s;

int main()
{
    int t; cin >> t;
    while(t--)
    {
        int n; cin >> n >> s;
        int sum = 0;
        if(s[0] == '*') sum = 1;
        fl[0] = 0;
        for(int i = 1; i < n; i++) 
            if(s[i] == '*')
            {
                sum++;
                fl[i] = fl[i-1];
            }
            else fl[i] = fl[i-1] + sum;

        if(s[n-1] == '*') sum = 1;
        else sum = 0;
        fr[n-1] = 0;
        for(int i = n-2; i >= 0; i--)
            if(s[i] == '*')
            {
                sum++;
                fr[i] = fr[i+1];
            }
            else fr[i] = fr[i+1] + sum;

        LL ans = min(fl[n-1], fr[0]);
        for(int i = 1; i < n-1; i++)
        {
           LL x;
           if(s[i] == '*') 
           {
               x = fl[i-1] + fr[i+1];
           }
           else
           {
               x = min(fl[i]+fr[i+1], fr[i]+fl[i-1]);
           }
           if(x < ans) 
           {
               ans = x;
           }
        }

        cout << ans << endl;
    }
    // system("pause");
}
```
# F1. Guess the K-th Zero (Easy version)
分析:
已知$n<2^{18}$, 最多可以问20次.
直接二分啊.

```cpp
#include <iostream>
#include <algorithm>
#include <string>
using namespace std;
int query(int l, int r)
{
    printf("? %d %d\n",l,r);
    fflush(stdout);
    int rev; cin >> rev;
    return rev;
}
int main()
{
    int n, t; cin >> n >> t;
    int k ; cin >> k;
    int l = 1, r = n;

    while(l < r)
    {
        int mid = l+r>>1;
        int x = query(1, mid);
        x = mid - x;
        if(k > x) l = mid+1;
        else if(k < x) r = mid-1;
        else if(k == x) r = mid;
    }

    printf("! %d\n",l);
    // system("pause");
}
```
# F2. Guess the K-th Zero (Hard version)
分析:
和简单版本一样二分.
但是相同查询只查一遍, 查完后记忆化.
*如何保证不过给定查询次数?*
我们考虑$n<2e5<2^{18}, \space t<6e4$, 可以把二分看作一棵深度为18的二叉决策树.
我们注意到只要把二分决策树的上14层,全部记忆化, 需要$2^{14}=16384$次查询.
除此之外, 每个要求的k, 只需要再往下4层即可确定, k最大为10, 也就是说还需要$10000 * 4 = 40000$次查询.
总共$40000 + 16384 = 56384 <60000$, 这样一来即可保证约束.

*每次查询过后数组都会变, 之前记忆化的查询好像用不了了, 这可如何是好?*
我们考虑对每次记录下的查询做变换处理, 即处理成最初的数组的对应区间查询的结果.
换句话说, 我们的查询要**减去**目前为止在查询区间插入的1.
通过**树状数组**可以维护目前为止在查询区间插入的1.
每次我们要用这些查询的时候, 就要**加上**目前为止在查询区间插入的1.

```cpp
#include <iostream>
#include <algorithm>
#include <string>
#include <map>
using namespace std;
int n, t; 
int sum[300100];
int c[300100];
void add(int x)
{
    for(int i = x; i < n; i+=i&-i)
        c[i]++;
}

int pre(int x)
{
    int rev = 0;
    for(int i = x; i > 0; i-=i&-i)
        rev += c[i];
    return rev;
}
int query(int l, int r)
{
    printf("? %d %d\n",l,r);
    fflush(stdout);
    int rev; cin >> rev;
    return rev;
}
int main()
{
    cin >> n >> t;
    for(int i = 1; i <= n; i++) sum[i] = -1;
    while(t--)
    {
        int k ; cin >> k;
        int l = 1, r = n;
        while(l < r)
        {
            int mid = l+r>>1;
            int x;
            if(sum[mid]>=0) x= sum[mid] + pre(mid);
            else { x = query(1, mid); sum[mid] = x - pre(mid); }
            x = mid - x;
            if(k > x) l = mid+1;
            else if(k < x) r = mid-1;
            else if(k == x) r = mid;
        }

        add(l);
        printf("! %d\n",l);
    }
    // system("pause");
}
```
# G. To Go Or Not To Go?
分析:
考虑到最优解下,传送门**至多**只会用到**2**个.
显然, 如果用到2个以上的传送门, 我们只需要选取其中第一个使用的传送门和最后一个使用的传送门就可以组成一个更优解.
当然, 最优解也有可能不用传送门.

那么只需要dp出两个数组, 一个代表从起点到该点的最小距离, 一个表示从该店的终点的最小距离.
然后转移出答案即可. (是不是和E题有点像)

note: dp的时候要用bfs序.

```cpp
#include <iostream>
#include <algorithm>
#include <queue>
#include <string.h>
using namespace std;
typedef long long LL;
const LL INF = 0x3f3f3f3f3f3f3f3f;
int n, m;
LL a[2021][2021], w, dp[2021][2021], dp_2[2021][2021];
struct node{
    int x, y;
};
LL h1 = INF, h2 = INF;
queue<node> q;
void work()
{
    q.push({1,1});
    if(a[1][1]>0) h1 = min(h1, a[1][1]);
    while(q.size())
    {
        node u = q.front();
        q.pop();
        
        if(u.x>1 && dp[u.x-1][u.y] == INF && a[u.x-1][u.y] >= 0)
        {
            dp[u.x-1][u.y] = dp[u.x][u.y] + w;
            if(a[u.x-1][u.y]>0) h1 = min(h1, dp[u.x-1][u.y]+a[u.x-1][u.y]);
            q.push({u.x-1, u.y});
        }
        if(u.y>1 && dp[u.x][u.y-1] == INF && a[u.x][u.y-1] >= 0)
        {
            dp[u.x][u.y-1] = dp[u.x][u.y] + w;
            if(a[u.x][u.y-1]>0) h1 = min(h1, dp[u.x][u.y-1]+a[u.x][u.y-1]);
            q.push({u.x, u.y-1});
        }
        if(u.x<n && dp[u.x+1][u.y] == INF && a[u.x+1][u.y] >= 0)
        {
            dp[u.x+1][u.y] = dp[u.x][u.y] + w;
            q.push({u.x+1, u.y});
            if(a[u.x+1][u.y]>0) h1 = min(h1, dp[u.x+1][u.y] + a[u.x+1][u.y]);
        }
        if(u.y<m && dp[u.x][u.y+1] == INF && a[u.x][u.y+1] >= 0)
        {
            dp[u.x][u.y+1] = dp[u.x][u.y] + w;
            q.push({u.x, u.y+1});
            if(a[u.x][u.y+1]) h1 = min(h1, dp[u.x][u.y+1] + a[u.x][u.y+1]);
        }
    }
    
    q.push({n, m});
    if(a[n][m] > 0) h2 = min(h2, a[n][m]);
    while(q.size())
    {
        node u = q.front();
        q.pop();
        if(u.x>1 && dp_2[u.x-1][u.y] == INF && a[u.x-1][u.y] >= 0)
        {
            dp_2[u.x-1][u.y] = dp_2[u.x][u.y] + w;
            q.push({u.x-1, u.y});
            if(a[u.x-1][u.y]>0)  h2 = min(h2, dp_2[u.x-1][u.y]+a[u.x-1][u.y]);
        }
        if(u.y>1 && dp_2[u.x][u.y-1] == INF && a[u.x][u.y-1] >= 0)
        {
            dp_2[u.x][u.y-1] = dp_2[u.x][u.y] + w;
            q.push({u.x, u.y-1});
            if(a[u.x][u.y-1]>0) h2 = min(h2, dp_2[u.x][u.y-1]+a[u.x][u.y-1]);
        }
        if(u.x<n && dp_2[u.x+1][u.y] == INF && a[u.x+1][u.y] >= 0)
        {
            dp_2[u.x+1][u.y] = dp_2[u.x][u.y] + w;
            q.push({u.x+1, u.y});
            if(a[u.x+1][u.y]>0) h2 = min(h2, dp_2[u.x+1][u.y]+a[u.x+1][u.y]);
        }
        if(u.y<m && dp_2[u.x][u.y+1] == INF && a[u.x][u.y+1] >= 0)
        {
            dp_2[u.x][u.y+1] = dp_2[u.x][u.y] + w;
            q.push({u.x, u.y+1});
            if(a[u.x][u.y+1]) h2 = min(h2, dp_2[u.x][u.y+1]+a[u.x][u.y+1]);
        }
    }
}
int main()
{
    cin >> n >> m >> w;
    memset(dp, 0x3f, sizeof dp);
    memset(dp_2, 0x3f, sizeof dp_2);
    dp[1][1] = 0;
    dp_2[n][m] = 0;
    for(int i = 1; i <= n; i++)
    for(int j = 1; j <= m; j++)
        scanf("%lld",&a[i][j]);
    work();
    LL ans = dp[n][m];
    ans = min(ans, h1+h2);
    if(ans != INF) cout << ans << endl;
    else cout << -1 << endl;
    // system("pause");
}
```
总结: 会算复杂度很重要. 经常卡一卡就过去了(不是).