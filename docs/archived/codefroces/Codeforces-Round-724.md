# A. Omkar and Bad Story
分析：
显然有负数存在不可能满足题意，故输出NO。
如果只有非负数的话，那么直接输出以0开头，100结尾的，公差为1的等差数列即可。因为值域是[-100,100]，而数组大小的限制是三百以内。
代码：

```cpp
#include <iostream>
#include <algorithm>
using namespace std;
int a[110];
int main()
{
    int t; cin >> t;
    while(t--)
    {
        int n; cin >> n;
        bool f = 1;
        int minx = 1000, maxx = -1000;
        for(int i = 0; i < n; i++)
        {
            cin >> a[i];
            if(a[i] < 0) f = 0;
            minx = min(a[i], minx);
            maxx = max(a[i], maxx);
        }
        if(f)
        {
            cout << "YES\n";
            cout << maxx+1 << endl;
            for(int i = 0; i <= maxx; i++) cout << i << " "; cout << endl;
        }
        else cout << "NO\n";
    }
    // system("pause");
}
```
# B. Prinzessin der Verurteilung
分析：
考虑长度为1的不同字符串有26个，长度为2的有$26^2$个,长度为3的有$26^3$个.
由于题目中所给字符串长度上限为1000, 故最多包含1000个长度为1的子串, 999个长度为2的子串, 998个长度为3的子串.
显然该字符串的mex最大长度为3.
暴力即可.
代码:

```cpp
#include <algorithm>
#include <iostream>
#include <map>
using namespace std;
string s;
bool ok(string t)
{
    int n = s.size(), m = t.size();
    for(int i = 0, j = 0; i < n; i++)
    {
        for(j = 0; j < m && i + j < n; j++)
        if(s[i+j] != t[j]) break;
        if(j == m) return 0;
    }
    return 1;
}
int main()
{
    int t; cin >> t;
    while(t--)
    {
        int n;
        cin >> n;
        cin >> s;
        string ans = "";
        for(int i = 0; i < 26; i++)
        {
            string t = ""; t += (char)('a'+i);
            if(ok(t)) 
            {
                ans = t;
                break;
            }
        }
        if(ans == "")
        for(int i = 0; i < 26; i++)
        {
            string t = ""; t += (char)('a'+i);
            for(int j = 0; j < 26; j++)
            {
                string t1 = t + (char)('a'+j);
                if(ok(t1)) 
                {
                    ans = t1;
                    break;
                }
            }
            if(ans != "") break;
        }

        if(ans == "")
        for(int i = 0; i < 26; i++)
        {
            string t = ""; t += (char)('a'+i);
            for(int j = 0; j < 26; j++)
            {
                string t1 = t + (char)('a'+j);
                for(int k = 0; k < 26; k++)
                {
                    string t2 = t1 + (char)('a'+k);
                    if(ok(t2)) 
                    {
                        ans = t2;
                        break;
                    }
                }
                if(ans != "") break;
            }
            if(ans != "") break;
        }

        cout << ans << endl;
    }
    // system("pause");
}
```
# C. Diluc and Kaeya
分析:
很明显的DP. 
记$dp[i][j]$表示比例为最近一次比例为$i:j$时的切割方案.
不能证明从一次$a:b$到下一次$a:b$, 新增的一段也是$a:b$
状态转移方程为 $dp[i][j] = dp[i/gcd(i,j)][j/gcd(i,j)] + 1$
还有一个问题, 显然状态数太多了, 将会用到$O(\varphi(n)^2)$的空间, 其中n最大可以去$5e5$, 二维数组开不下.
***map!***
显然实际上不会用到那么多状态, 只需要记录用到的状态就好了.
代码:

```cpp
#include <algorithm>
#include <map>
#include <iostream>
#include <string>
using namespace std;
struct node{
    int a, b;
    bool operator <(const node& o) const{
        if(a != o.a) return a < o.a;
        else return b < o.b;
    }
};
map<node, int> mp;
string s;
int ans[501000];
int gcd(int a, int b)
{
    return b == 0? a: gcd(b, a%b);
}
int main()
{
    int t; cin >> t;
    while(t--)
    {
        int n; cin >> n;
        cin >> s;
        node x;
        x.a = x.b = 0;
        for(int i = 0; i < n; i++)
        {
            if(s[i] == 'D') x.a++;
            else x.b++;

            node y = x;
            int z = gcd(y.a, y.b);
            // printf("%d %d gcd = %d\n",x.a,x.b,z);
            y.a /= z;
            y.b /= z;
            if(mp.find(y) == mp.end()) mp[y] = 1;
            else mp[y]++;
            ans[i] = mp[y];
        }
        for(int i = 0; i < n; i++)
            printf("%d ",ans[i]);
        printf("\n");
        mp.clear();
    }
    // system("pause");
}
```
# D. Omkar and Medians
分析:
考虑每次增加一次前缀, 相当于往数组加入两个数字, 也就是说中位数最多偏移1个位置.
记上一次的中位数为$mid$, 在所有出现过的中位数中第一个比mid严格大于的为$r$, 第一个比严格小于的为$l$.
如果$r$不存在就记为无穷大, 如果$l$不存在就记为**负**无穷大.
那么新加入一个中位数, 记为x. 
x必须存在于$[l, r]$区间才算合法, 否则就是不存在.
那么如何去找第一个比mid大的数,第一个比mid小的数呢?
***~~我会Splay!~~*** 
***map!***
众所周知, map底层是红黑树, 直接用迭代器就好了.
代码:

```cpp
#include <algorithm>
#include <map>
#include <iostream>
using namespace std;
map<int, int> mp;
int a[301000];
int main()
{
    int t; cin >> t;
    while(t--)
    {
        int n; cin >> n;
        for(int i = 0; i < n; i++) cin >> a[i];

        int l = -1e9-1, r = 1e9+1;
        // cout << l << " " << r << endl;
        int mid = a[0];
        mp[mid] = 1;
        bool f = 1;
        for(int i = 1; i < n; i++)
        {

            map<int,int>::const_iterator it = mp.find(mid);
            if(it == mp.begin()) l = -1e9-1;
            else l = (--it)->first;

            it = mp.find(mid);
            if((++it) == mp.end()) r = 1e9+1;
            else r = it->first;

            mid = a[i];
            mp[mid] = 1;
            if(l <= a[i] && a[i] <= r);
            else 
            {
                
                // cout << i << " is not good\n" << l << " " << r << endl;
                f = 0; 
                break;
            }
        }

        if(f) cout << "YES\n";
        else cout << "NO\n";

        mp.clear();
    }
    // system("pause");
}
```
小总结: map是个好东西.