# LeetCode Contest 293

Finished in 2022-07-02

Rank 1208 / 25150

Score 18

Total Finish Time 0:56:08 + 0:05:00 x 4 = 1:16:08

**[2325. Decode the Message](https://leetcode.com/contest/weekly-contest-300/problems/decode-the-message/)**

```
class Solution {
public:
    string decodeMessage(string key, string message) {
        unordered_map<char, char> decode;
        unordered_map<char, int> cnt;
        char decoded = 'a';
        for(char x: key) {
            if(x == ' ')continue;
            if(cnt[x]++ > 0)continue;
            decode[x] = decoded++;
        }
        string result = "";
        for(char x: message) {
            if(x == ' ')result += " ";
            else result += decode[x];
        }
        return result;
    }
};
```

**[2326. Spiral Matrix IV](https://leetcode.com/contest/weekly-contest-300/problems/spiral-matrix-iv/)**

```
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    vector<vector<int>> spiralMatrix(int n, int m, ListNode* head) {
        vector<vector<int>> mat(n, vector<int> (m, -1));
        int up = 0, down = n - 1, left = 0, right = m - 1;
        while(up <= down && left <= right && head) {
            if(head && up <= down) {
                for(int i = left;i <= right && head;i++) {mat[up][i] = head->val; head = head->next;}
                up++;
            }   
            if(head && left <= right) {
                for(int i = up;i <= down && head;i++){mat[i][right] = head->val; head = head->next;}
                right--;
            }
            if(head && up <= down) {
                for(int i = right;i >= left && head;i--){mat[down][i] = head->val; head = head->next;}
                down--;
            }
            if(head && left <= right) {
                for(int i = down;i >= up && head;i--){mat[i][left] = head->val; head = head->next;}
                left++;
            }
        }
        return mat;
    }
};
```

**[2327. Number of People Aware of a Secret](https://leetcode.com/contest/weekly-contest-300/problems/number-of-people-aware-of-a-secret/)**

```
class Solution {
public:
    int peopleAwareOfSecret(int n, int delay, int forget) {
        vector<long> dp(1010, 0);
        int m = 1e9 + 7;
        dp[1] = 1;
        for(int i = delay + 1;i <= n;i++) {
            long sum = 0;
            for(int j = max(1, i - (forget - 1)); j <= i - delay;j++) {
                sum = (sum + dp[j]) % m;
            }
            dp[i] = sum;
        }
        long result = 0;
        for(int i = max(n - forget + 1, 1); i <= n;i++)result = (result + dp[i]) % m;
        return result;
    }
};
```

**[2328. Number of Increasing Paths in a Grid](https://leetcode.com/contest/weekly-contest-300/problems/number-of-increasing-paths-in-a-grid/)**

```
class Solution {
public:
    int mod = 1e9 + 7;
    int dfs(vector<vector<int>>& grid, int i, int j, vector<vector<long>>& numberOfPaths) {
        int n = grid.size(), m = grid[0].size();
        if(i < 0 || i >= n || j < 0 || j >= m)return 0;
        if(numberOfPaths[i][j] != 0)return numberOfPaths[i][j];
        long result = 1;
        if(i - 1 >= 0 && grid[i - 1][j] > grid[i][j]) {
            result += dfs(grid, i - 1, j, numberOfPaths);
        }
        if(i + 1 < n && grid[i + 1][j] > grid[i][j]) {
            result += dfs(grid, i + 1, j, numberOfPaths);
        }
        if(j - 1 >= 0 && grid[i][j - 1] > grid[i][j]) {
            result += dfs(grid, i, j - 1, numberOfPaths);
        }
        if(j + 1 < m && grid[i][j + 1] > grid[i][j]) {
            result += dfs(grid, i, j + 1, numberOfPaths);
        }
        return numberOfPaths[i][j] = result % mod;
    }
    int countPaths(vector<vector<int>>& grid) {
        int n = grid.size(), m = grid[0].size();
        long sum = 0;
        vector<vector<long>> numberOfPaths(n, vector<long> (m, 0));
        for(int i = 0;i < n;i++) {
            for(int j = 0;j < m;j++) {
                if(numberOfPaths[i][j] == 0){
                    numberOfPaths[i][j] = dfs(grid, i, j, numberOfPaths);
                }
                sum = (sum + numberOfPaths[i][j]) % mod;
            }
        }
        return sum;
    }
};
```
