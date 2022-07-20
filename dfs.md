# DFS

CREATED 2022-07-15

## DFS + MEMO

记忆化dfs搜索，搜索过程中把状态结果保存起来，以便作缓存用。

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
