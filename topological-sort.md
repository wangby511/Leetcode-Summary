# Topological Sort

Created in 2021-12-15

Updated 2022-07-31

Remember: no duplicate directed edges in the graph! Only in DAG(Directed Acyclic Graph) graph.

推荐BFS Kahn算法，基于贪心，每次从入度为0度点开始，正序为拓扑。单纯拓扑不推荐DFS。

## Graph Related

Ways of representing graph:

1) adjacency list

2) adjacency matrix

3) list of edges

## Basic Steps

1 建立图 adjacency list format

2 找出in-degree

3 找出入度为0的入口，加入队列queue中。

4 BFS拓扑排序，依次把入度减为0的节点加入队列，最后查看是否全部遍历到，否则有环或者非连通图。

```
// Construct the graph with adjacency list format
unordered_map<T, vector<T>> neighbors;

// Find all the nodes' in-degree
unordered_map<T, int> inDegree;
for (E directedEdge: edges) {
    inDegree[directedEdge[0]]+=0;
    inDegree[directedEdge[1]]++;
}

// Find all the nodes with zero in-degree and put them into the queue initially
queue<T> qu;
for (auto it: inDegree) {
    if(it.second == 0) {
        qu.push(it.first);
    }
}

// Traverse with BFS order
while (!qu.empty()) {
    T current = qu.front();
    qu.pop();
    for (T neighbor : current's neighbors nodes) {
        if(--inDegree[neighbor] == 0) {
            qu.push(neighbor);
        }
    }
}

// Determine if all nodes have been traversed or not
```

