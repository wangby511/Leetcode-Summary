# Interval

CREATED 2022-10-02

## Union 求并集

**[56. Merge Intervals](https://leetcode.com/problems/merge-intervals/)**

```
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        vector<vector<int>> result;
        sort(intervals.begin(), intervals.end());
        result.push_back(intervals[0]);
        for(int i = 1;i < intervals.size();i++) {
            if(intervals[i][0] > result.back()[1]) {
                result.push_back(intervals[i]);
            } else{
                result.back()[1] = max(result.back()[1], intervals[i][1]);
            }
        }
        return result;
    }
};
```

## Intersection 求交集

**[986. Interval List Intersections](https://leetcode.com/problems/interval-list-intersections)**

```
class Solution {
public:
    vector<vector<int>> intervalIntersection(vector<vector<int>>& firstList, vector<vector<int>>& secondList) {
        vector<vector<int>> result;
        int len1 = firstList.size(), len2 = secondList.size();
        int i = 0,j = 0;
        while(i < len1 && j < len2) {
            if(firstList[i][1] < secondList[j][0])i++;
            else if (secondList[j][1] < firstList[i][0])j++;
            else {
                int begin = max(firstList[i][0], secondList[j][0]);
                int end = min(firstList[i][1], secondList[j][1]);
                result.push_back({begin, end});
                if(firstList[i][1] < secondList[j][1])i++;
                else j++;
            }
        }
        return result;
    }
};
```

## Others

**[57. Insert Interval](https://leetcode.com/problems/insert-interval)**

```
class Solution {
public:
    vector<vector<int>> insert(vector<vector<int>>& intervals, vector<int>& newInterval) {
        int length = intervals.size();
        vector<vector<int>> result;
        int i = 0;
        for (i = 0;i < length && intervals[i][1] < newInterval[0]; i++) {
            result.push_back(intervals[i]);
        }
        result.push_back(newInterval);
        for (;i < length && !(newInterval[1] < intervals[i][0]); i++) {
            result.back()[0] = min(result.back()[0], intervals[i][0]);
            result.back()[1] = max(result.back()[1], intervals[i][1]);
        }
        for (;i < length; i++) result.push_back(intervals[i]);
        return result;
    }
};
```

Time complexity O(n), Space complexity O(n)
