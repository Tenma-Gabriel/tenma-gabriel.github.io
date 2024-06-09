# A. Mean Inequality
分析：很简单的构造。
代码：

```cpp
#include <iostream>
#include <algorithm>
#include <vector>
using namespace std;
const int N = 100;
int a[N], b[N];
int main()
{
    int t; cin >> t;
    while(t--)
    {
        int n; cin >> n;
        for(int i = 1; i <= n<<1; i++) cin >> a[i];
        sort(a+1,a+1+2*n);
        for(int i = 1; i <= 2*n; i++)
        {
            if(i&1) b[i] = a[(i+1)>>1];
            else b[i] = a[n+(i>>1)];
        }
        for(int i = 1; i <= 2*n; i++) cout << b[i] << " ";
        cout << endl;
    }
//     system("pause");
}
```
# B. I Hate 1111
分析：
显然大于111的形如$[1^*]$的数字都可以被$11a+111b$来表示, 其中a和b是常数. 所以我们只要分析给定的数字n可不可以被11和111分解即可. 
考虑$111\%11=1$, 不难发现任何可以被11和111分解的数字, 可以被分解成$11a+111b$, 其中$b\le10$. 那么枚举b即可.
代码:

```cpp
#include <iostream>
#include <algorithm>
#include <vector>
using namespace std;
typedef long long INT;

int main()
{
    int t; cin >> t;
    while(t--)
    {
        INT n; cin >> n;
        bool f = 0;
        int x = n%11;
        if((n -= x*111) >= 0) 
            if(n%11 == 0) f = 1;
        // printf("= %d*111 + %d*11\n",x,n/11);
        if(f) cout << "YES\n";
        else cout << "NO\n";
    }
    // system("pause");
}
```
# C1. Potions (Easy Version)
分析:
因为是简单版本,所以下面给出$O(n^2)$的做法.
参考最长上升子序列的DP方式, $dp[i]$表示长度为$i$的合法子序列中最大的sum.
状态转移方程: $dp[i] = dp[i-1] + a[i]$
ps: 记得开long long
代码:

```cpp
#include <iostream>
#include <algorithm>
#include <vector>
using namespace std;
typedef long long INT;
const int N = 3000;

INT a[N], f[N];

int main()
{
    int n; cin >> n;
    for(int i = 0; i < n; i++) cin >> a[i];
    int start = 0;
    while(start < n && a[start]<0) start++;
    if(start == n) 
    {
        cout << 0 << endl;
        // system("pause");
        return 0;
    }
    f[1] = a[start];
    int maxh = 1;
    for(int i = start+1; i < n; i++)
    {
        for(int j = maxh; j >= 0; j--)
        {
            if(f[j] + a[i] >= 0)
            {
                f[j+1] = max(f[j+1], f[j]+a[i]);
                if(j == maxh)
                {
                    maxh++;
                }
            }
        }
    }
    cout << maxh << endl;
    // system("pause");
}
```
# C2. Potions (Hard Version)
分析:
下面介绍$O(n \log n)$的做法.
算法 tag: 反悔贪心.
我们维护一个反悔堆, 这将是一个小根堆(优先队列).
每当我们把一个负数加入我们的子序列时, 我们就把负数压入堆中.
原因是, 我们将负数加入子序列(当前最优解)的时候是最优, 但是在未来我们可能可以遇到更好的负数(本质上就是数值比当前这个负数大), 因此我们需要提供一个反悔机制.
当我们的子序列无法加入当前的负数的时候, 一般来说是因为子序列的sum+当前的负数<0. 如果此时堆顶元素小于当前负数, 那么选择当前负数而不选择堆顶负数是一定更优的, 那么就弹出堆顶元素, 压入当前负数.
具体操作看代码:

```cpp
#include <iostream>
#include <algorithm>
#include <queue>
using namespace std;
priority_queue<long long, vector<long long>, greater<long long>> h;
long long sum;
int cnt;
int main()
{
    int n; scanf("%d",&n);
    for(int i = 0; i < n; i++) 
    {
        long long tmp; scanf("%lld",&tmp);
        if(tmp >= 0) sum += tmp, cnt++;
        else if(tmp + sum >= 0) 
        {
            sum += tmp;
            cnt++;
            h.push(tmp);
        }
        else if(h.size() && h.top() < tmp)
        {
            sum = sum - h.top() + tmp;
            h.pop();
            h.push(tmp);
        }
    }
    printf("%d\n",cnt);
    // system("pause");
}
```
