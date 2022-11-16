# BFS - Breadth First Search

## 扩散类，从每一个关键点扩散到周围，或检测能否到达

Queue：首先有很多起始点，比如岸边的点，或者指定的点，烂掉的橘子的点，这些要首先加入queue

Visited：其次，对于visited，有两种情况:

1）一种是每visit一个点，**inplace update matrix**。

2）另一种是用visited matrix/array来记录当前节点是否访问过，因为扩散类问题不能re-visit去过的点。

### 单点扩散类

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

### 多点扩散类

起始状态有多个点

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

### 自己根据边或关系构造图

一般做法，根据edge边的关系构造directed有向图或undirected无向图。

```
unordered_map<int, vector<int>> tree; // initialize a hashmap, key is node, value is set of its neighbors
for(vector<int>& edge: edges){
    tree[edge[0]].push_back(edge[1]);
    tree[edge[1]].push_back(edge[0]); // construct undirected graph
}
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

**[399. Evaluate Division](https://leetcode.com/problems/evaluate-division)**

类似货币汇率转化问题

```
class Solution {
public:
    // unordered_map<string, unordered_map<string, double>> memo; // Set up cache for speeding up if necessary
    unordered_map<string, vector<pair<string, double>>> graph;
    double bfs(string s1, string s2, double current) {
        // if (memo[s1].count(s2) != 0) return memo[s1][s2];
        queue<pair<string, double>> qu;
        qu.push({s1, 1.0});
        unordered_map<string, bool> visited;
        visited[s1] = true;
        while (qu.size() > 0) {
            pair<string, double> current = qu.front();
            qu.pop();
            string currentName = current.first;
            double rate = current.second;
            // memo[s1][currentName] = rate;
            if (current.first == s2)return rate;
            for (auto next: graph[current.first]) {
                if(visited[next.first])continue;
                visited[next.first] = true;
                qu.push({next.first, next.second * rate});
            }
        }
        return -1.0;
        
    }
    vector<double> calcEquation(vector<vector<string>>& equations, vector<double>& values, vector<vector<string>>& queries) {
        vector<double> result;
        for (int i = 0;i < equations.size();i++){
            string s1 = equations[i][0];
            string s2 = equations[i][1];
            double times = values[i];
            graph[s2].push_back(make_pair(s1, 1.0/times));
            graph[s1].push_back(make_pair(s2, times));
        }
        for (int i = 0;i < queries.size();i++) {
            string s1 = queries[i][0];
            string s2 = queries[i][1];
            if (graph[s1].size() == 0 || graph[s2].size() == 0){
                result.push_back(-1.0);
                continue;
            }
            double currency = bfs(s1, s2, 1.0);
            result.push_back(currency);
        }
        return result; 
    }
};
```

## 求最短路径问题

求从一个点到另一个点能到达的最短路径，这里分为两种情况：一种为每次搜索到下一届点或状态的代价都相同，第二种为每次搜索付出的代价不同。

### 点与点，状态相互之间连接代价相同的情况

当每个点的代价相同时，使用普通queue做BFS即可，也可以level order层级遍历。

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
        if (grid[0][0] == 0) {
            qu.push({0,0,1});
            grid[0][0] = VISITED;
        }
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
                previous[{x, y}] = {i, j}; // Record the shortest path for the follow up question
            } 
        }
        return -1;
    }
};
```

