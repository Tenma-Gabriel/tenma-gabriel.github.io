# A. Contest Start
分析：
首先不难想到倒数第$\lfloor t/x\rfloor$个人开始, 每往后数一个人, 不满值会减一, 故可以得出:
$answer = \frac{n*(n-1)}{2} \space \space ,n\le\lfloor t/x\rfloor$
考虑从倒数第$\lfloor t/x\rfloor$个人往前数, 每个人都能积攒相同的怒值, 这个值为$\lfloor t/x\rfloor$.
记k = $\lfloor t/x\rfloor$. 于是可以得到:
$answer = (n-k)*k+\frac{k*(k-1)}{2}\space \space, n>\lfloor t/x\rfloor$
代码:
```cpp
#include <iostream>
#include <algorithm>
using namespace std;

int main()
{
    int K; cin >> K;
    while(K--)
    {
        long long n, x, t; cin >> n >> x >> t;
        long long ans = 0;
        if(n > t/x) 
        {
            ans = (n-t/x) *(t/x);
            t /= x;
            ans += t*(t-1)/2;    
        }    
        else ans = n*(n-1)/2;
        cout << ans << endl;
    }
    // system("pause");
}
```
# B. Love Song
分析:
前缀和做到O(n)预处理, O(1)查询即可
代码:
```cpp
#include <iostream>
using namespace std;
const int N = 200001;
char s[N];
long long a[N];
int main()
{
    int n, q; cin >> n >> q;
    scanf("%s",s);
    for(int i = 0; i < n; i++)
    {
        a[i+1] = a[i] + (s[i]-'a'+1);
    }
    for(int i = 0; i < q; i++)
    {
        int l, r; scanf("%d %d",&l,&r);
        printf("%lld\n",a[r]-a[l-1]);
    }
    // system("pause");
}
```
# C. Stable Groups
分析:
贪心.
每次选取最小代价可以合并的两段来合并即可.  正确性是显然的.
可以使用堆来维护.
```cpp
#include <algorithm>
#include <iostream>
#include <queue>
using namespace std;
typedef long long LL;
LL a[300100];
priority_queue<LL, vector<LL>, greater<LL>> q;
int main()
{
    LL n, k, x; cin >> n >> k >> x;
    for(int i = 0; i < n; i++) scanf("%lld",&a[i]);
    sort(a, a+n);
    for(int i = 0; i < n-1; i++)
    {
        if(a[i+1]-a[i] > x) 
        {
            LL tmp = a[i+1]-a[i];
            q.push((tmp)/x + (tmp%x!=0) -1);
        }
    }
    // cout << q.size() << endl;
    while(q.size() && k >= q.top())
    {
        k -= q.top();
        q.pop();
    }
    cout << q.size()+1 << endl;
    // system("pause");
}
```
# D. PriceFixed
分析: 
还是贪心.
以${b_i}$为第一关键字非降序排序.
排序完, 显然高位的商品可以打折 那么低位的也可以打折。
也就是高位打折的概率低些，低位打折的概率高些。
那么当不能打折的时候我们就去买高位的，可以打折的时候从低位买起。
这个贪心策略所得出的结果是所有商品都恰好$a_i$的最好价格, 如果最优策略不是所有商品都恰好买的情况, 这个策略是不一定最优的.
但不用担心, 因为一定存在一种最优策略是刚好买$a_i$个的.
原因很简单, 假设存在一个最优解不是刚好买$a_i$个, 考虑到打折1块, 原价2块, 如果不是刚好的话,  额外买的商品应该是拿来凑数打折的, 否则不可能价格最优. 那么我们可以直接如此构造: 所有被额外商品凑数打折的商品原价买, 凑数商品不买, 可得到最优解.
代码:
```cpp
#include <algorithm>
#include <queue>
#include <iostream>
using namespace std;
typedef long long LL;
struct node{
    LL a, b;
    bool operator < (const node& o) const{
        return b < o.b;
    }
}p[200100];
LL c[200100];
int main()
{
    int n; cin >> n;
    for(int i = 1; i <= n; i++) scanf("%lld %lld",&p[i].a,&p[i].b);
    sort(p+1, p+1+n);
    int r = n, l = 1;
    LL sum = 0, cost = 0;
    while(l <= r)
    {
        // for(int i = 1; i <= n; i++) cout << c[i] << " "; cout << cost << ":" << sum << endl;
        if(sum >= p[l].b)
        {
            // printf("%lld %lld %lld\n",sum, p[l].a, c[l]);
            cost += p[l].a - c[l];
            sum += p[l].a - c[l];
            c[l] = p[l].a;
            l++;
            continue;
        }
        else
        {
            LL sub = min(p[r].a-c[r], p[l].b-sum);
            if(sub == 0) { r--; continue; }
            c[r] += sub;
            cost += 2*sub;
            sum += sub;
            if(p[r].a - c[r] == 0) r--; 
            continue;
        }
    }
    // for(int i = 1; i <= n; i++) cout << c[i] << " "; cout << endl;
    cout << cost << endl;
    // system("pause");
}
```