# Union Find

Created in 2021-12-15. https://leetcode.com/tag/union-find/ Update 2022-04-30.

## Main Method

```
unordered_map<int,int> index;

// Find Origin source
int find(int x) {
    if(index.count(x) == 0 || index[x] == x)return index[x] = x;
    return index[x] = find(index[x]);
}

OR initialize first

int initialize(int n) {
    for(int i = 0;i < n;i++) index[i] = i;
}
int find(int x) {
    if(index[x] == x)return x;
    return index[x] = find(index[x]);
}


// Union
void connect(int a, int b) {
    index[find(a)] = find(b);
}

// Determine
bool connected(int a, int b) {
    return find(a) == find(b);
}
```

https://leetcode.com/problems/process-restricted-friend-requests/discuss/1576935/C%2B%2B-Union-Find 

Class 写法
```
class UnionFind {
    vector<int> id;
public:
    UnionFind(int n) : id(n) {
        iota(begin(id), end(id), 0);
    }
    void connect(int a, int b) {
        id[find(a)] = find(b);
    }
    int find(int a) {
        return id[a] == a ? a : (id[a] = find(id[a]));
    }
    int connected(int a, int b) {
        return find(a) == find(b);
    }
};
```

## LeetCode Examples

### [684. Redundant Connection](https://leetcode.com/problems/redundant-connection/)

```
class Solution {
public:
    unordered_map<int,int> index;
    int root(int x){
        if(index.count(x) == 0 || index[x] == x)return index[x] = x;
        return index[x] = root(index[x]);
    }
    vector<int> findRedundantConnection(vector<vector<int>>& edges) {
        vector<int> res;
        for(vector<int>& edge:edges){
            int ra = root(edge[0]), rb = root(edge[1]);
            if(ra == rb){
                return edge;
            }else{
                index[ra] = rb;
            }
        }
        return {};
    }
};
```

### [1584. Min Cost to Connect All Points](https://leetcode.com/problems/min-cost-to-connect-all-points/)

```
class Solution {
public:
    unordered_map<int,int> parent;
    int get(int x) {
        if(parent[x] == x)return x;
        return parent[x] = get(parent[x]);
    }
    int minCostConnectPoints(vector<vector<int>>& points) {
        priority_queue<vector<int>, vector<vector<int>>, greater<vector<int>>> pq;
        int length = points.size();
        for(int i = 0; i < length; i++) {
            parent[i] = i;
        }
        for(int i = 0; i < length; i++) {
            for(int j = i + 1; j < length;j++) {
                pq.push({abs(points[i][0] - points[j][0]) + abs(points[i][1] - points[j][1]), i, j});
            }
        }
        int component = length;
        int result = 0;
        while(component > 1) {
            vector<int> current = pq.top();
            pq.pop();
            int parentX = get(current[1]);
            int parentY = get(current[2]);
            if(parentX != parentY) {
                parent[parentX] = parentY;
                result += current[0];
                component--;
            }
        }
        return result;
    }
};
```

### [1135. Connecting Cities With Minimum Cost](https://leetcode.com/problems/connecting-cities-with-minimum-cost/)

```
class Solution {
public:
    unordered_map<int,int> unions;
    int getParent(int x) {
        if (unions[x] == x) return x;
        return unions[x] = getParent(unions[x]);
    }
    int minimumCost(int n, vector<vector<int>>& connections) {
        for (int i = 1; i <= n; i++) unions[i] = i;
        sort(connections.begin(), connections.end(), [](const vector<int>& c1, const vector<int>& c2) {
            return c1[2] < c2[2];
        });
        int result = 0, component = n;
        for (vector<int>& connection: connections) {
            int a = connection[0], b = connection[1], cost = connection[2];
            int parentA = getParent(a), parentB = getParent(b);
            if (parentA != parentB) {
                result += cost;
                unions[parentA] = parentB;
                component--;
            }
        }
        return component != 1 ? -1: result;
    }
};
```

### [1168. Optimize Water Distribution in a Village](https://leetcode.com/problems/optimize-water-distribution-in-a-village/)

```
        ......
int minCostToSupplyWater(int n, vector<int>& wells, vector<vector<int>>& pipes) {
        for (int i = 1; i <= n; i++) unions[i] = i;
        
        // Add a virtual node "0", 
        // connect it to houses with edges weighted by the costs to build wells in these houses.
        for (int i = 0; i < wells.size(); i++) {
            pipes.push_back({0, i + 1, wells[i]});
        }
        ......
```

**[305. Number of Islands II](https://leetcode.com/problems/number-of-islands-ii/)**

```
class Solution {
public:
    unordered_map<string, string> root;
    string serialize(int x, int y) {
        return "(" + to_string(x) + "," + to_string(y) + ")";
    }
    string getRoot(string coordinate){
        if (root.count(coordinate) == 0 || 
            root[coordinate] == coordinate) return root[coordinate] = coordinate;
        return root[coordinate] = getRoot(root[coordinate]);
    }
    vector<int> numIslands2(int m, int n, vector<vector<int>>& positions) {
        int count = 0;
        vector<vector<int>> directions {{1,0}, {-1,0}, {0,1}, {0,-1}};
        vector<int> result;
        unordered_map<string, int> islands;
        for(int i = 0;i < positions.size(); i++){
            if(islands[serialize(positions[i][0], positions[i][1])]++ == 0)count++;
            for(vector<int>& direction: directions){
                int x = positions[i][0] + direction[0];
                int y = positions[i][1] + direction[1];
                if(x < 0 || x >= m || y < 0 || y >= n || islands[serialize(x, y)] == 0)continue;
                string root2 = getRoot(serialize(positions[i][0], positions[i][1]));
                string root1 = getRoot(serialize(x, y));
                if(root1 != root2){
                    root[root2] = root1;
                    count--;
                }
            }
            result.push_back(count);
        }
        return result;
    }
};
```
