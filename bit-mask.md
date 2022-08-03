# Bit Mask

CREATED 2022-07-26

## Dynamic Programming with bits indicating state

<https://www.1point3acres.com/bbs/thread-890516-1-1.html>

coding 2：密码锁破解。一共五个数字，每次可以按一个或多个数字，穷举所有的密码输入。例如"1-2-3-45"表示第一下按1，第二下按2，第三下按3，第四下45同时按，同时按的几个数字不考虑先后顺序，搜索+二进制状压。follow up估计是用二进制优化搜索中的状态。但因为一开始就用了二进制所以这轮20分钟就结束了。

```
class Solution {
public:
    int helper() {
        int dp[1 << 5][5 + 1];
        memset(dp, 0, sizeof(dp));
        for(int i = 1; i < (1 << 5);i++) {
            dp[i][1] = 1;
        }
        for(int k = 2;k <= 5;k++) {
            for(int i = 0;i < (1 << 5);i++) {
                for(int j = 0;j < (1 << 5);j++) {
                    if((i & j) != j)continue;
                    if(i == j)continue;
                    dp[i][k] += dp[j][k - 1];
                }
            }
        }
        int sum = 0;
        for(int k = 1;k <= 5;k++) sum += dp[(1 << 5) - 1][k];
        return sum;
    }
}
```
