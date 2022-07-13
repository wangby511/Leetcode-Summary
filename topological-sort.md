## Topological Sort

Created in 2021-12-15.

** https://www.lintcode.com/problem/127/**

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
        unordered_map<int,vector<int>> neighbour;
        if(n == 1)return {0};
        vector<int> result;
        for(vector<int>& edge:edges){
            neighbour[edge[0]].push_back(edge[1]);
            neighbour[edge[1]].push_back(edge[0]);
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
                for(int x: neighbour[current]) {
                    if(--degree[x] == 0){
                        qu.push(x);
                    }
                }
            }
            level++;
        }
        int maxlevel = *max_element(levels.begin(), levels.end());
        for(int i = 0;i < n;i++) {
            if(levels[i] == maxlevel){
                result.push_back(i);
            }
        } 
        return result;
    }
};
```

**269. Alien Dictionary**


**444. Sequence Reconstruction**
