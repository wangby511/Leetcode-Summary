# LeetCode Contest 302

Finished in 2022-07-16

Rank 1695 / 24327

Score 18

Total Finish Time 0:36:13 + 0:05:00 x 2 = 0:46:13

https://leetcode.com/contest/weekly-contest-302/

**[2341. Maximum Number of Pairs in Array](https://leetcode.com/contest/weekly-contest-302/problems/maximum-number-of-pairs-in-array/)**

```
class Solution {
public:
    vector<int> numberOfPairs(vector<int>& nums) {
        vector<int> result = {0, 0};
        int length = nums.size();
        unordered_map<int, int> cnt;
        for(int x: nums)cnt[x]++;
        
        for(auto it: cnt) {
            if(it.second % 2) {
                result[1]++;
            }
            result[0] += it.second / 2;
        }
        return result;
    }
};
```

**[2342. Max Sum of a Pair With Equal Sum of Digits](https://leetcode.com/contest/weekly-contest-302/problems/max-sum-of-a-pair-with-equal-sum-of-digits/)**

```
class Solution {
public:
    int maximumSum(vector<int>& nums) {
        unordered_map<int, 
        priority_queue<int, vector<int>, greater<int>>> index;
        for(int x : nums) {
            int copy = x, digits = 0;
            while(copy) {
                digits += copy % 10;
                copy /= 10;
            }
            if(index[digits].size() < 2) {
                index[digits].push(x);
            }else{
                index[digits].push(x);
                index[digits].pop();
            }
        }
        int result = -1;
        for(auto it: index) {
            int sum = 0;
            if(it.second.size() == 2) {
                while(it.second.size() > 0) {
                    sum += it.second.top();
                    it.second.pop();
                }
                result = max(result, sum);
            }
        }
        return result;
    }
};
```

**[2343. Query Kth Smallest Trimmed Number](https://leetcode.com/contest/weekly-contest-302/problems/query-kth-smallest-trimmed-number/)**

```
class Solution {
public:
    vector<int> smallestTrimmedNumbers(vector<string>& nums, vector<vector<int>>& queries) {
        int length = queries.size();
        vector<int> result(length, 0);
        unordered_map<int,unordered_set<int>> needToFind;
        unordered_map<int,unordered_map<int, int>> cacheResult;
        for(vector<int>& query: queries) {
            needToFind[query[1]].insert(query[0]);
        }
        for(auto it: needToFind) {
            int trimLength = it.first;
            vector<pair<string,int>> toBeSorted;
            int index = 0;
            for(string s: nums) {
                if(s.size() <= trimLength) {
                    toBeSorted.push_back({s, index});
                }else{
                    toBeSorted.push_back({s.substr(s.size() - trimLength, trimLength), index});
                }
                index++;
            }
            sort(toBeSorted.begin(), toBeSorted.end());
            for(int j : it.second) {
                cacheResult[trimLength][j] = toBeSorted[j - 1].second;
            }
        }
        for(int i = 0;i < length;i++) {
            vector<int> query = queries[i];
            result[i] = cacheResult[query[1]][query[0]];
        }
        return result;
    }
};
```

**[2344. Minimum Deletions to Make Array Divisible](https://leetcode.com/contest/weekly-contest-302/problems/minimum-deletions-to-make-array-divisible/)**

```
class Solution {
public:
    int minOperations(vector<int>& nums, vector<int>& numsDivide) {
        sort(nums.begin(), nums.end());
        int length1 = nums.size(), result = 0;
        int length2 = numsDivide.size();
        int smallest = numsDivide[0];
        for(int i = 1;i < length2 && smallest > 1;i++) {
            smallest = __gcd(smallest, numsDivide[i]);
        }
        for(int j = 0; j < length1;j++) {
            if(smallest % nums[j] != 0)result++;
            else break;
        }
        return result == length1 ? -1 : result;
    }
};
```
