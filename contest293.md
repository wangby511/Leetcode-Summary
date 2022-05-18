# LeetCode Contest 293

Finished in 2022/05/14 only with first 3 questions before 9:00 PM PDT

**[2273. Find Resultant Array After Removing Anagrams](https://leetcode.com/problems/find-resultant-array-after-removing-anagrams/)**

```
class Solution {
public:
    bool isAnagram(string word1, string word2) {
        sort(word1.begin(), word1.end());
        sort(word2.begin(), word2.end());
        return word1 == word2;
    }
    vector<string> removeAnagrams(vector<string>& words) {
        vector<string> result;
        int length = words.size();
        for(int i = 0; i < length; i++) {
            if(result.empty() || (result.size() > 0 && !isAnagram(result.back(), words[i]))) {
                result.push_back(words[i]);
            }
        }
        return result;
    }
};
```

**[2274. Maximum Consecutive Floors Without Special Floors](https://leetcode.com/problems/maximum-consecutive-floors-without-special-floors/)**

```
class Solution {
public:
    int maxConsecutive(int bottom, int top, vector<int>& special) {
        int result = 0;
        sort(special.begin(), special.end());
        if(special.size() == 0) {
            return top - bottom + 1;
        }
        int length = special.size();
        for(int i = 0;i < length;i++) {
            if(i == 0) result = max(result, (special[0] - 1) - bottom + 1);
            else result = max(result, (special[i] - 1) - (special[i - 1] + 1) + 1);
        }
        result = max(result, top - (special.back() + 1) + 1);
        return result;
    }
};
```

**[2275. Largest Combination With Bitwise AND Greater Than Zero](https://leetcode.com/problems/largest-combination-with-bitwise-and-greater-than-zero/)**

```
class Solution {
public:
    int largestCombination(vector<int>& candidates) {
        vector<int> bits(32, 0);
        for(int c: candidates) {
            int j = 0;
            while(c) {
                if(c & 0x01) {
                    bits[j]++;
                }
                c /= 2;
                j++;
            }
        }
        int result = 0;
        for(int j = 0;j < 32;j++) result = max(result, bits[j]);
        return result;
    }
};
```

**[2276. Count Integers in Intervals](https://leetcode.com/problems/count-integers-in-intervals/)**

```
class CountIntervals {
public:
    map<int, int> intervals; // TreeMap
    int total;
    CountIntervals() {
        intervals[-1] = -1;
        intervals[INT_MAX] = INT_MAX; // 2 dummy intervals
        total = 0;
    }
    
    void add(int left, int right) {
        auto index = intervals.lower_bound(left);
        index--;
        if (left <= index->second) {
            total += max(0, right - index->second);
            index->second = max(index->second, right);
        } else {
            if (intervals.find(left) != intervals.end()) {
                total -= intervals[left] - left + 1;
            }
            intervals[left] = right;
            total += right - left + 1;
            index = intervals.find(left);
        }
        auto next = intervals.upper_bound(left);
        while (next != intervals.end()) {
            if (right >= next->second) {
                total -= (next->second - next->first + 1);
                intervals.erase(next->first);
                next = intervals.upper_bound(left);
            }else if (right >= next->first) {
                index->second = max(index->second, next->second);
                total += max(0, next->second - right) - (next->second - next->first + 1);
                intervals.erase(next->first);
                break;
            } else {
                break;
            }
        }
    }
    
    int count() {
        return total;
    }
};

/**
 * Your CountIntervals object will be instantiated and called as such:
 * CountIntervals* obj = new CountIntervals();
 * obj->add(left,right);
 * int param_2 = obj->count();
 */
 ```
