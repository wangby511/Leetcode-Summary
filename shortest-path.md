# Shortest Path

## Dijkstra's

**Example**

```
    void example() {
        priority_queue<vector<int>,vector<vector<int>>,greater<vector<int>>> pq;
        unordered_map<char,vector<pair<char,int>>> neighbors {
            {'A', {{'B', 4}, {'C', 2}}},
            {'B', {{'D', 2}, {'E', 3}, {'C', 3}}},
            {'C', {{'B', 1}, {'D', 4}, {'E', 5}}},
            {'E', {{'D', 1}}}
        };
        int n = 5;
        vector<int> distance(n, 1e9);
        char start = 'A';
        distance[start - 'A'] = 0;
        pq.push({0, start - 'A'});
        while(!pq.empty()) {
            vector<int> current = pq.top();
            pq.pop();
            int dist = current[0];
            char currentLocation = 'A' + current[1];
            for(pair<char, int>& f: neighbors[currentLocation]) {
                if(dist + f.second < distance[f.first - 'A']) {
                    distance[f.first - 'A'] = dist + f.second;
                    pq.push({dist + f.second, f.first - 'A'});
                }
            }
        }
    }
```

**[743. Network Delay Time](https://leetcode.com/problems/network-delay-time/)**

```
class Solution {
public:
    int networkDelayTime(vector<vector<int>>& times, int n, int k) {
        unordered_map<int,vector<vector<int>>> neighbors;
        for(vector<int>& time: times) {
            neighbors[time[0]].push_back({time[1], time[2]});
        }
        vector<int> distance(n + 1, 1e9);
        distance[k] = 0;
        priority_queue<vector<int>, vector<vector<int>>, greater<vector<int>>> pq;
        pq.push({k, 0});
        while(!pq.empty()) {
            vector<int> current = pq.top();
            pq.pop();
            int dist = current[1];
            for(vector<int>& x: neighbors[current[0]]) {
                if(distance[x[0]] > dist + x[1]) {
                    distance[x[0]] = dist + x[1];
                    pq.push({x[0], distance[x[0]]});
                }
            }
        }
        int maxLatency = 0;
        for(int i = 1;i <= n;i++) {
            if(distance[i] == 1e9)return -1;
            maxLatency = max(maxLatency, distance[i]);
        }
        return maxLatency;
    }
};
```

**[787. Cheapest Flights Within K Stops](https://leetcode.com/problems/cheapest-flights-within-k-stops/)**

```
class Solution {
public:
    bool bfs(int n, unordered_map<int,vector<vector<int>>>& neighbors, int src, int dst) {
        queue<int> qu;
        qu.push(src);
        vector<int> visited(n, 0);
        visited[src]++;
        while(qu.size() > 0) {
            int x = qu.front();
            qu.pop();
            if(x == dst)return true;
            for(vector<int>& f: neighbors[x]) {
                if(visited[f[0]]++ == 0) {
                    qu.push(f[0]);
                }
            }
        }
        return false;
    }
    int findCheapestPrice(int n, vector<vector<int>>& flights, int src, int dst, int k) {
        priority_queue<vector<int>,vector<vector<int>>,greater<vector<int>>> pq;
        unordered_map<int,vector<vector<int>>> neighbors;
        for(vector<int>& flight: flights) {
            neighbors[flight[0]].push_back({flight[1], flight[2]});
        }

        // Need to add this function line below in case TLE
        if(!bfs(n, neighbors, src, dst))return -1;

        pq.push({0, src, 0});
        while(!pq.empty()) {
            vector<int> current = pq.top();
            pq.pop();
            int dist = current[0];
            int currentLocation = current[1];
            int currentStop = current[2];
            if(currentStop > k + 1)continue;
            if(currentLocation == dst)return dist;
            for(vector<int>& f: neighbors[currentLocation]) {
                pq.push({dist + f[1], f[0], 1 + currentStop});
            }
        }
        return -1;
    }
};
```