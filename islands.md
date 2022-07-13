# Islands Problem

**[200. Number of Islands](https://leetcode.com/problems/number-of-islands/)**

BFS(166ms) & DFS (34ms) solutions:

```
class Solution {
public:
    void dfs(vector<vector<char>>& grid,int i,int j){
        int n = grid.size();
        int m = grid[0].size();
        if(i < 0 || i >= n || j < 0 || j >= m)return;
        if(grid[i][j] == '0')return;
        grid[i][j] = '0';
        dfs(grid,i + 1,j);
        dfs(grid,i - 1,j);
        dfs(grid,i,j - 1);
        dfs(grid,i,j + 1);
        
    }
    void bfs(vector<vector<char>>& grid,int i,int j){
        vector<vector<int>> dirs = {{-1,0},{1,0},{0,-1},{0,1}};
        int n = grid.size();
        int m = grid[0].size();
        queue<vector<int>> qu;
        qu.push({i, j});
        while(!qu.empty()) {
            vector<int> current = qu.front();
            qu.pop();
            grid[current[0]][current[1]] = '0';
            for(vector<int>& dir: dirs) {
                int newi = current[0] + dir[0];
                int newj = current[1] + dir[1];
                if(newi < 0 || newi >= n || newj < 0 || newj >= m || grid[newi][newj] == '0')continue;
                qu.push({newi, newj});
                grid[newi][newj] = '0';
            }
        }
    }
    int numIslands(vector<vector<char>>& grid) {
        int result = 0;
        int n = grid.size();
        int m = grid[0].size();
        for(int i = 0;i < n;i++){
            for(int j = 0;j < m;j++){
                if(grid[i][j] == '1'){
                    dfs(grid, i, j); // bfs(grid, i, j); // Change approaches here
                    ++result;
                }
            }
        }
        return result;
    }
};
```

**Follow up** - If we can make only one water pixel into island, what is the maximum island area can we get?

Solution:

Traverse each land pixel, calculate its area with identifier. And also records which identifier does a land pixel belongs.
Traverse each water pixel, calculate the number of distinct island identifiers for its surrounding four neighbors. And calculate all the sum area of its distinct surrounding neighbors if they belong to an island.

```
class Solution {
public:
    vector<vector<int>> directions = {{-1,0}, {1,0}, {0,-1}, {0,1}};
    int calculateIslandArea(vector<vector<char>>& grid, vector<vector<int>>& ids, int i, int j, int islandId){
        int n = grid.size();
        int m = grid[0].size();
        if(i < 0 || i >= n || j < 0 || j >= m)return 0;
        if(grid[i][j] == '0' || ids[i][j] == islandId)return 0;
        ids[i][j] = islandId;
        return 1 + calculateIslandArea(grid, ids, i + 1, j, islandId) + calculateIslandArea(grid, ids, i - 1, j, islandId) + calculateIslandArea(grid, ids, i, j - 1, islandId) + calculateIslandArea(grid, ids, i, j + 1, islandId);
    }
    
    int numIslands(vector<vector<char>>& grid) {
        int n = grid.size();
        if(n == 0)return 0;
        int m = grid[0].size();
        if(m == 0)return 0;
        int numberOfIslands = 0;
        unordered_map<int, int> islandArea;
        map<vector<int>, int> belongsToIcelandId;
        int islandId = 1;
        vector<vector<int>> ids(n, vector<int> (m, 0));
        for(int i = 0;i < n;i++){
            for(int j = 0;j < m;j++){
                if(grid[i][j] == '1' && ids[i][j] == 0){
                    int area = calculateIslandArea(grid, ids, i, j, islandId);
                    islandArea[islandId] = area;
                    ++numberOfIslands;
                    ++islandId;
                }
            }
        }
        int maxResult = 0;
        for(int i = 0;i < n;i++){
            for(int j = 0;j < m;j++){
                if(grid[i][j] == '0') {
                    // See if its neighbors are belonging to different island id.
                    unordered_map<int, int> cntIslands;
                    for(vector<int>& dir: directions) {
                        int newi = i + dir[0];
                        int newj = j + dir[1];
                        if(newi < 0 || newi >= n || newj < 0 || newj >= m || grid[newi][newj] == '0')continue;
                        if(ids[newi][newj] != 0) {
                            cntIslands[ids[newi][newj]]++;
                        }
                        int connectedIslandArea = 1;
                        for (auto &[id, numberOfId]: cntIslands) {
                            connectedIslandArea += islandArea[id];
                        }
                        maxResult = max(maxResult, connectedIslandArea);
                    }
                }
            }
        }
        cout<<maxResult<<endl;
        return numberOfIslands;
    }
/*
    [
    ["1","1","0","0","0"],
    ["1","1","0","0","0"],
    ["0","0","1","0","0"],
    ["0","0","0","1","1"]
    ]
    
    grid:
    1 1 0 0 0 
    1 1 0 0 0 
    0 0 1 0 0 
    0 0 0 1 1 

    ids:
    1 1  0  0 0 
    1 1 (0) 0 0 
    0 0  2  0 0 
    0 0  0  3 3
    
    
    maxResult = 4 + 1 + 1 = 6
    
    make grid[1][2] from water into island, and connect islands with id '1' and '2'. The altogether area is 4 + 1 + 1 = 6.
*/
    
};
```

**[694. Number of Distinct Islands](https://leetcode.com/problems/number-of-distinct-islands/)**

An island is considered to be the same as another if and only if one island can be translated (and not rotated or reflected) to equal the other. Return the number of distinct islands.

```
class Solution {
public:
    vector<vector<int>> directions = {{-1, 0}, {1,0}, {0,-1}, {0,1}};
    void bfs(vector<vector<int>>& grid, int i,int j, vector<vector<int>>& island){
        int n = grid.size();
        int m = grid[0].size();
        queue<pair<int, int>> qu;
        qu.push({i, j});
        grid[i][j] = 2;
        island.push_back({i, j});
        while(!qu.empty()){
            pair<int, int> current = qu.front();
            qu.pop();
            for(vector<int>& direction: directions){
                int nextx = current.first + direction[0];
                int nexty = current.second + direction[1];
                if(nextx < 0 || nextx >= n || nexty < 0 || nexty >= m || grid[nextx][nexty] != 1)continue;
                qu.push({nextx, nexty});
                grid[nextx][nexty] = 2;
                island.push_back({nextx, nexty});
            }
        }
    }
    string serialize(vector<vector<int>>& island) {
        int beginx = island[0][0], beginy = island[0][1];
        string result = "";
        for(vector<int>& i: island) {
            i[0] -= beginx;
            i[1] -= beginy;
            result += "(" + to_string(i[0]) + "," + to_string(i[1]) + "),"; 
        }
        return result;
    }
    int numDistinctIslands(vector<vector<int>>& grid) {
        int n = grid.size();
        if(n == 0)return 0;
        int m = grid[0].size();
        if(m == 0)return 0;
        unordered_map<string, int> cnt;
        for(int i = 0;i < n;i++){
            for(int j = 0;j < m;j++){
                if(grid[i][j] == 1) {
                    vector<vector<int>> island;
                    bfs(grid, i, j, island);
                    sort(island.begin(), island.end());
                    cnt[serialize(island)]++;
                }
            }
        }
        return cnt.size();
    }
};
```
