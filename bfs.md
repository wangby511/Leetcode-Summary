# BFS

**[1091. Shortest Path in Binary Matrix](https://leetcode.com/problems/shortest-path-in-binary-matrix/)**

```
class Solution {
public:
    enum enumType {VISITED = 2};
    vector<vector<int>> dirs = {
        {-1,0}, {1,0}, {0,1}, {0,-1}, {-1,-1}, {1,1}, {-1,1}, {1,-1}};
    map<vector<int>, vector<int>> previous;
    int shortestPathBinaryMatrix(vector<vector<int>>& grid) {
        int n = grid.size(), m = grid[0].size();
        queue<vector<int>> qu;
        if (grid[0][0] == 0) qu.push({0,0,1});
        while (!qu.empty()) {
            vector<int> c = qu.front();
            qu.pop();
            int i = c[0], j = c[1], step = c[2];
            if(i == n - 1 && j == m - 1)return step;
            for(vector<int> & dir: dirs) {
                int x = i + dir[0], y = j + dir[1];
                if(x < 0 || x >= n || y < 0 || y >= m || grid[x][y] != 0)continue;
                qu.push({x, y, 1 + step});
                grid[x][y] = VISITED;
                previous[{x, y}] = {i, j};
            } 
        }
        return -1;
    }
};
```

**[286. Walls and Gates](https://leetcode.com/problems/walls-and-gates/)**

Push all gates into queue first. Then for each coordinate, update its neighbor cells and push them to the queue if shorter distance is updated.Repeating above steps until there is nothing left in the queue.

TIME - O(N * M)

```
class Solution {
public:
    void wallsAndGates(vector<vector<int>>& rooms) {
        int n = rooms.size();
        if(n == 0)return;
        int m = rooms[0].size();
        if(m == 0)return;
        queue<vector<int>> qu;
        for(int i = 0;i < n;i++) {
            for(int j = 0;j < m;j++) {
                if(rooms[i][j] == 0)qu.push({i, j, 0});
            }
        }
        vector<vector<int>> dirs = {{-1,0}, {1,0}, {0,1}, {0,-1}};
        while(!qu.empty()) {
            vector<int> current = qu.front();
            qu.pop();
            int dist = current[2];
            for(vector<int>& dir: dirs) {
                int newx = current[0] + dir[0];
                int newy = current[1] + dir[1];
                if(newx < 0 || newx >= n || newy < 0 || newy >= m || rooms[newx][newy] == -1)continue;
                if(rooms[newx][newy] > 1 + dist) {
                    rooms[newx][newy] = 1 + dist;
                    qu.push({newx, newy, 1 + dist});
                }
            }
        }
    }
};
```

**[317. Shortest Distance from All Buildings](https://leetcode.com/problems/shortest-distance-from-all-buildings/)**

```
TODO
```

**[200. Number of Islands](https://leetcode.com/problems/number-of-islands/)**

```
class Solution {
public:
    vector<vector<int>> dirs = {{-1,0},{1,0},{0,-1},{0,1}};
    void bfs(vector<vector<char>>& grid, int i, int j){
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
                    bfs(grid,i,j);
                    ++result;
                }
            }
        }
        return result;
    }
};
```
