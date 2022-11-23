# DFS - Depth First Search

CREATED 2022-07-15

UPDATED 2022-11-21

## Permutation排列问题

**[46. Permutations](https://leetcode.com/problems/permutations/)**

排列问题。当前数字交换每一个和后面的数字，之后进行递归操作。本题中没有重复数字。

```
class Solution {
public:
    vector<vector<int>> result;
    void permutation_helper(vector<int>& nums,int begin){
        if (begin == nums.size()){
            result.push_back(nums);
            return;
        }
        for (int i = begin; i < nums.size(); i++){
            swap(nums[i], nums[begin]);
            permutation_helper(nums, 1 + begin);
            swap(nums[i], nums[begin]);  
        }
    }
    vector<vector<int>> permute(vector<int>& nums) {
        permutation_helper(nums, 0);
        return result;
    }
};
```

## 搜索问题 - 判断是否可以从一点到达另外一点

**[787. Cheapest Flights Within K Stops](https://leetcode.com/problems/cheapest-flights-within-k-stops)**

```
class Solution {
public:
    unordered_map<int, vector<vector<int>>> neighbors;
    unordered_map<int, bool> visited;
    bool canReach(int n, unordered_map<int,vector<vector<int>>>& neighbors, int src, int dst) {
        // ... Normal DFS with visited array
    }
    int result = INT_MAX;
    void dfs(int current, int dst, int k, int price) {
        if(k < 0 || price > result)return;
        if(current == dst) {
            result = min(result, price);
            return;
        }
        if(visited[current])return;
        visited[current] = true;
        for(vector<int>& nextFlight: neighbors[current]) {
            dfs(nextFlight[0], dst, k - 1, price + nextFlight[1]);
        }
        visited[current] = false;
    }
    int findCheapestPrice(int n, vector<vector<int>>& flights, int src, int dst, int k) {
        for(vector<int>& flight: flights) {
            neighbors[flight[0]].push_back({flight[1], flight[2]});
        }
        if(!canReach(n, neighbors, src, dst))return -1;
        dfs(src, dst, k + 1, 0);
        return result == INT_MAX ? -1 : result;
    }
};
```

## DFS + MEMO - 记忆化dfs搜索

搜索过程中把中间状态结果保存起来，以便作缓存用，降低时间复杂度以及重复搜索。

**[329. Longest Increasing Path in a Matrix](https://leetcode.com/problems/longest-increasing-path-in-a-matrix/)**

找出矩阵中最长的单调自增路径长度。

对每一个点做DFS，遍历四周的点，如果已有保存结果，则直接取结果值，如果没有则继续在递增的邻居上进行递归搜索。

```
class Solution {
public:
    vector<vector<int>> directions = {{-1, 0}, {1,0}, {0,1}, {0,-1}};
    int dfs(vector<vector<int>>& matrix, int i, int j, vector<vector<int>>& memo) {
        int n = matrix.size();
        int m = matrix[0].size();
        if (memo[i][j] != 0) return memo[i][j];
        int result = 1;
        for (vector<int>& direction: directions) {
            int ii = i + direction[0];
            int jj = j + direction[1];
            if (ii < 0 || ii >= n || jj < 0 || jj >= m) continue;
            if (matrix[ii][jj] >= matrix[i][j]) continue;
            result = max(result, 1 + dfs(matrix, ii, jj, memo));
        }
        return memo[i][j] = result;
        
    }
    int longestIncreasingPath(vector<vector<int>>& matrix) {
        int n = matrix.size();
        if(n == 0)return 0;
        int m = matrix[0].size();
        if(m == 0)return 0;
        vector<vector<int>> memo(n, vector<int> (m,0));
        int result = 0;
        for (int i = 0;i < n;i++) {
            for (int j = 0;j < m;j++) {
                int path = dfs(matrix, i, j, memo);
                result = max(result, path);
            }
        }
        return result;
    }
};
```

Time complexity : O(N * M). Each vertex/cell will be calculated once and only once.

Space complexity : O(N * M). The cache dominates the space complexity.

**[576. Out of Boundary Paths](https://leetcode.com/problems/out-of-boundary-paths/)**

```
class Solution {
public:
    int dp[51][51][51];
    const int mod = 1e9 + 7; // DO NOT CONFUSE WITH m in the below.
    vector<vector<int>> directions = {{1,0}, {-1,0}, {0,1}, {0,-1}};
    int backtrack(int n, int m, int maxMove, int i, int j) {
        if (maxMove == 0) return 0;
        if (dp[i][j][maxMove] != -1) return dp[i][j][maxMove];
        long result = 0;
        for (vector<int> &direction: directions) {
            int newi = i + direction[0];
            int newj = j + direction[1];
            if (newi < 0 || newi >= n || newj < 0 || newj >= m) {
                result++;
            } else {
                result += backtrack(n, m, maxMove - 1, newi, newj);
                result %= mod;
            }
        }
        return dp[i][j][maxMove] = result;
    }
    int findPaths(int n, int m, int maxMove, int startRow, int startColumn) {
        memset(dp, -1, sizeof(dp));
        backtrack(n, m, maxMove, startRow, startColumn);
        return dp[startRow][startColumn][maxMove] == -1 ? 0 : dp[startRow][startColumn][maxMove];
    }
};
```

**[688. Knight Probability in Chessboard](https://leetcode.com/problems/knight-probability-in-chessboard/)**

```
class Solution {
public:
    double dp[26][26][101];
    vector<vector<int>> directions = {{1,-2},{2,-1},{1,2},{2,1},
                                      {-1,-2},{-2,-1},{-1,2},{-2,1}
                                     };
    double backtrack(int n, int k, int i, int j) {
        if(i < 0 || i >= n || j < 0 || j >= n) return 0.0;
        if(k == 0)return dp[i][j][k] = 1.0;
        if(dp[i][j][k] != 0)return dp[i][j][k];
        double result = 0.0;
        for(vector<int> &direction: directions) {
            int newi = i + direction[0];
            int newj = j + direction[1];
            result += backtrack(n, k - 1, newi, newj) / 8.0;
        }
        return dp[i][j][k] = result;
    }
    double knightProbability(int n, int k, int row, int column) {
        memset(dp, 0, sizeof(dp));
        backtrack(n, k, row, column);
        return dp[row][column][k];
    }
};
```
