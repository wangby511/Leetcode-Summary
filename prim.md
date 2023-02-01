# Prim

CREATED 2023-01-28

Trees are connected acyclic undirected graphs.

n个节点的树，一定有n-1个边，少了就不连通了(有孤岛)，多了就有环了。

输入是连接的无向图比如n个节点，找到n-1条边，让n个节点连接起来时总cost最小的subgraph就是mst(minimum spanning tree).

## Implementation Methods

1. Prim Native O(V^2) 稠密图 邻接矩阵

2. Prim PQ O(ElogV) 稀疏图 邻接表 E << V^2

3. Kruskal UF O(ElogE) 或者更大范围ElogV, E <= V^2, 稀疏图

## Basic Idea

加点法 - 每次迭代选择代价最小的边对应的点，加入到最小生成树中。算法从某一个点s开始，逐渐覆盖到整个连通图的所有点。

## LeetCode Examples

**[1135. Connecting Cities With Minimum Cost](https://leetcode.com/problems/connecting-cities-with-minimum-cost)**

```
class Solution {
public:
    int minimumCost(int n, vector<vector<int>>& connections) {
        // construct the graph
        unordered_map<int, vector<pair<int,int>>> graph;
        for (vector<int>& connection: connections) {
            graph[connection[0]].push_back({connection[1], connection[2]});
            graph[connection[1]].push_back({connection[0], connection[2]});
        }

        priority_queue<vector<int>, vector<vector<int>>, greater<vector<int>>> pq;
        int begin = 1, totalCost = 0;
        pq.push({0, begin});
        unordered_map<int, int> visited;
        while (!pq.empty()) {
            int current = pq.top()[1];
            int cost = pq.top()[0];
            pq.pop();
            if (visited[current] > 0) continue;

            visited[current]++; // Add to the MST
            totalCost += cost;
            for (auto neighbor: graph[current]) {
                if (visited[neighbor.first] == 0) {
                    pq.push({neighbor.second, neighbor.first});
                }
            }
        }
        return visited.size() == n ? totalCost : -1;
    }
};
```

The time complexity of the Prim's Algorithm is O((E + V)logV) because each edge is inserted in the priority queue only once and also we performed the delete operation V times. The insertion/deletion in/from priority queue both take logarithmic time.

## Reference

[1] [图的基础算法(一) -- MST最小生成树 [重置版Prim] 古城算法](https://www.youtube.com/watch?v=Ilg-5E6cmTM)