**[https://www.lintcode.com/problem/127/](https://www.lintcode.com/problem/127/)**

```
/**
 * Definition for Directed graph.
 * struct DirectedGraphNode {
 *     int label;
 *     vector<DirectedGraphNode *> neighbors;
 *     DirectedGraphNode(int x) : label(x) {};
 * };
 */

class Solution {
public:
    /**
     * @param graph: A list of Directed graph node
     * @return: Any topological order for the given graph.
     */
    vector<DirectedGraphNode*> topSort(vector<DirectedGraphNode*> graph) {
        // write your code here
        unordered_map<DirectedGraphNode*, int> indegree;
        for(DirectedGraphNode* node: graph) {
            indegree[node]+=0;
            for(DirectedGraphNode* n: node->neighbors) {
                indegree[n]++;
            }
        }
        queue<DirectedGraphNode*> qu;
        vector<DirectedGraphNode*> result;
        for(auto it: indegree) {
            if(it.second == 0) {
                qu.push(it.first);
            }
        }
        while(qu.size() > 0) {
            DirectedGraphNode* current = qu.front();
            result.push_back(current);
            qu.pop();
            for(DirectedGraphNode* n : current->neighbors) {
                if(--indegree[n] == 0) {
                    qu.push(n);
                }
            }
        }
        return result;
    }
};
```

**207. Course Schedule && 210. Course Schedule II**

判断并找出正确的上课顺序。

https://leetcode.com/problems/course-schedule/

https://leetcode.com/problems/course-schedule-ii/

BFS topological-sort解法：

```
class Solution {
public:
    vector<int> findOrder(int numCourses, vector<vector<int>>& prerequisites) {
        vector<int> result;
        vector<int> degree(numCourses, 0);
        unordered_map<int, vector<int>> pre;
        for (vector<int> &p: prerequisites) {
            degree[p[0]]++;
            pre[p[1]].push_back(p[0]);
        }
        queue<int> qu;
        for (int i = 0; i < numCourses; i++) {
            if(degree[i] == 0){
                qu.push(i);
            }
        }
        while (!qu.empty()) {
            int current = qu.front();
            qu.pop();
            result.push_back(current);
            for (int next: pre[current]) {
                if(--degree[next] == 0) {
                    qu.push(next);
                }
            }
        }
        if(result.size() < numCourses)return {};
        return result;

        // return result.size() == numCourses; is Leetcode 207. Course Schedule
    }
};
```

**[310. Minimum Height Trees](https://leetcode.com/problems/minimum-height-trees/)**

利用topological sort找出入度最多的节点，其可能构成树的根结点并且使得树的高度最小。bfs层级遍历。

```
class Solution {
public:
    vector<int> findMinHeightTrees(int n, vector<vector<int>>& edges) {
        vector<int> degree(n, 0);
        unordered_map<int,vector<int>> neighbor;
        if(n == 1)return {0};
        vector<int> result;
        for(vector<int>& edge:edges){
            neighbor[edge[0]].push_back(edge[1]);
            neighbor[edge[1]].push_back(edge[0]);
            degree[edge[0]]++;
            degree[edge[1]]++;
        }
        queue<int> qu;
        vector<int> levels(n, 1);
        for(int i = 0;i < n;i++) {
            if(--degree[i] == 0){
                qu.push(i);
            }
        }
        int level = 1;
        while(!qu.empty()) {
            int size = qu.size();
            for(int i = 0;i < size;i++) {
                int current = qu.front();
                qu.pop();
                levels[current] = level;
                for(int x: neighbor[current]) {
                    if(--degree[x] == 0){
                        qu.push(x);
                    }
                }
            }
            level++;
        }
        int maxLevel = *max_element(levels.begin(), levels.end());
        for(int i = 0;i < n;i++) {
            if(levels[i] == maxLevel){
                result.push_back(i);
            }
        } 
        return result;
    }
};
```

**[2115. Find All Possible Recipes from Given Supplies](https://leetcode.com/problems/find-all-possible-recipes-from-given-supplies/)**

The indegree is no longer a integer number, instead it is a hash-set.

```
// > 1200 ms
class Solution {
public:
    vector<string> findAllRecipes(vector<string>& recipes, vector<vector<string>>& ingredients, vector<string>& supplies) {
        unordered_map<string, unordered_set<string>> indegree;
        unordered_map<string, vector<string>> to;
        int length = recipes.size();
        for(int i = 0;i < length;i++) {
            for(string ingredient: ingredients[i]) {
                to[ingredient].push_back(recipes[i]);
                indegree[recipes[i]].insert(ingredient);
            }
        }
        queue<string> qu;
        for(string supply: supplies)qu.push(supply);
        vector<string> result;
        while(qu.size()) {
            string current = qu.front();
            qu.pop();
            for(string next: to[current]) {
                if(indegree[next].find(current) != indegree[next].end()) {
                    indegree[next].erase(current);
                }
                if(indegree[next].size() == 0) {
                    qu.push(next);
                    result.push_back(next);
                }
            }
        }
        return result;
    }
};
```

Also the indegree can be an integer number!

```
class Solution {
public:
    vector<string> findAllRecipes(vector<string>& recipes, vector<vector<string>>& ingredients, vector<string>& supplies) {
        unordered_map<string, int> indegree;
        unordered_map<string, vector<string>> to;
        int length = recipes.size();
        for(int i = 0;i < length;i++) {
            for(string ingredient: ingredients[i]) {
                to[ingredient].push_back(recipes[i]);
                indegree[recipes[i]]++;
            }
        }
        queue<string> qu;
        for(string supply: supplies)qu.push(supply);
        vector<string> result;
        while(qu.size()) {
            string current = qu.front();
            qu.pop();
            for(string next: to[current]) {
                if(--indegree[next] == 0) {
                    qu.push(next);
                    result.push_back(next);
                }
            }
        }
        return result;
    }
};
```

**[269. Alien Dictionary](https://leetcode.com/problems/alien-dictionary/)**

```
class Solution {
public:
    string alienOrder(vector<string>& words) {
        //每个字母都要考虑
        int n = words.size();
        unordered_map<char,vector<char>> tpo;
        unordered_map<char,int> degree;
        string result = "";
        
        //每个字母都要考虑
        for(auto w: words){
            for(auto c: w){ 
                degree[c] = 0;
            }
        }
        
        for(int i = 0;i + 1 < n;i++){
            string word1 = words[i];
            string word2 = words[i + 1];
            int n1 = word1.size();
            int n2 = word2.size();
            int j = 0;
            while(j < n1 && j < n2 && word1[j] == word2[j])j++;
            if(j < n1 && j == n2)return "";
            if(j < n1 && j < n2){
                tpo[word1[j]].push_back(word2[j]);
                degree[word2[j]]++;
            }
        }

        // 找出入度为0的字母加入队列
        queue<char> q;
        for(auto it:degree){
            if(it.second == 0){
                q.push(it.first);
            }
        }

        // 开始拓扑排序
        while(!q.empty()){
            char x = q.front();
            q.pop();
            result += x;
            for(char next: tpo[x]){
                if(--degree[next] == 0){
                    q.push(next);
                }
            }   
        }

        // 检查是否有剩余字母入度不为零
        // for(auto it:degree){
        //     if(it.second > 0)return "";
        // }
        if(result.size() != degree.size())return "";
        return result;
        
    }
};
```

**444. Sequence Reconstruction**

TODO
