# Mono Stack 总结

Created in 2021-12-12. https://leetcode.com/tag/monotonic-stack/

Updated 2022-08-21

## Max Rectangle Area Problem

**[84. Largest Rectangle in Histogram](https://leetcode.com/problems/largest-rectangle-in-histogram/)**

Search for the maximum rectangle area in a histogram. Using **a monolithic increasing stack** to record numbers. DO NOT forget to add a ZERO at the end of the array to deal with exit criteria.

```
class Solution {
public:
    int largestRectangleArea(vector<int>& height) {
        int result = 0;
        height.push_back(0);
        vector<int> index;
            
        for(int i = 0; i < height.size(); i++){
            while(index.size() > 0 && height[index.back()] >= height[i]){
                int h = height[index.back()];
                index.pop_back();
                    
                int sidx = index.size() > 0 ? index.back() : -1;
                result = max(result, h * (i-sidx-1));
                cout<<h<<" * "<<"("<<i<<"-"<<sidx<<"-1) = "<<h * (i-sidx-1)<<endl;
            }
            index.push_back(i);
        }
        return result;
    }
};
```

Follow up question - **[85. Maximal Rectangle](https://leetcode.com/problems/maximal-rectangle/)** with time complexity O(#row^2 * #column).

**[739. Daily Temperatures](https://leetcode.com/problems/daily-temperatures/)**

```
class Solution {
public:
    vector<int> dailyTemperatures(vector<int>& t) {
        int len = t.size();
        vector<int> res(len, 0);
        stack<int> st;
        for(int i = len - 1;i >= 0;i--) {
            while(st.size() > 0 && t[i] >= t[st.top()]){
                st.pop();
            }
            if(st.empty()) res[i] = 0;
            else res[i] = st.top() - i;
            st.push(i);
        }
        return res;
    }
};
```

**[1673. Find the Most Competitive Subsequence](https://leetcode.com/problems/find-the-most-competitive-subsequence/)**

Add another variable `canBeDeleted`.

```
class Solution {
public:
    vector<int> mostCompetitive(vector<int>& nums, int k) {
        int canBeDeleted = nums.size() - k;
        stack<int> st;
        for(int i = 0;i < nums.size();i++) {
            while(!st.empty() && st.top() > nums[i] && canBeDeleted > 0) {
                st.pop();
                canBeDeleted--;
            }
            st.push(nums[i]);
        }
        vector<int> result;
        while(!st.empty()) {
            result.push_back(st.top());
            st.pop();
        }
        reverse(result.begin(), result.end());
        while(result.size() > k)result.pop_back();
        return result;
    }
};
```

**[402. Remove K Digits](https://leetcode.com/problems/remove-k-digits/)**

```
class Solution {
public:
    string removeKdigits(string num, int k) {
        if(num.size() == k)return "0";
        int K = k;
        int length = num.size();
        stack<char> st;
        for(int i = 0;i < length;i++) {
            while(st.size() > 0 && st.top() > num[i] && k > 0) {
                st.pop();
                k--;
            }
            st.push(num[i]);
        }
        string result = "";
        while(st.size()){
            result = st.top() + result;
            st.pop();
        }
        if(result.size() > num.size() - K){
            result = result.substr(0, num.size() - K);
        }
        while(result.size() && result[0] == '0'){
            result = result.substr(1);
        }
        return result == ""?"0":result;
    }
};
```

**[1944. Number of Visible People in a Queue](https://leetcode.com/problems/number-of-visible-people-in-a-queue/)**

Mono stack + Binary search
```
class Solution {
public:
    int upper_bound(deque<int>& dq, int target) {
        if(dq.empty())return 0;
        if(dq.back() < target)return dq.size();
        int i = 0,j = dq.size();
        while(i < j) {
            int mid = i + (j - i)/2;
            if(dq[mid] <= target)i = mid + 1;
            else j = mid;
        }
        return i + 1;
    }
    vector<int> canSeePersonsCount(vector<int>& h) {
        int length = h.size();
        vector<int> result(length, 0);
        deque<int> dq;
        for(int i = length - 1; i >= 0;i--) {
            result[i] = upper_bound(dq, h[i]);
            while(dq.size() > 0 && h[i] > dq.front())dq.pop_front();
            dq.push_front(h[i]);
        }
        return result;
    }
};
```
