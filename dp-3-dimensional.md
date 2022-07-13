# Dynamic Programming - 3 Dimension

**[1473. Paint House III](https://leetcode.com/problems/paint-house-iii/)**

```
class Solution {
public:
    int minCost(vector<int>& houses, vector<vector<int>>& cost, int m, int n, int target) {
        int dp[m][n][m + 10];
        memset(dp, 0, sizeof(dp));
        int LIMIT = 1e9;
        
        for (int i = 0; i < m;i++) {
            for (int j = 0; j < n;j++) {
                for (int z = 0; z <= m; z++) {
                    dp[i][j][z] = LIMIT;
                }
            } 
        }
        
        if (houses[0] == 0) {
            for (int color = 0; color < n;color++) {
                dp[0][color][1] = cost[0][color];
            }
        } else {
            int color = houses[0] - 1;
            dp[0][color][1] = 0;
        }
        
        for (int i = 1; i < m;i++) {
            if (houses[i] != 0) {
                int color = houses[i] - 1;
                for (int prevColor = 0; prevColor < n; prevColor++) {
                    for (int t = 1;t <= i + 1;t++){
                        if (prevColor == color) {
                            dp[i][color][t] = min(dp[i][color][t], dp[i - 1][color][t]);
                        } else {
                            if(t - 1 >= 1) dp[i][color][t] = min(dp[i][color][t], dp[i - 1][prevColor][t - 1]);
                            // It is ok to remove if(t - 1 >= 1) in the line above.
                        }
                    }
                }
            } else {
                for (int color = 0; color < n; color++) {
                    for (int prevColor = 0; prevColor < n; prevColor++) {
                        if (houses[i - 1] != 0 && houses[i - 1] != prevColor + 1)continue;
                        for (int t = 1;t <= i + 1;t++){
                            if(prevColor == color) {
                                dp[i][color][t] = min(dp[i][color][t], dp[i - 1][prevColor][t] + cost[i][color]);
                            } else {
                                if(t - 1 >= 1) dp[i][color][t] = min(dp[i][color][t], dp[i - 1][prevColor][t - 1] + cost[i][color]);
                                // It is ok to remove if(t - 1 >= 1) in the line above.
                            }
                        }
                    }
                }
            }
        }
        int result = LIMIT;
        for (int color = 0; color < n; color++) result = min(result, dp[m - 1][color][target]);
        return result >= LIMIT ? -1 : result;
        
        // 2022 07 07 THURSDAY NIGHT
        // TIME Complexity - O(house * color * color * target) = O(house^2 * color^2)
        // SPACE Complexity - O(house * color * target) = O(house^2 * color)
    }
};
```
