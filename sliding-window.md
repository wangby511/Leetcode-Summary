# Sliding Window

**[239. Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/)**

找出window里面的最大元素，保证deque里面的元素是单调递减顺序。

```
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        deque<int> dq;
        vector<int> result;
        for(int i = 0;i < nums.size();i++) {
            while(dq.size() > 0 && dq.front() <= i - k)dq.pop_front();
            while(dq.size() > 0 && nums[dq.back()] < nums[i]) {
                dq.pop_back();
            }
            dq.push_back(i);
            if(i >= k - 1)result.push_back(nums[dq.front()]);
        }
        return result;
    }
};
```

Follow Up - Use the maximum window and the minimum window at the same time, and then do some restrictions:

**[1438. Longest Continuous Subarray With Absolute Diff Less Than or Equal to Limit](https://leetcode.com/problems/longest-continuous-subarray-with-absolute-diff-less-than-or-equal-to-limit/)**

```
class Solution {
public:
    int longestSubarray(vector<int>& nums, int limit) {
        int length = nums.size();
        deque<int> maximum, minimum;
        int begin = 0;
        int result = 0;
        for(int i = 0;i < length;i++) {
            while(maximum.size() && nums[maximum.back()] < nums[i])maximum.pop_back();
            maximum.push_back(i);
            
            while(minimum.size() && nums[minimum.back()] > nums[i])minimum.pop_back();
            minimum.push_back(i);
            
            while(begin <= i && abs(nums[maximum.front()] - nums[minimum.front()]) > limit) {
                begin++;
                while(maximum.size() && maximum.front() < begin)maximum.pop_front();
                while(minimum.size() && minimum.front() < begin)minimum.pop_front();
            }
            result = max(result, i - begin + 1);
        }
        return result;
    }
};
```

**[76. Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/)**

```
class Solution {
public:
    string minWindow(string s, string t) {
        int length = s.size();
        string result = s + s;
        int begin = 0;
        unordered_map<char,int> cnt;
        for (char x: t) cnt[x]++;
        int unMatched = cnt.size();
        for (int i = 0;i < length;i++) {
            if (--cnt[s[i]] == 0)unMatched--;
            while (unMatched == 0 && begin <= i) {
                if (result.size() > i - begin + 1) {
                    result = s.substr(begin, i - begin + 1);
                }
                if (cnt[s[begin++]]++ == 0)unMatched++;
            }
        }
        return (result == s + s) ? "" : result;
    }
};
```

Here comes the template.

For most substring problem, we are given a string and need to find a substring of it which satisfy some restrictions. A general way is to use a hashmap assisted with two pointers. The template is given below.

```
int findSubstring(string s) {
    vector<int> map(128,0);
    int counter; // check whether the substring is valid
    int begin = 0, end = 0; //two pointers, one point to tail and one  head
    int result; //the length of substring

    for() { /* initialize the hash map and counter here */ }

    while(end < s.size()){

        if(map[s[end++]]-- ?){  /* modify counter here */ }

        while(/* counter condition */){ 
                
                /* update result here if finding minimum*/

            //increase begin to make it invalid/valid again
            
            if(map[s[begin++]]++ ?){ /*modify counter here*/ }
        }  

        /* update result here if necessary*/
    }
    return result;
}
```

**[3. Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)**

```
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        int length = s.size();
        unordered_map<char, int> cnt;
        int begin = 0, duplicate = 0, result = 0;
        for(int i = 0;i < length;i++) {
            if(cnt[s[i]]++ > 0) duplicate++;
            while(duplicate > 0 && begin <= i) {
                if(--cnt[s[begin++]] == 1)duplicate--;
            }
            if(duplicate == 0) {
                result = max(result, i - begin + 1);
            }
        }
        return result;
    }
};
```

**[159. Longest Substring with At Most Two Distinct Characters](https://leetcode.com/problems/longest-substring-with-at-most-two-distinct-characters/submissions/)**

```
class Solution {
public:
    int lengthOfLongestSubstringTwoDistinct(string s) {
        int length = s.size();
        unordered_map<char, int> cnt;
        int begin = 0, distinct = 0, result = 0;
        for(int i = 0;i < length;i++) {
            if(cnt[s[i]]++ == 0) distinct++;
            while(distinct > 2 && begin <= i) {
                if(--cnt[s[begin++]] == 0) distinct--;
            }
            if(distinct <= 2) {
                result = max(result, i - begin + 1);
            }
        }
        return result;
    }
};
```

**[340. Longest Substring with At Most K Distinct Characters](https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/submissions/)**

The solution is exactly the same as above besides k = 2.

```
class Solution {
public:
    int lengthOfLongestSubstringKDistinct(string s, int k) {
        int length = s.size();
        unordered_map<char, int> cnt;
        int begin = 0, distinct = 0, result = 0;
        for(int i = 0;i < length;i++) {
            if(cnt[s[i]]++ == 0) distinct++;
            while(distinct > k && begin <= i) {
                if(--cnt[s[begin++]] == 0) distinct--;
            }
            if(distinct <= k) {
                result = max(result, i - begin + 1);
            }
        }
        return result;
    }
};
```

## Reference

[1] <https://leetcode.com/problems/minimum-window-substring/discuss/26808/Here-is-a-10-line-template-that-can-solve-most-'substring'-problems>
