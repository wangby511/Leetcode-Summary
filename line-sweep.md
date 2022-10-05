# Line Sweep

CREATED 2022-09-20

**[253. Meeting Rooms II](https://leetcode.com/problems/meeting-rooms-ii/)**

Solution I - Use a treemap to do travserse in time ascending order. When it is the starting, plus one. Otherwise minus one.
Then use a room number to travserse it and get the maximum number.

```
class Solution {
public:
    int minMeetingRooms(vector<vector<int>>& intervals) {
        map<int, int> u; // treeMap in stl
        for (vector<int> &interval: intervals){
            u[interval[0]]++;
            u[interval[1]]--;
        }
        int result = 0, total = 0;
        for (auto it: u) {
            total += it.second;
            result = max(result, total);
        }
        return result;
    }
};
```

Solution II - Use 2 separate arrays, starts[] and ends[] to store startPointer and endPointer.

Sort those 2 arrays and use 2 pointers to traverse.

```
class Solution {
public:
    int minMeetingRooms(vector<vector<int>>& intervals) {
        int result = 0;
        vector<int> beginTime, endTime;
        int length = intervals.size();
        for (vector<int> &interval: intervals){
            beginTime.push_back(interval[0]);
            endTime.push_back(interval[1]);
        }
        sort(beginTime.begin(), beginTime.end());
        sort(endTime.begin(), endTime.end());
        int i = 0, j = 0, room = 0;
        while(i < length && j < length) {
        // while(i < length) { // We can gurantee that i < length condition is reached first.
            if (beginTime[i] >= endTime[j]) {
                room--;
                j++;
            } else {
                result = max(++room, result);
                i++;
            }
        }
        return result;
    }
};
```

## Reference

[1] <https://syjohnson11.gitbook.io/leetcode/13_intervalyu_sao_miao_xian>

[2] <https://leetcode.com/tag/line-sweep/>
