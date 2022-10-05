# Mono Stack

Created in 2021-12-12. https://leetcode.com/tag/monotonic-stack/

Updated 2022-10-02

## Max Rectangle Area Problem

### Solution I - Straight forward stack

**[84. Largest Rectangle in Histogram](https://leetcode.com/problems/largest-rectangle-in-histogram/)**

Search for the maximum rectangle area in a histogram. Using **a monolithic increasing stack** to record numbers. 
**DO NOT forget to add a ZERO at the end of the array to deal with exit criteria.**

```
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        heights.push_back(0); // Mark the end by pushing back an additional zero number
        int length = heights.size();
        stack<int> st;
        int result = 0;
        for (int i = 0; i < length; i++) {
            while (!st.empty() && heights[st.top()] >= heights[i]) {
                int height = heights[st.top()];
                st.pop();
                result = max(result, height * (i - (st.empty() ? -1: st.top()) - 1));
            }
            st.push(i);
        }
        return result;
    }
};
```

Follow up question on the same solution - **[85. Maximal Rectangle](https://leetcode.com/problems/maximal-rectangle/)** with time complexity O(#row^2 * #column).

### Solution II - Use left and right arrary as the broders

Similar solution: use left & right two arrays to limit the broder of each item number.

left[i] - the index of heights which heights[left[i]] <= heights[i], left[i] < i and is the closest to i.

right[i] - the index of heights which heights[right[i]] < heights[i], right[i] > i and is the closest to i.

```
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        int length = heights.size();
        stack<int> st;
        vector<int> left(length, -1);
        vector<int> right(length, length);
        for (int i = 0; i < length; i++) {
            int height = heights[i];
            while (!st.empty() && !(heights[st.top()] <= height)) {
                st.pop();
            }
            left[i] = st.empty() ? -1: st.top();
            st.push(i);
        }

        while(st.size() > 0)st.pop();
        for (int i = length - 1; i >= 0; i--) {
            int height = heights[i];
            while (!st.empty() && !(heights[st.top()] < height)) {
                st.pop();
            }
            right[i] = st.empty() ? length: st.top();
            st.push(i);
        }

        int area = 0;
        for (int i = 0; i < length; i++) {
            area = max(area, (right[i] - left[i] - 1) * heights[i]);
        }
        return area;
    }
};
```

Similar question - **[907. Sum of Subarray Minimums](https://leetcode.com/problems/sum-of-subarray-minimums)**

Solution: use left & right two arrays to limit the broder of each item number.

```
class Solution {
public:
    int sumSubarrayMins(vector<int>& arr) {
        int length = arr.size();
        stack<int> st;
        vector<int> left(length, -1);
        vector<int> right(length, length);

        // mono increasing-or-equal stack
        for (int i = 0; i < length; i++) {
            int num = arr[i];
            while (!st.empty() && !(arr[st.top()] <= num)) {
                st.pop();
            }
            left[i] = st.empty() ? -1: st.top();
            st.push(i);
        }

        while(st.size() > 0)st.pop();

        // mono increasing stack by reversely traversing the array
        for (int i = length - 1; i >= 0; i--) {
            int num = arr[i];
            while (!st.empty() && !(arr[st.top()] < num)) {
                st.pop();
            }
            right[i] = st.empty() ? length: st.top();
            st.push(i);
        }

        long result = 0;
        const int mod = 1e9 + 7;
        for (int i = 0; i < length; i++) {
            result += (long)(i - left[i]) * (long)(right[i] - i) * (long)arr[i];
            result %= mod;
        }
        return result;
    }
};
```

### Data Stream Problems

**[901. Online Stock Span](https://leetcode.com/problems/online-stock-span/)**

Use a mono decreasing stack

```
class StockSpanner {
public:
    stack<pair<int,int>> prices;
    int index;
    StockSpanner() {
        index = 0;
    }
    
    int next(int price) {
        while(prices.size() > 0 && !(prices.top().first > price)) {
            prices.pop();
        }
        int day = prices.empty()? 0 : prices.top().second;
        prices.push({price, ++index});
        return index - day;
    }
};

/**
 * Your StockSpanner object will be instantiated and called as such:
 * StockSpanner* obj = new StockSpanner();
 * int param_1 = obj->next(price);
 */
```

**[739. Daily Temperatures](https://leetcode.com/problems/daily-temperatures/)**

Traverse in reverse order and use a mono decreasing stack

```
class Solution {
public:
    vector<int> dailyTemperatures(vector<int>& temperatures) {
        stack<int> st;
        int length = temperatures.size();
        vector<int> result(length, 0);
        for(int i = length - 1; i >= 0;i--) {
            while(!st.empty() && !(temperatures[st.top()] > temperatures[i])) {
                st.pop();
            }
            if(!st.empty())result[i] = st.top() - i;
            st.push(i);
        }
        return result;
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
