# Two Pointer

Created in 2022-07-05

1 快慢指针（两个指针步长不同）

2 左右端点指针（两个指针分别位于头尾，并往中间移动，步长不确定）

3 固定间距指针（两个指针间距相同，步长相同）

## 快慢指针，步长不同

**[209. Minimum Size Subarray Sum](https://leetcode.com/problems/minimum-size-subarray-sum/)**

Find the minimal length of a contiguous sub-array, of which the sum is greater than or equal to target.

Use two pointers, one is normal for-loop and the other one controls that the sum between them is smaller than target.

```
class Solution {
public:
    int minSubArrayLen(int target, vector<int>& nums) {
        int result = nums.size() + 1;
        long product = 0;
        int begin = 0;
        for(int i = 0;i < nums.size();++i) {
            int x = nums[i];
            product += x;
            while(begin <= i && product >= target) {
                result = min(result, i - begin + 1);
                product -= nums[begin++];
            }
        }
        return (result == nums.size() + 1)? 0 : result;
    }
};
```

The same idea is in ***[713. Subarray Product Less Than K](https://leetcode.com/problems/subarray-product-less-than-k/)**

```
class Solution {
public:
    int numSubarrayProductLessThanK(vector<int>& nums, int k) {
        int result = 0;
        long product = 1;
        int begin = 0;
        for(int i = 0;i < nums.size();++i) {
            int x = nums[i];
            product *= x;
            while(begin <= i && product >= k) {
                product /= nums[begin++];
            }
            result += i + 1 - begin;
        }
        return result;
    }
};
```

## 左右端点指针，相向而行

**[15. 3 Sum](https://leetcode.com/problems/3sum/)**

```
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> result;
        sort(nums.begin(), nums.end());
        int len = nums.size();
        for (int i = 0;i < len;i++) {
            int j = i + 1, k = len - 1;
            if (i > 0 && nums[i] == nums[i - 1])continue;
            while (j < k) {
                int sum = nums[i] + nums[j] + nums[k];
                if (sum > 0) {
                    k--;
                } else if(sum < 0) {
                    j++;
                } else {
                    result.push_back({nums[i], nums[j], nums[k]});
                    j++;
                    k--;
                    while(j < len && nums[j] == nums[j - 1])j++;
                    while(k >= 0 && nums[k] == nums[k + 1])k--;
                }
            }
        }
        return result;
    }
};
```
Time Complexity - O(N^2)

**[1570. Dot Product of Two Sparse Vectors](https://leetcode.com/problems/dot-product-of-two-sparse-vectors/)**

Solution I - Simple, Not suitable for interview

Also hashmap is not suitable here, because hash collision will be a huge problem when the data size is very big, like billions.

```
class SparseVector {
public:
    vector<int> originList;
    SparseVector(vector<int> &nums) {
        for(int i = 0;i < nums.size();i++) {
            originList.push_back(nums[i]);
        }
    }
    
    // Return the dotProduct of two sparse vectors
    int dotProduct(SparseVector& vec) {
        int result = 0;
        for(int i = 0;i < vec.originList.size();i++) {
            result += originList[i] * vec.originList[i];
        }
        return result;
    }
};

// Your SparseVector object will be instantiated and called as such:
// SparseVector v1(nums1);
// SparseVector v2(nums2);
// int ans = v1.dotProduct(v2);
```

Solution II - Deal with sparse condition and use binary search to find index

```
class SparseVector {
public:
    vector<pair<int,int>> originList;
    SparseVector(vector<int> &nums) {
        for(int i = 0;i < nums.size();i++) {
            if(nums[i] == 0)continue;
            originList.push_back({i, nums[i]});
        }
    }
    
    // Return the dotProduct of two sparse vectors
    int dotProduct(SparseVector& vec) {
        int result = 0;
        for(int i = 0;i < vec.originList.size();i++) {
            result += vec.originList[i].second * binarySearch(vec.originList[i].first);
        }
        return result;
    }
    
    int binarySearch(int index) {
        int left = 0, right = originList.size() - 1;
        while(left <= right) {
            int middle = left + (right - left)/2;
            if(originList[middle].first == index)return originList[middle].second;
            else if(originList[middle].first < index)left = middle + 1;
            else right = middle - 1;
        }
        return 0;
    }
};
```

Solution III - Two index pointers

```
class SparseVector {
public:
    vector<pair<int,int>> originList;
    SparseVector(vector<int> &nums) {
        for (int i = 0;i < nums.size();i++) {
            if(nums[i] == 0)continue;
            originList.push_back({i, nums[i]});
        }
    }
    
    // Return the dotProduct of two sparse vectors
    int dotProduct(SparseVector& vec) {
        int result = 0;
        int p1 = 0, p2 = 0;
        while(p1 < originList.size() && p2 < vec.originList.size()) {
            if(originList[p1].first == vec.originList[p2].first) {
                result += originList[p1].second * vec.originList[p2].second;
                p1++;
                p2++;
            } else if (originList[p1].first < vec.originList[p2].first) {
                p1++;
            } else {
                p2++;
            }
        }
        return result;
    }
};
```

**[42. Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/)**

Solution I - left, right view, 相当于阳光从左右两侧分别照过来，形成有阴影的山。

```
class Solution {
public:
    int trap(vector<int>& height) {
        if(!height.size())
            return 0;
        int result = 0;
        int size = height.size();
        vector<int> left_max(size), right_max(size);
        left_max[0] = height[0];
        for (int i = 1; i < size; i++) {
            left_max[i] = max(height[i], left_max[i - 1]);
        }
        right_max[size - 1] = height[size - 1];
        for (int i = size - 2; i >= 0; i--) {
            right_max[i] = max(height[i], right_max[i + 1]);
        }
        for (int i = 1; i < size; i++) {
            result += min(left_max[i], right_max[i]) - height[i];
        }
        return result;
    }
};
```

Solution II - 使用单调递减栈，因为递增栈无法储水。

```
class Solution {
public:
    int trap(vector<int>& height) {
        int result = 0, current = 0;
        stack<int> st;
        for (int current = 0; current < height.size(); current++) {
            while (!st.empty() && height[current] >= height[st.top()]) {
                int top = st.top(); // 湖底高度
                st.pop();
                if (st.empty())
                    break; // 没有了左边墙壁无法储水
                int distance = current - st.top() - 1;
                int bounded_height = min(height[current], height[st.top()]) - height[top];
                result += distance * bounded_height;
            }
            st.push(current);
        }
        return result;
    }
};
```
