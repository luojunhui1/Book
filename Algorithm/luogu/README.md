# 洛谷刷题

**build for exercise in 洛谷**

## 壹 · [DP基础](https://www.luogu.com.cn/training/5011#problems)

### [P1049 [NOIP2001 普及组\] 装箱问题 ](https://www.luogu.com.cn/problem/P1049)

```c++
/**经典DP**/
#include <iostream>
#include <vector>
#include <cstdio>
#include <cmath>

using namespace std;

const int M = 25000;
const int N = 50;

int main()
{
    freopen("input.txt","r",stdin);

    int vol,n;
    int dp[M] = {0};
    int cubes[N];

    scanf("%d%d",&vol,&n);

    for(int i = 0; i < n; i++)
        scanf("%d",cubes + i);
    
    for(int i = 0; i <= vol; i++)
    {
        for(int j = 0; j < n; j++)
        {
            if(i - dp[i] >= cubes[j])
                dp[i] += cubes[j];
            else if(i - cubes[j] > 0)
                dp[i] = max(cubes[j] + dp[i - cubes[j]], dp[i]);
        }
    }

    printf("%d\n",vol - dp[vol]);

    return 0;
}
```

### [P1048 [NOIP2005 普及组\] 采药 - 洛谷 ](https://www.luogu.com.cn/problem/P1048)

```c++
/**
 * 经典背包，但是我已经不熟悉了，简单来说：
 * dp[i][j]表示前i个物品中容量为j时的最大价值，每次的更新矩阵为dp[i][j] = max(dp[i - 1][j], dp[i - 1][j - weight[i]] + val[i])
 * max函数中第一个值表示不将第i个物体添加到背包中，第二个值表示将第i个物体添加到背包中。
 * OK,接下来将其缩小到一维矩阵中，注意此时j的遍历不能从前往后，即不能从背包容量小到背包容量大的方向遍历，由于使用的是同一个矩阵，
 * 这样遍历会导致相同的物体被多次添加到背包中，只能从后往前遍历。
 * End!
 * **/
#include <iostream>
#include <vector>
#include <cstdio>
#include <cmath>

using namespace std;

const int T = 1000;
const int M = 105;

int main()
{
    freopen("input.txt","r",stdin);

    int aviableTime,n;
    int timeTable[M],valTable[M];
    int dp[T] = {0};

    cin>>aviableTime>>n;
    
    for(int i = 1; i <= n; i++)
    {
        cin>>timeTable[i];
        cin>>valTable[i];
    }

    for(int i = 1; i <= n; i++)
    {
        for(int j = aviableTime; j >= timeTable[i] ; j--)
        {
            dp[j] = max(dp[j], dp[j - timeTable[i]] + valTable[i]);
        }
    }

    printf("%d\n", dp[aviableTime]);
    
    return 0;
}
```

### [P1018 [NOIP2000 提高组\] 乘积最大](https://www.luogu.com.cn/problem/P1018)

```c++
/**有一说一，知道是dp的情况下，知道dp矩阵中元素的意义的情况下，错过状态方程，并且高精度计算并不熟练
以下代码没有高精，将在明天内改写成高精，别刷提高了，先写完普及吧**/
#include <iostream>
#include <cstdio>
#include <cmath>
using namespace std;

#define MAXN 45

int main()
{
    freopen("input.txt","r",stdin);
    
    int n,k;
    int nums[MAXN];
    long long a[MAXN][MAXN];   
    long long dp[MAXN][MAXN] = {0};

    char c;
    
    scanf("%d%d",&n,&k);
    
    char str[MAXN];
    scanf("%s",str);

    for(int i = 1; i <= n; i++)
    {
        nums[i] = str[i - 1] - '0';
    }
    
    for(int i = 1; i <= n ;i++)
    {
        a[i][i] = nums[i];
        for(int j = i + 1;j <= n ;j++)
        {
            a[i][j] = a[i][j - 1]*10 + nums[j];
        }
    }

    for(int i = 1; i <= n; i++)
        dp[i][0] = a[1][i];
    
    for(int i = 1; i <= k; i++)
    {
        for(int j = 1; j <=n ; j++)
        {
            for(int h = j; h > i; h--)
            {
                dp[j][i] = max(dp[j][i], dp[h - 1][i - 1]*a[h][j]);
            }
        }
    }

    printf("%lld\n", dp[n][k]);

    return 0;
}
```

### [P1057 [NOIP2008 普及组\] 传球游戏](https://www.luogu.com.cn/problem/P1057)

```c++
/**在dp的规律上，可能需要相信更朴素的规律，就从左右两边两个人那里获得，dp[i][j]表示传球次数为i时到第j个人的
可能次数，再确定初始状态，一直向前推导就可以了**/
#include <iostream>
#include <cstdio>
#include <cmath>

using namespace std;

const int MAXN = 35;

int dp[MAXN][MAXN],i,j,m,n;
int main()
{
    freopen("input.txt","r",stdin);
	
    cin>>n>>m;

	dp[0][1]=1;

	for(int i=1; i<=m; i++)
    {
        dp[i][1]=dp[i-1][n]+dp[i-1][2];
        dp[i][n]=dp[i-1][1]+dp[i-1][n - 1];

        for(int j = 2; j < n; j++)
        {
            dp[i][j]=dp[i-1][j-1]+dp[i-1][j+1];
        }
    }
                
	cout<<dp[m][1]<<endl;
	return 0;
}
```

