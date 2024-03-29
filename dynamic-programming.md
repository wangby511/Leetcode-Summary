# Dynamic Programming

CREATED 2022-08-25

## Word Distance Related

**[72. Edit Distance](https://leetcode.com/problems/edit-distance/)**

```
dp[i][j] means the edit distance between word1.substring(0, i) and word2.substring(0, j)

dp[i][j] = 1 + min(dp[i][j - 1], dp[i][j], dp[i - 1][j]), if word1[i - 1] != word2[j - 1]

OR

dp[i][j] = dp[i - 1][j - 1], if word1[i - 1] == word2[j - 1]
```

```
class Solution {
public:
    int minDistance(string word1, string word2) {
        int n = word1.size(), m = word2.size();
        int dp[n + 1][m + 1];
        memset(dp, 0, sizeof(dp));
        for(int i = 0;i <= n;i++)dp[i][0] = i;
        for(int i = 0;i <= m;i++)dp[0][i] = i;
        for(int i = 0;i < n;i++) {
            for(int j = 0;j < m;j++) {
                if(word1[i] == word2[j])dp[i + 1][j + 1] = dp[i][j];
                else {
                    dp[i + 1][j + 1] = 1 + min(dp[i + 1][j], min(dp[i][j], dp[i][j + 1]));
                }
            }
        }
        return dp[n][m];
    }
};
```

## SubArray Related

**[718. Maximum Length of Repeated Subarray](https://leetcode.com/problems/maximum-length-of-repeated-subarray/)**

Find maximum length of the longest common sub-array between two arrays.

dp[i][j] means the current length of the same suffix sub-array between two arrays, which means `nums1[i - t] == nums2[j - t], t = 1,2, ..., dp[i][j]`.

```
class Solution {
public:
    int findLength(vector<int>& nums1, vector<int>& nums2) {
        int n = nums1.size(), m = nums2.size();
        int dp[n + 1][m + 1];
        memset(dp, 0, sizeof(dp));
        int result = 0;
        for(int i = 0;i < n;i++) {
            for(int j = 0;j < m;j++) {
                if (nums1[i] == nums2[j]) {
                    dp[i + 1][j + 1] = dp[i][j] + 1;
                }else{
                    dp[i + 1][j + 1] = 0;
                }
                result = max(result, dp[i + 1][j + 1]);
            }
        }
        return result;
    }
};
```

## Max-Profit Related

**[1235. Maximum Profit in Job Scheduling](https://leetcode.com/problems/maximum-profit-in-job-scheduling/)**

Sort by the endTime ascending order, and use dp[i] to represent the maximum profit we can get from job 1 to i.

dp[i] = max(dp[j] + currentProfit[i], dp[i - 1]);

For each job, if we do it then we need to find the previous non-overlapping jobs. If we don't do it, then use the previous total profit (until last job).

Space - O(N), Time - O(NlogN), N is the size/length of jobs.

```
class Solution {
public:
    int jobScheduling(vector<int>& startTime, vector<int>& endTime, vector<int>& profit) {
        vector<vector<int>> p;
        int len = startTime.size();
        for(int i = 0;i < len;i++) {
            p.push_back({startTime[i], endTime[i], profit[i]});
        }
        sort(p.begin(), p.end(), [](const vector<int>& a, const vector<int>& b){
            return a[1] < b[1];
        });
        vector<int> dp(len, p[0][2]);
        for(int i = 0;i < len;i++) {
            int l = 0, r = i;
            while(l < r) {
                int mid = l + (r - l)/2;
                if(p[mid][1] <= p[i][0])l = mid + 1;
                else r = mid;
            }
            int prev = (i - 1 >= 0)?dp[i - 1] : 0;
            dp[i] = max(prev, ((l - 1 >= 0)?dp[l - 1]:0) + p[i][2]);
        }
        return dp[len - 1];
    }
};
```

## 3 Dimension

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