**[815. Bus Routes](https://leetcode.com/problems/bus-routes/)**

```
class Solution {
public:
    int numBusesToDestination(vector<vector<int>>& routes, int source, int target) {
        unordered_map<int, vector<int>> stationToBuses;
        unordered_map<int, int> visitedBuses;
        unordered_map<int, int> visitedStations;
        for(int i = 0;i < routes.size();i++) {
            for(int station: routes[i])stationToBuses[station].push_back(i);
        }
        queue<vector<int>> qu;
        qu.push({source, 0});
        visitedStations[source]++;
        while(qu.size() > 0) {
            int currentPosition = qu.front()[0];
            int currentStep = qu.front()[1];
            qu.pop();
            if(currentPosition == target)return currentStep;
            for(int bus: stationToBuses[currentPosition]) {
                if(visitedBuses[bus]++ > 0)continue;
                for(int stop: routes[bus]) {
                    if(visitedStations[stop]++ > 0)continue;
                    qu.push({stop, 1 + currentStep});
                }
            }
        }
        return -1;
    }
};
```

**[1730. Shortest Path to Get Food](https://leetcode.com/problems/shortest-path-to-get-food/)**

Start from one node and then do bfs.

```
class Solution {
public:
    int getFood(vector<vector<char>>& grid) {
        int n = grid.size(), m = grid[0].size();
        queue<vector<int>> qu;
        vector<vector<bool>> visited(n, vector<bool> (m, false)); // Try to use boolean instead of int.
        for(int i = 0;i < n;i++) {
            for(int j = 0;j < m;j++) {
                if(grid[i][j] == '*') {
                    visited[i][j] = true;
                    qu.push({i, j, 0});
                }
            }
        }
        vector<vector<int>> dirs = {{-1,0}, {1,0}, {0,-1}, {0,1}};
        while(!qu.empty()) {
            vector<int> current = qu.front();
            qu.pop();
            int x = current[0], y = current[1], step = current[2];
            if(grid[x][y] == '#')return step;
            for(vector<int> &dir: dirs) {
                int newx = x + dir[0], newy = y + dir[1];
                if(newx < 0 || newx >= n || newy < 0 || newy >= m || grid[newx][newy] == 'X') {
                    continue;
                }
                if(!visited[newx][newy]) {
                    visited[newx][newy] = true;
                    qu.push({newx, newy, 1 + step});
                }
            }
        }
        return -1;
    }
};
```

**[286. Walls and Gates](https://leetcode.com/problems/walls-and-gates/)**

Start from multiple nodes and then do bfs in level order.

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
Similar to the question above (286. Walls and Gates).
Do bfs operation from each building to all other spaces. Calculate the sum distance to all buildings for each empty space.
And also remember to check whether this cell can be reached from all buildings.
```

### BFS依次顺序和要求的顺序不一致，类似DIJKSTRA'S算法，此时使用优先级队列

当点到点的代价不同时，此时必须使用优先级队列，保证每次遍历到的状态都是能以最小代价到达的。

**[505. The Maze II](https://leetcode.com/problems/the-maze-ii/)**

Follow Up of "The Maze I" - find the shortest distance: 迷宫题，小球直到撞墙才停下来。

用distance matrix记录停留到点坐标的距离, dijkstra's算法，采用优先级队列实现。

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

**[1293. Shortest Path in a Grid with Obstacles Elimination](https://leetcode.com/problems/shortest-path-in-a-grid-with-obstacles-elimination/)**

This time we are using a 3-dimensional distance array to record each state's shortest distance.

distance[i][j][k] - the shortest distance from (0,0) to (i,j) which has k times of breaking walls chances left.

Also this is belonging to dijkstra's algorithm, using a priority queue.

```
class Solution {
public:
    int shortestPath(vector<vector<int>>& grid, int k) {
        priority_queue<vector<int>, vector<vector<int>>, greater<vector<int>>> pq;
        int n = grid.size(), m = grid[0].size();
        pq.push({0, 0, 0, ((grid[0][0] == 0) ? k : k - 1)});
        
        vector<vector<vector<int>>> distance(n, 
            vector<vector<int>> (m, vector<int> (k + 1, n * m)));
        // int distance[n][m][k + 1];
        // initialize 3 dimensional matrix for distance by giving values
        
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

**[787. Cheapest Flights Within K Stops](https://leetcode.com/problems/cheapest-flights-within-k-stops/)**

See solution in shortest-path.md file. Similar solution with priority queue to pop up the cheapest price every time.

**[752. Open the Lock](https://leetcode.com/problems/open-the-lock/)**

Find the smallest step to open the lock. Also we use a priority queue (min heap). Each time we pop a state from this priority queue, it must be the smallest step to reach this number.

Also we need to avoid dead states and use a visited hashmap to record whether each number we have visited it before or not.

```
class Solution {
public:
    int openLock(vector<string>& deadends, string target) {
        unordered_map<string, int> deadendMaps;
        for(string d: deadends)deadendMaps[d]++;
        
        unordered_map<string, int> visited;
        struct comp {
            bool operator()(const pair<int, string> &a, const pair<int, string> &b) {
                return a > b;
            }
        };
        priority_queue<pair<int, string>, vector<pair<int, string>>, comp> pq;
        if(deadendMaps.count("0000") == 0) {
            pq.push({0, "0000"});
        }
        while (pq.size() > 0) {
            string current = pq.top().second;
            int step = pq.top().first;
            pq.pop();
            if(current == target)return step;
            for(int k = 0;k < 4;k++) {
                char currentChar = current[k];
                
                // plus one
                string nextState = current;
                char copyChar = currentChar;
                if(copyChar == '9')copyChar = '0';
                else copyChar++;
                nextState[k] = copyChar;
                if(deadendMaps.count(nextState) == 0 && visited[nextState]++ == 0) {
                    pq.push({1 + step, nextState});
                }
                
                // minus one
                nextState = current;
                copyChar = currentChar;
                if(copyChar == '0')copyChar = '9';
                else copyChar--;
                nextState[k] = copyChar;
                if(deadendMaps.count(nextState) == 0 && visited[nextState]++ == 0) {
                    pq.push({1 + step, nextState});
                }
            }
        }
        return -1;
    }
};
```

### Follow up - BFS求出最短路径同时并记录当前顺序

Record the path while doing bfs search

**[126. Word Ladder II](https://leetcode.com/problems/word-ladder-ii/)**

Establish the graph first by connecting the words which only have one character difference

Do the bfs search for the graph by beginning with beginWord, also record the previous word when searching for a new word.

```
class Solution {
public:
    bool oneLetterChanged(string a, string b){
        if(a.size() != b.size())return false;
        int n = a.size(), cnt = 0;
        for(int i = 0;i < n;i++){
            if(a[i] != b[i]){
                if(cnt++ > 0)return false;
            }
        }
        return true;
    }
    unordered_map<string, vector<string>> reverseIndexMap;
    vector<vector<string>> result;
    void findReverseOrder(string beginWord, string endWord, vector<string>& path) {
        if(endWord == beginWord) {
            path.push_back(beginWord);
            result.push_back(path);
            path.pop_back();
            return;
        }
        path.push_back(endWord);
        for(string previousWord: reverseIndexMap[endWord]) {
            findReverseOrder(beginWord, previousWord, path);
        }
        path.pop_back();
    }
    vector<vector<string>> findLadders(string beginWord, string endWord, vector<string>& wordList) {
        unordered_set<string> words;
        for(string s:wordList) words.insert(s);
        if(words.find(endWord) == words.end())return {};
        
        // Establish the graph
        unordered_map<string, unordered_set<string>> graph;
        wordList.push_back(beginWord);
        int n = wordList.size();
        for(int i = 0;i < n;i++){
            for(int j = i + 1;j < n;j++){
                if(oneLetterChanged(wordList[i], wordList[j])){
                    graph[wordList[i]].insert(wordList[j]);
                    graph[wordList[j]].insert(wordList[i]);
                }
            }
        }
        wordList.pop_back();
        
        // Do BFS search
        queue<pair<string, int>> qu;
        unordered_map<string, int> distance;
        distance[beginWord] = 0;
        qu.push({beginWord, 0});
        while(!qu.empty()) {
            pair<string, int> current = qu.front();
            qu.pop();
            string currentWord = current.first;
            int step = current.second;
            for(string newWord: graph[currentWord]) {
                if(distance.count(newWord) == 0) {
                    qu.push({newWord, 1 + step});
                    distance[newWord] = 1 + step;
                    reverseIndexMap[newWord] = {currentWord};
                } else if(1 + step == distance[newWord]) {
                    reverseIndexMap[newWord].push_back(currentWord);
                } else if(1 + step < distance[newWord]) {
                    qu.push({newWord, 1 + step});
                    reverseIndexMap[newWord] = {currentWord};
                }
            }
        }
        if(distance.count(endWord) == 0)return {};
        vector<string> path = {};
        findReverseOrder(beginWord, endWord, path);
        for(vector<string> &path: result) {
            reverse(path.begin(), path.end());
        }
        return result;
    }
};
```

## 二分图问题 - Bipartition

每个节点分配两种颜色之一，看是否满足二分图条件。bfs搜索没有染色的节点，看是否和邻居节点形成不同颜色。

**[785. Is Graph Bipartite?](https://leetcode.com/problems/is-graph-bipartite/)**

```
class Solution {
public:
    enum colorType{
        UNDEFINED = 0, GROUPA = 1, GROUPB = 2
    };
    bool bfs(vector<vector<int>>& graph, int begin, vector<int>& colors) {
        queue<int> qu;
        qu.push(begin);
        colors[begin] = GROUPA;
        while(!qu.empty()) {
            int current = qu.front();
            qu.pop();
            int currentColor = colors[current];
            for(int x: graph[current]) {
                if(colors[x] == UNDEFINED) {
                    colors[x] = (currentColor == GROUPA) ? GROUPB : GROUPA;
                    qu.push(x);
                }else{
                    if(colors[x] == currentColor)return false;
                }
            }
        }
        return true;
    }
    bool isBipartite(vector<vector<int>>& graph) {
        int n = graph.size();
        vector<int> colors(n, UNDEFINED);
        for(int i = 0;i < n;i++) {
            if(colors[i] == UNDEFINED){
                if(!bfs(graph, i, colors))return false;
            }
        }
        return true;
    }
};
```

Similar question:

**[886. Possible Bipartition](https://leetcode.com/problems/possible-bipartition/)**

```
class Solution {
public:
    enum colorType{
        UNDEFINED = 0, GROUPA = 1, GROUPB = 2
    };
    unordered_map<int, vector<int>> dislikesmap;
    bool bfs(int start, vector<int>& colors) {
        queue<int> qu;
        qu.push(start);
        colors[start] = GROUPA;
        while(!qu.empty()) {
            int current = qu.front();
            qu.pop();
            int currentColor = colors[current];
            for(int x: dislikesmap[current]) {
                if(colors[x] == UNDEFINED) {
                    colors[x] = (currentColor == GROUPA) ? GROUPB : GROUPA;
                    qu.push(x);
                }else{
                    if(colors[x] == currentColor)return false;
                }
            }
        }
        return true;
    }
    bool possibleBipartition(int n, vector<vector<int>>& dislikes) {
        for(vector<int> real: dislikes){
            dislikesmap[real[0]].push_back(real[1]);
            dislikesmap[real[1]].push_back(real[0]);
        }
        vector<int> colors(n + 1, 0);
        for(int i = 1;i <= n;i++) {
            if(colors[i] == UNDEFINED) {
                if(!bfs(i, colors))return false;
            }
        }
        return true;
    }
};
```

## Reference

[1] [[Leetcode] 【BFS｜宽度优先搜索】题型技巧分类总结](https://www.1point3acres.com/bbs/thread-909366-1-1.html)