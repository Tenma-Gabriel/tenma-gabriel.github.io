# A. Fair Playoff
分析：略

```cpp
#include <iostream>
#include <algorithm>
using namespace std;
int a[10];
int main()
{
    int t; cin >> t;
    while(t--)
    {
        for(int i = 0; i < 4; i++) cin >> a[i];
        int x = max(a[0], a[1]), y = max(a[2], a[3]);
        if(x>y) swap(x, y);
        sort(a, a+4);
        if(x == a[2] && y == a[3]) cout << "YES\n";
        else cout << "NO\n";
    }
    // system("pause");
}
```
# B. Array Reodering
分析：
把偶数全部排到最前面，对奇数暴力枚举。复杂度$O(n^2)$

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
const int N = 3010;

int a[N], b[N];
int gcd(int a, int b) {return b == 0? a: gcd(b, a%b);}
int main()
{
    int t; cin >> t;
    while(t--)
    {
        int n; scanf("%d",&n);
        for(int i = 0; i < n; i++) scanf("%d",&b[i]);
        long long ans = 0;
        int cnt = 0;
        for(int i = 0; i < n; i++) 
            if(b[i]%2 == 0) 
            {
                a[cnt++] = b[i];
                ans += n-cnt;
            }
        int x = cnt;
        for(int i = 0; i < n; i++)
            if(b[i]&1) a[cnt++] = b[i];

        for(int i = x; i < n; i++)
            for(int j = i+1; j < n; j++)
            if(gcd(a[i], a[j]) > 1) ans++;
        cout << ans << endl;
    }
    // system("pause");
}
```
# C. Unstable String
分析：
线性dp。
记dp[i][0]表示以第i个字符结尾并且以0结尾的合法子串的数量.
dp[i][1]表示以第i个字符结尾并且以1结尾的合法子串的数量.
$$ dp[i][0]=\left\{
\begin{aligned}
dp[i-1][1]+1 &&&&s[i] = 0 \space or \space ? \\
0 &&&&s[i] = 1
\end{aligned}
\right.
$$
$$ dp[i][1]=\left\{
\begin{aligned}
dp[i-1][0]+1 &&&&s[i] = 1 \space or \space ? \\
0 &&&&s[i] = 0
\end{aligned}
\right.
$$
dp的过程中维护答案和即可.

```cpp
#include <iostream>
#include <algorithm>
#include <string>
using namespace std;
const int N = 2e5+100;
int dp[N][2];
string s;
int main()
{
    int t; cin >> t;
    while(t--)
    {
        cin >> s; 
        int n = s.size();
        for(int i = 0; i < n; i++) dp[i][0] = dp[i][1] = 0;
        long long ans = 1;
        if(s[0] == '1') dp[0][1] = 1;
        else if(s[0] == '0') dp[0][0] = 1;
        else dp[0][1] = dp[0][0] = 1;

        for(int i = 1; i < n; i++)
        {
            if(s[i] == '1') dp[i][1] = dp[i-1][0] + 1;
            else if(s[i] == '0') dp[i][0] = dp[i-1][1] + 1;
            else 
            {
                dp[i][1] = dp[i-1][0] + 1;
                dp[i][0] = dp[i-1][1] + 1;
            }

            ans += max(dp[i][1], dp[i][0]);
        }
        // for(int i = 0 ; i < n; i++) cout << dp[i][0] << " "; cout << endl;
        // for(int i = 0 ; i < n; i++) cout << dp[i][1] << " "; cout << endl;
        cout << ans << endl;
    }
    // system("pause");
}
```
# D. Playoff Tournament
分析:
在二叉堆上DP.
对每个节点来说, 如果$s_i$是0,那么该节点的可能人数就是下标较低的子节点的可能人数, 如果$s_i$是1, 那么就是下标较高的子节点的可能人数. 如果$s_i$是?, 那么该节点就是两个子节点的可能人数之**和**.
边界条件是叶节点, 要么为1, 要么为2.
考虑每次修改只会改变该节点,以及该节点的祖先节点,单词操作是$O(\log n)$的.
每次询问完后, 答案是根节点的可能数.
故总的时间复杂度是$O(n \log n)$
代码:

```cpp
#include <iostream>
#include <string>
#include <algorithm>
using namespace std;
const int N = 600100;
int c[N], dp[N];
int to[N];
void  init(int k, int n)
{
    int cnt = 0;
    for(int i = k-1; i >= 0; i--)
    {
        int x = 1<<i;
        for(int j = 0; j < x; j++)
            c[x+j] = ++cnt;  
    }
    for(int i = 1; i <= n-1; i++)
        to[c[i]] = i;
    // for(int i = 1; i < n; i++)
    //     printf("%d->%d\n",i,to[i]);
}
string s;

int update(int x, int mode, int n)
{
    x = to[x];
    c[x] = mode;
    while(x)
    {
        // printf("now x = %d\n", x);
        if(c[x] == 0)
        {
            if(x >= n/2) dp[x] = 1;
            else dp[x] = dp[x<<1];
        }
        else if(c[x] == 1)
        {
            if(x >= n/2) dp[x] = 1;
            else dp[x] = dp[(x<<1)+1];
        }
        else
        {
            if(x >= n/2) dp[x] = 2;
            else dp[x] = dp[(x<<1)+1] + dp[x<<1];
        }
        x /= 2;
    }
    // for(int i = 1; i < n; i++) cout << dp[i] << " "; cout << endl;
    return dp[1];
}

int main()
{
    int k; scanf("%d",&k);
    int n = 1<<k;
    init(k, n);
    cin >> s;
    for(int i = 1; i <= n-1; i++)
    {
        int x = to[i];
        if(s[i-1] == '0')
        {
            if(x >= n/2) dp[x] = 1;
            else dp[x] = dp[x<<1];
            c[x] = 0;
        }
        else if(s[i-1] == '1')
        {
            if(x >= n/2) dp[x] = 1;
            else dp[x] = dp[(x<<1)+1];
            c[x] = 1;
        }
        else
        {
            if(x >= n/2) dp[x] = 2;
            else dp[x] = dp[(x<<1)+1] + dp[x<<1];
            c[x] = 2;
        }
    }
    // for(int i = 1; i < n; i++) cout << dp[i] << " "; cout << endl;
    int m; cin >> m;
    for(int i = 0; i < m; i++)
    {
        int x; cin >> x >> s;
        int ans;
        if(s == "0") ans = update(x, 0, n);
        else if(s == "1") ans = update(x, 1, n);
        else ans = update(x, 2, n);
        printf("%d\n", ans);
    }
    // system("pause");
}
```
