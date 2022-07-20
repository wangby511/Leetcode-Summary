# BFS

## 扩散类，从每一个关键点扩散到周围

Queue：首先有很多起始点，比如岸边的点，或者指定的点，烂掉的橘子的点，这些要首先加入queue

Visited：其次，对于visited，有两种情况:

1）一种是每visit一个点，**inplace update matrix**。

2）另一种是用visited matrix/array来记录当前节点是否访问过，因为扩散类问题不能re-visit去过的点。

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

**[417. Pacific Atlantic Water Flow](https://leetcode.com/problems/pacific-atlantic-water-flow/)**

从边缘两侧开始bfs，遍历到最高的山峰，遍历时候只能往高处走。

```
class Solution {
public:
    enum enumType {PACIFIC, ATLANTIC};
    vector<vector<int>> dirs = {{0,1}, {0,-1}, {1,0}, {-1,0}};
    vector<vector<int>> pacificAtlantic(vector<vector<int>>& h) {
        int n = h.size(), m = h[0].size();
        vector<vector<int>> visited1 = bfs(h, PACIFIC);
        vector<vector<int>> visited2 = bfs(h, ATLANTIC);
        vector<vector<int>> result;
        for(int i = 0;i < n;i++){
            for(int j = 0;j < m;j++) {
                if(visited1[i][j] > 0 && visited2[i][j] > 0) {
                    result.push_back({i,j});
                }
            }
        }
        return result;
    }
    vector<vector<int>> bfs(vector<vector<int>>& h, int ocean) {
        int n = h.size(),m = h[0].size();
        vector<vector<int>> visited(n, vector<int> (m, 0));
        queue<vector<int>> qu;
        if (ocean == PACIFIC) {
            for (int i = 0;i < m;i++){
                qu.push({0, i});
                visited[0][i]++;
            }
            for (int i = 1;i < n;i++){
                qu.push({i, 0});
                visited[i][0]++;
            }
        } else {
            for (int i = 0;i < m;i++){
                qu.push({n - 1, i});
                visited[n - 1][i]++;
            }
            for (int i = 0;i + 1 < n;i++){
                qu.push({i, m - 1});
                visited[i][m - 1]++;
            }
        }
        while(qu.size() > 0) {
            vector<int> c = qu.front();
            qu.pop();
            for(vector<int>& dir:dirs) {
                int x = c[0] + dir[0];
                int y = c[1] + dir[1];
                if(x < 0 || x >= n || y < 0|| y >= m || visited[x][y] > 0) continue;
                if(h[c[0]][c[1]] > h[x][y])continue;
                qu.push({x,y});
                visited[x][y]++;
            }
        }
        return visited;
    }
};
```

**[733. Flood Fill](https://leetcode.com/problems/flood-fill/)**

bfs，就地update matrix里的元素表示已经访问过。

```
class Solution {
public:
    vector<vector<int>> floodFill(vector<vector<int>>& image, int sr, int sc, int newColor) {
        int n = image.size(), m = image[0].size();
        int o = image[sr][sc];
        image[sr][sc] = newColor;
        if(o == newColor)return image;
        queue<pair<int, int>> qu;
        qu.push({sr, sc});
        vector<vector<int>> directions = {{1,0},{-1,0},{0,-1},{0,1}};
        
        while(!qu.empty()){
            pair<int,int> c = qu.front();
            qu.pop();
            for(vector<int>& direction: directions){
                int xx = direction[0] + c.first;
                int yy = direction[1] + c.second;
                if(xx < 0 || xx >= n || yy < 0 || yy >= m || image[xx][yy] != o)continue;
                image[xx][yy] = newColor;
                qu.push({xx, yy});
            }
        }
        return image;
    }
};
```

**[994. Rotting Oranges](https://leetcode.com/problems/rotting-oranges/)**

层次(level)遍历, time或distance作为level，同样就地update matrix里的元素表示已经访问过。

```
class Solution {
public:
    vector<vector<int>> dirs = {{-1,0},{1,0},{0,1},{0,-1}};
    int orangesRotting(vector<vector<int>>& grid) {
        int n = grid.size(), m = grid[0].size();
        int left = 0;
        queue<vector<int>> qu;
        for(int i = 0;i < n;i++) {
            for(int j = 0;j < m;j++) {
                if(grid[i][j] == 1)left++;
                else if(grid[i][j] == 2) {
                    qu.push({i, j});
                }
            }
        }
        int time = 0;
        if(left == 0)return 0;
        while(!qu.empty()) {
            int length = qu.size();
            for(int i = 0; i < length;i++) {
                vector<int> c = qu.front();
                qu.pop();
                int x = c[0], y = c[1];
                for(vector<int>& dir: dirs) {
                    int xx = x + dir[0];
                    int yy = y + dir[1];
                    if(xx < 0 || xx >= n || yy < 0 || yy >= m)continue;
                    if(grid[xx][yy] == 1) {
                        qu.push({xx,yy});
                        grid[xx][yy] = 2;
                        left--;
                    }
                }
            }
            if(qu.size() > 0)time++;
        }
        if(left > 0)return -1;
        return time;
    }
};
```

**[841. Keys and Rooms](https://leetcode.com/problems/keys-and-rooms/)**

```
class Solution {
public:
    bool canVisitAllRooms(vector<vector<int>>& rooms) {
        queue<int> unlocks;
        unlocks.push(0);
        int length = rooms.size();
        vector<int> visited(length, 0);
        while(unlocks.size() > 0) {
            int current = unlocks.front();
            unlocks.pop();
            if(visited[current]++ > 0)continue; // visited[current]++; in case it causes forever loop!
            for(int newKey: rooms[current]) {
                unlocks.push(newKey);
            }
        }
        for(int i = 0;i < length;i++)if(visited[i] == 0)return false;
        return true;
    }
};
```

**[261. Graph Valid Tree](https://leetcode.com/problems/graph-valid-tree/)**

```
class Solution {
public:
    bool validTree(int n, vector<vector<int>>& edges) {
        unordered_map<int, vector<int>> tree;
        for(vector<int>& edge: edges){
            tree[edge[0]].push_back(edge[1]);
            tree[edge[1]].push_back(edge[0]);
        }
        queue<int> qu;
        vector<int> visited(n, 0);
        qu.push(0);
        visited[0]++; // DO NOT FORGET THIS LINE!
        unordered_map<int, int> parent;
        while(qu.size() > 0) {
            int current = qu.front();
            qu.pop();
            for(int neighbor: tree[current]) {
                if(parent.count(current)!= 0 && parent[current] == neighbor)continue;
                if(visited[neighbor]++ > 0)return false;
                qu.push(neighbor);
                parent[neighbor] = current;
            }
        }
        for(int i = 0;i < n;i++)if(!visited[i])return false;
        return true;
    }
};
```

## 检测能否到达，一般是给matrix或者graph，从起始点到指定的终点

Queue：加入起始点， 普通BFS，poll出来的每一个值检查是否是终点

Visited：一定要visited， 用来保证不在circle里循环，有的时候会变成是否全联通， 或者是是否能达到所有的点，这个时候就是检查visited set的大小了

**[490. The Maze](https://leetcode.com/problems/the-maze/)**

迷宫题，小球直到撞墙才停下来。用visited记录中途停留到的点的坐标。

```
class Solution {
public:
    bool hasPath(vector<vector<int>>& maze, vector<int>& start, vector<int>& end) {
        vector<vector<int>> directions = {{-1,0},{1,0},{0,1},{0,-1}};
        int n = maze.size(), m = maze[0].size();
        queue<vector<int>> qu;
        qu.push({start[0], start[1]});
        vector<vector<int>> visited(n, vector<int> (m, 0));
        while (!qu.empty()) {
            vector<int> current = qu.front();
            qu.pop();
            int x = current[0], y = current[1];
            if(x == end[0] && y == end[1])return true;
            for (vector<int>& direction: directions) {
                int nextX = x;
                int nextY = y;
                while (nextX >= 0 && nextX < n && nextY >= 0 && nextY < m && maze[nextX][nextY] == 0) {
                    nextX += direction[0];
                    nextY += direction[1];
                }
                nextX -= direction[0];
                nextY -= direction[1];
                if (visited[nextX][nextY]++ == 0) {
                    qu.push({nextX, nextY});
                }
            }
        }
        return false;
    }
};
```

Follow Up - find the shortest distance: 迷宫题，小球直到撞墙才停下来。用distance记录停留到点坐标的距离, dijkstra's算法，采用优先级队列实现。

**[505. The Maze II](https://leetcode.com/problems/the-maze-ii/)**

```
class Solution {
public:
    int shortestDistance(vector<vector<int>>& maze, vector<int>& start, vector<int>& end) {
        vector<vector<int>> directions = {{-1,0},{1,0},{0,1},{0,-1}};
        int n = maze.size(), m = maze[0].size();
        priority_queue<vector<int>> pq;
        pq.push({0, start[0], start[1]});
        int LIMIT = n * m;
        vector<vector<int>> distance (n, vector<int> (m, LIMIT));
        distance[start[0]][start[1]] = 0;
        while (!pq.empty()) {
            vector<int> current = pq.top();
            pq.pop();
            int dist = -current[0], x = current[1], y = current[2];
            if (x == end[0] && y == end[1])return dist;
            for (vector<int>& direction: directions) {
                int nextX = x;
                int nextY = y;
                int currentStep = 0;
                while (nextX >= 0 && nextX < n && nextY >= 0 && nextY < m && maze[nextX][nextY] == 0) {
                    nextX += direction[0];
                    nextY += direction[1];
                    currentStep++;
                }
                currentStep--;
                nextX -= direction[0];
                nextY -= direction[1];
                if (distance[nextX][nextY] > currentStep + dist) {
                    distance[nextX][nextY] = currentStep + dist;
                    pq.push({-(currentStep + dist), nextX, nextY});
                }
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
Do bfs operation from each building to all other spaces. Calculate the sum distance to all buildings for each empty space.
```

**[1293. Shortest Path in a Grid with Obstacles Elimination](https://leetcode.com/problems/shortest-path-in-a-grid-with-obstacles-elimination/)**

This time we are using a 3-dimensional distance array to record each shortest distance.

distance[i][j][k] - the shortest distance from (0,0) to (i,j) which has k times of breaking walls chances left.

dijkstra's - use a priority_queue

```
class Solution {
public:
    int shortestPath(vector<vector<int>>& grid, int k) {
        priority_queue<vector<int>, vector<vector<int>>, greater<vector<int>>> pq;
        int n = grid.size(), m = grid[0].size();
        pq.push({0, 0, 0, ((grid[0][0] == 0) ? k : k - 1)});
        
        vector<vector<vector<int>>> distance(n, 
            vector<vector<int>> (m, vector<int> (k + 1, n * m)));
        
        vector<vector<int>> directions = {{-1,0}, {1,0}, {0,1}, {0,-1}};
        while (pq.size() > 0) {
            vector<int> currentState = pq.top();
            pq.pop();
            int dist = currentState[0], i = currentState[1], j = currentState[2];
            int kLeft = currentState[3];
            if (i == n - 1 && j == m - 1)return dist;
            for (vector<int> &direction: directions) {
                int newi = i + direction[0];
                int newj = j + direction[1];
                if (newi < 0 || newi >= n || newj < 0 || newj >= m)continue;
                if (grid[newi][newj] == 0) {
                    if (distance[newi][newj][kLeft] > 1 + dist) {
                        distance[newi][newj][kLeft] = 1 + dist;
                        pq.push({1 + dist, newi, newj, kLeft});
                    }
                } else {
                    if (kLeft - 1 >= 0 && distance[newi][newj][kLeft - 1] > 1 + dist) {
                        distance[newi][newj][kLeft - 1] = 1 + dist;
                        pq.push({1 + dist, newi, newj, kLeft - 1});
                    }
                }
            }
        }
        return -1;
    }
};
```

## Reference

[1] [[Leetcode] 【BFS｜宽度优先搜索】题型技巧分类总结](https://www.1point3acres.com/bbs/thread-909366-1-1.html)