# A. Stone Game
分析：
考虑从两边删，还是从一遍删，选比较小的那个
```cpp
#include <iostream>
#include <algorithm>
using namespace std;
int a[200];
int main(){
    int t; cin >> t;
    a[101] = 101;
    while(t--)
    {
        int n; cin >> n;
        int minx = 101, maxx = 0;
        for(int i = 1; i <= n; i++) 
        {
            cin >> a[i];
            if(a[minx] > a[i]) minx = i;
            if(a[maxx] < a[i]) maxx = i;
        }

        if(minx > maxx) swap(minx, maxx);
        int ans = min(maxx, n-minx+1);
        ans = min(minx + n-maxx+1, ans);
        cout << ans  << endl;
    }
    // system("pause");
}
```
# B. Friends and Candies
分析：
把大于均值的人挑出来即可
```cpp
#include <iostream>
#include <algorithm>
using namespace std;
const int N = 200100;
typedef long long LL;
int a[N];

int main()
{
    int t; cin >> t;
    while(t--)
    {
        int n; cin >> n;
        LL sum = 0;
        for(int i = 1; i <= n; i++)
        {
            cin >> a[i];
            sum += a[i];
        }

        if(sum%n != 0) 
        {
            cout << "-1" << endl;
            continue;
        }

        int avg = sum/n;
        int cnt = 0;
        for(int i = 1; i <= n; i++)
        {
            if(a[i] > avg) cnt++;
        }
        cout << cnt << endl;
    }
    // system("pause");
}
```
# C. Number of Pairs
分析:
先排序,排序后枚举有序对的第一个数字, 然后二分出第二个数字的合法区间.
复杂度$O(n\log n)$
```cpp
#include <iostream>
#include <algorithm>
#include <vector>
#include <cmath>
using namespace std;
const int N= 200100;
int a[N];
int n, l, r; 

int work(int x)
{
    int tl = l-a[x], tr = r-a[x];
    auto p = lower_bound(a+x+1, a+1+n, tl);
    int ll = p-a;
    p = upper_bound(a+x+1, a+n+1, tr);
    int rr = p-a-1;
    int rev = 0;
    if(ll < rr) 
    {
        if(rr == n+1) rr = n;
        rev = rr-ll+1;
    }
    else if(ll == rr && ll <= n) rev = 1;
    return rev>=0?rev:0;
}

int main()
{
    int t; cin >> t;
    while(t--)
    {
        cin >> n >> l >> r;
        for(int i = 1; i <= n; i++) scanf("%d",&a[i]);
        sort(a+1,a+1+n);
        long long ans = 0;
        for(int i = 1; i < n; i++)
        {
            ans += work(i);
        }
        cout << ans << endl;
    }
    // system("pause");
}
```
# D. Another Problem About Dividing Numbers
分析:
首先用欧拉筛筛出sqrt(1e9)以下的质数, 用来做质因数分解.
然后对a, b质因数分解.
处理一下细节即可.
```cpp
#include <iostream>
#include <algorithm>
#include <vector>
#include <cmath>
using namespace std;
const int MAXN = 100010;
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
}

vector<int> func(int a)
{
    vector<int> rev;
    int lim = ceil(sqrt(a));
    for(int i = 0; primes[i] <= lim; i++)
    {
        while(a%primes[i] == 0) 
        {
            a/=primes[i];
            rev.push_back(primes[i]);
        }
    }
    if(a != 1) rev.push_back(a);
    return rev;
}

int main()
{
    int t; cin >> t;
    init(100000);
    while(t--)
    {
        int a, b, k; scanf("%d %d %d",&a,&b,&k);
        vector<int> fa = func(a);
        vector<int> fb = func(b);
        int s1 = fa.size(), s2 = fb.size();
        int cnt = 0;
        for(int i = 0, j = 0; i < s1 && j < s2; )
        {
            if(fa[i] == fb[j]) { cnt++, i++, j++; continue; }
            if(fa[i] < fb[j]) i++;
            if(fa[i] > fb[j]) j++;
        }
        bool f = 0;
        int l = (s1>cnt) + (s2>cnt);
        int r = s1+s2;
        if(l <= k && k <= r) f=1;
        if(l == 0 && k==1) f=0;
        if(f) printf("YES\n");
        else printf("NO\n");
        
    }
    // system("pause");
}
```
# E. Funny Substrings
分析: 
一开始以为是暴力模拟, 一看n最大50, 那显然就不行了.
因为这样子最坏情况下, 答案字符串的长度会达到$5*2^{49}$.
目前考虑是维护一个变量的有效前缀和后缀, 这样不会超过4个, 在维护一个内部的haha的个数.
晚点再来写.
# F. Interesting Function
如果这算数位DP, 那么算是我见过最简单的数位DP了.
分析:
记$dp[i]$表示从0到$10^{i-1}$的数位变化次数.
然后暴力合并即可.
```cpp
#include <iostream>
#include <algorithm>
#include <vector>
#include <cmath>
using namespace std;
typedef long long LL;
LL dp[20];

void init(int n)
{
    dp[1] = 1;
    for(int i = 2; i <= n; i++) dp[i] = dp[i-1]*10+1;
}

LL work(LL x)
{
    int a[20] = {0}, cnt = 0;
    while(x)
    {
        a[cnt++] = x%10;
        x/=10;
    }
    LL rev = 0;
    for(int i = cnt-1; i >= 0; i--)
        rev += dp[i+1]*a[i];
    return rev;
}

int main()
{
    int t; cin >> t;
    init(14);
    while(t--)
    {
        LL a, b; cin >> a>>b;
        cout << work(b) - work(a) << endl;
    }
    // system("pause");
}
```
# G. Gift Set
分析：
考虑二分可分的集合的数量。
*如何O(1)求给定的某个集合可行与否？*
不妨设$x<y, a<b$
记n为要验证的集合数量，s为第一种集合数量，t为第二种集合数量。
于是我们有
$$ \left\{
\begin{aligned}
as +bt \le x \\
bs + at \le y \\
s + t = n
\end{aligned}
\right.
$$
变形可以得到
$$ \left\{
\begin{aligned}
s \ge \frac{x-nb}{a-b} \\
s \le \frac{y-na}{b-a} 
\end{aligned}
\right.
$$
也就是说，给定n，我们可以求出s的取值区间。再判定下该区间是否合法即可。
note: s取值区间的左界应该是向下取整，但是$x-nb$和$a-b$都是负数，而c++默认是趋零取整，所以要特判。
```cpp
#include <algorithm>
#include <iostream>
#include <cmath>
using namespace std;
typedef long long LL;
LL x, y, a, b;
bool check(LL n)
{
    LL l = ((x-n*b)/(a-b));
    if((x-n*b)<0 && (-(x-n*b))%(b-a)!=0) l++;
    LL r = (y-n*a)/(b-a);
    // printf("%lld [%lld, %lld]\n",n,l,r);
    return l<=r && r>=0 && l<=n;
}
int main()
{
    int t; cin >> t;
    while(t--)
    {
        cin >> x >> y >> a >> b;
        if(x>y) swap(x, y);
        if(a>b) swap(a, b);
        if(a == b)
        {
            cout << (x/a) << endl;
            continue;
        }
        LL l=0 , r=x;
        while(l<r)
        {
            if(l == r-1) 
            {
                if(check(r)) l = r;
                break;
            }
            LL mid = l+r>>1;
            if(check(mid)) l=mid;
            else r=mid-1;
        }
        cout << l << endl;
    }
    // system("pause");
}
```
## 三分做法正确性
二分的正确性是显然的，但是三分的正确性并不那么显然。
为了证明三分的正确性，我们需要证明集合总数n是第一集合数s的函数，且该函数至多有一个峰。
下面给出证明：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210611180003968.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FyY3RpY19DbGFt,size_16,color_FFFFFF,t_70#pic_center)

小总结:
C和D分别wa了两发, 很亏, C是因为把一个upper_bound写成了lower_bound, D是没考虑清楚细节.
就很亏.
昨晚写完F的时候还有20多分钟, 然而电脑没电, 下次应该早点开省点模式.
说起来之前补的5.5的那场div.3也是这几个人出的题, 是真的喜欢二分...