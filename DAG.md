# DAG - Directed Acyclic Graph

CREATED 2022-07-20

## Undirected Graph 找环(Cycle) - 无向图找环（至少三个点构成环）

**[261. Graph Valid Tree](https://leetcode.com/problems/graph-valid-tree/)**

DFS Solution:

```
class Solution {
public:
    unordered_map<int, vector<int>> tree;
    bool hasCycle(int prev, int current, vector<int>& visited) {
        visited[current]++;
        for(int next: tree[current]) {
            if(!visited[next]) {
                if(hasCycle(current, next, visited))return true;
            }else{
                if(next != prev)return true;
            }
        }
        return false;
    }

    // Another approach
    bool hasCycle2(int prev, int current, vector<int>& visited) {
        visited[current]++;
        for(int next: tree[current]) {
            if(next == prev)continue;
            if(!visited[next]) {
                if(hasCycle2(current, next, visited))return true;
            }else{
                return true;
            }
        }
        return false;
    }

    bool validTree(int n, vector<vector<int>>& edges) {
        for(vector<int>& edge: edges){
            tree[edge[0]].push_back(edge[1]);
            tree[edge[1]].push_back(edge[0]);
        }
        vector<int> visited(n, 0);
        if(hasCycle(-1, 0, visited))return false;
        for(int i = 0;i < n;i++) {
            if(!visited[i])return false;
        }
        return true;
    }
};
```

BFS Solution: 同样，我们遍历时需要记录当前节点从哪个先前节点过来的。

```
class Solution {
public:
    unordered_map<int, vector<int>> tree;
    bool validTree(int n, vector<vector<int>>& edges) {
        for(vector<int>& edge: edges){
            tree[edge[0]].push_back(edge[1]);
            tree[edge[1]].push_back(edge[0]);
        }
        vector<int> visited(n, 0);
        queue<vector<int>> qu;
        qu.push({-1, 0});
        visited[0]++;
        while(!qu.empty()) {
            int prev = qu.front()[0];
            int current = qu.front()[1];
            qu.pop();
            visited[current]++;
            for(int next: tree[current]) {
                if(next == prev)continue;
                if(visited[next]++ > 0)return false;
                qu.push({current, next});
            }
        }
        for(int i = 0;i < n;i++) {
            if(!visited[i])return false;
        }
        return true;
    }
};
```

## Directed Graph 找环(Cycle) - 有向图找环

### DFS 三色倒序解法

```
To detect a cycle in a digraph, we need 3 visit states.

0: not visit yet

1: visited and is one ancestor of current visiting path. If we met a node with 1 state, we detect a cycle.

2: other visited node
```

原因：Directed Graph中，之前遍历过的节点**可能**与当前正在遍历的节点们**不**构成环。但Undirected Graph则是另一回事。

```
class Solution {
public:
    // vector<int> result;
    void dfs(vector<int>& visited, int start, bool& valid, unordered_map<int, vector<int>>& pre) {
        visited[start] = 1;
        for(int next: pre[start]) {
            if(visited[next] == 0)dfs(visited, next, valid, pre);
            else if(visited[next] == 1) valid = false;
        }
        // result.insert(result.begin(), start);
        visited[start] = 2;
    }
    bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
        unordered_map<int, vector<int>> pre;
        for(vector<int> &p:prerequisites) {
            pre[p[1]].push_back(p[0]);
        }
        vector<int> visited(numCourses, 0);
        bool valid = true;
        for(int i = 0;i < numCourses;i++) {
            if(visited[i] == 0)dfs(visited, i, valid, pre);
        }
        // if(!valid)return {};
        // return result;
        return valid;
    }
};
```

### 双array visited数组标记法

```
class Solution {
public:
    vector<int> visited;
    vector<int> inPath;
    bool valid = true;
    unordered_map<int, vector<int>> pre;
    bool hasCycle(int start) {
        if(inPath[start] > 0)return true;
        if(visited[start]++ > 0)return false; // MIND HERE!
        inPath[start]++;
        for(int next: pre[start]) {
            if(hasCycle(next))return true;
        }
        inPath[start]--;
        return false;
    }
    bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
        for(vector<int> &p:prerequisites) {
            pre[p[1]].push_back(p[0]);
        }
        visited.resize(numCourses, 0);
        inPath.resize(numCourses, 0);
        bool valid = true;
        for(int i = 0;i < numCourses;i++) {
            if(visited[i] == 0) {
                if(hasCycle(i))return false;
            }
        }
        return true;
    }
};
```
