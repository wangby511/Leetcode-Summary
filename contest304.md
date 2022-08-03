# LeetCode Contest 304

Finished in 2022-07-30

Rank 229 / 26877

Score 18

Total Finish Time 0:32:17

https://leetcode.com/contest/weekly-contest-304/ranking

**[6132. Make Array Zero by Subtracting Equal Amounts](https://leetcode.com/problems/make-array-zero-by-subtracting-equal-amounts/)**

```
class Solution {
public:
    int minimumOperations(vector<int>& nums) {
        unordered_map<int,int> cnt;
        for(int x: nums) {
            if(x > 0) {
                cnt[x]++;
            }
        }
        return cnt.size();
    }
};
```

**[6133. Maximum Number of Groups Entering a Competition](https://leetcode.com/problems/maximum-number-of-groups-entering-a-competition/)**

```
class Solution {
public:
    int maximumGroups(vector<int>& grades) {
        sort(grades.begin(), grades.end());
        int length = grades.size();
        int result = 0;
        int groupSize = 0;
        int prevGroupSize = 0;
        int presum = 0;
        int sum = 0;
        int timer = 1;
        for(int i = 0;i < length;i++) {
            if(++groupSize == timer) {
                timer++;
                sum += grades[i];
                prevGroupSize = groupSize;
                presum = sum;
                sum = 0;
                groupSize = 0;
                result++;
            }else{
                sum += grades[i];
            }
        }
        if(sum > presum && groupSize > prevGroupSize)result++;
        return result;
    }
};
```

**[6134. Find Closest Node to Given Two Nodes](https://leetcode.com/problems/find-closest-node-to-given-two-nodes/)**

```
class Solution {
public:
    void bfs(vector<int>& edges, int start, vector<int>& distance1) {
        int n = edges.size();
        queue<pair<int,int>> qu;
        qu.push({start, 0});
        unordered_map<int,int> visited;
        visited[start]++;
        while(!qu.empty()) {
            pair<int,int> current = qu.front();
            qu.pop();
            int currentNode = current.first;
            int currentDist = current.second;
            distance1[currentNode] = currentDist;
            int nextNode = edges[currentNode];
            if(nextNode != -1 && visited[nextNode]++ == 0) {
                qu.push({nextNode, 1 + currentDist});
            }
        }
    }
    int closestMeetingNode(vector<int>& edges, int node1, int node2) {
        // FIND THE DISTACNE FROM ONE NODE TO ALL OTHER NODES
        // Record the distances
        int n = edges.size();
        vector<int> distance1(n, INT_MAX);
        vector<int> distance2(n, INT_MAX);
        bfs(edges, node1, distance1);
        bfs(edges, node2, distance2);
        int result = INT_MAX;
        int index = -1;
        for(int i = 0;i < n;i++) {
            if(distance1[i] != INT_MAX && distance2[i] != INT_MAX) {
                if(max(distance1[i], distance2[i]) < result){
                    result = max(distance1[i], distance2[i]);
                    index = i;
                }
            }
        }
        return index;
    }
};
```

**[6135. Longest Cycle in a Graph](https://leetcode.com/problems/longest-cycle-in-a-graph/)**

```
class Solution {
public:
    unordered_map<int,int> globalVisited;
    unordered_map<int,int> onCurrentPath;
    int result = -1;
    void dfs(vector<int>& edges, int start, int currentDepth) {
        globalVisited[start] = 1;
        onCurrentPath[start] = currentDepth;
        int next = edges[start];
        if(next != -1) {
            if(globalVisited[next] == 0) {
                dfs(edges, next, 1 + currentDepth);
            }else if(globalVisited[next] == 1) {
                int level2 = 1 + currentDepth;
                int level1 = onCurrentPath[next];
                result = max(result, level2 - level1);
            }
        }
        globalVisited[start] = 2;
        onCurrentPath.erase(start);
    }
    int longestCycle(vector<int>& edges) {
        int n = edges.size();
        
        for(int i = 0;i < n;i++) {
            dfs(edges, i, 1);
        }
        return result;
    }
};
```

Also this is the same:

```
class Solution {
public:
    unordered_map<int,int> globalVisited;
    unordered_map<int,int> onCurrentPath;
    int result = -1;
    void dfs(vector<int>& edges, int start, int currentDepth) {
        globalVisited[start]++;
        onCurrentPath[start] = currentDepth;
        int next = edges[start];
        if(next != -1) {
            if(globalVisited[next] == 0) {
                dfs(edges, next, 1 + currentDepth);
            }else if(onCurrentPath.count(next) != 0) {
                int level2 = 1 + currentDepth;
                int level1 = onCurrentPath[next];
                result = max(result, level2 - level1);
            }
        }
        onCurrentPath.erase(start);
    }
    int longestCycle(vector<int>& edges) {
        int n = edges.size();
        
        for(int i = 0;i < n;i++) {
            if(globalVisited[i] == 0)
                dfs(edges, i, 1);
        }
        return result;
    }
};
```
