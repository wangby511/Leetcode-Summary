## Union Find

Created in 2021-12-15. https://leetcode.com/tag/union-find/

### Main Method

```
unordered_map<int,int> index;

// Find Origin source
int find(int x) {
    if(index.count(x) == 0 || index[x] == x)return x;
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

### 684. Redundant Connection

https://leetcode.com/problems/redundant-connection/

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

### 2076. Process Restricted Friend Requests

https://leetcode.com/problems/process-restricted-friend-requests/

```
class Solution {
public:
    unordered_map<int,int> index;
    int getParent(int x) {
        if(index[x] == x)return x;
        return index[x] = getParent(index[x]);
    }
    vector<bool> friendRequests(int n, vector<vector<int>>& restrictions, vector<vector<int>>& requests) {
        vector<bool> res;
        for(int i = 0;i < n;i++)index[i] = i; // 初始化
        for(int i = 0;i < requests.size();i++) {
            int a = requests[i][0], b = requests[i][1];
            int p = getParent(a), q = getParent(b);
            bool valid = true;
            for(vector<int> &r: restrictions) {
                int x = getParent(r[0]), y = getParent(r[1]);
                if ((x == p && y == q) || (x == q && y == p)) {
                    valid = false;
                    break;
                }
            }
            res.push_back(valid);
            if(valid)index[p] = q;
        }
        return res;
    }
};
```