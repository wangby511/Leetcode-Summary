# Binary Search

Created 2022/05/04

General case: Find the first item which satisfies some condition.

通用情况: 在一个排好序的序列中，找到第一个满足条件的项，即左半边是不满足的选项，右半边是满足的选项。[不满足, 不满足, ... , 不满足, ->**满足**<-, ..., 满足, 满足]。例子：在一个排好序（从小到大）的数组中，找到第一个大于等于target的数。

## Upper Bound & Lower Bound

std::lower_bound - returns iterator to first element in the given range which is **EqualTo or Greater** than value.

std::upper_bound - returns iterator to first element in the given range which is **Strictly Greater** than value.

lower_bound 例子：在一个排好序（从小到大）的数组中，找到第一个**大于等于**target的数的索引。

upper_bound 例子：在一个排好序（从小到大）的数组中，找到第一个**大于**target的数的索引。

### Cpp References

lower_bound - https://en.cppreference.com/w/cpp/algorithm/lower_bound

upper_bound - https://en.cppreference.com/w/cpp/algorithm/upper_bound

## Template

### Find a specific item exist or not

查询特定的索引存在与否

**[704. Binary Search](https://leetcode.com/problems/binary-search/)**

```
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int n = nums.size();
        int left = 0, right = n - 1;
        while (left <= right) {
            int mid = (left + right)/2;
            if (nums[mid] == target)return mid;
            else if (nums[mid] < target) {
                left = mid + 1;
            }else {
                right = mid - 1;
            }
        }
        return -1;
    }
};
```

### Find the first item beginning in satisfying some condition - 找到第一个满足某个条件的选项

[不满足, 不满足, ... , 不满足, ->**满足**<-, ..., 满足, 满足]

当用int middle = left + (right - left)/2;时，二分搜索的一个方向必须保证left = middle + 1;，否则造成死循环。举例， 8 = 8 + (9 - 8)/2, 8不满足条件，9满足，但如果左边界设置成8会造成死循环forever loop。

要找出第一个满足条件(例如此题中例子第一个坏的版本编号)的索引，所以当中间值索引middle不满足条件时，我们把左边界设置成middle + 1，因为middle这个索引不满足条件(是好的版本)所以不予考虑了。

反之，当中间值索引middle满足条件时，我们把右边界设置成middle，因为middle这个可能就是我们本身最后要找的答案（第一个满足条件的），所以往左二分搜索时必须不能忽略middle本身，不同于上述往右二分搜索的情况。

**[35. Search Insert Position](https://leetcode.com/problems/search-insert-position/)**

即数组中**找到第一个**大于等于target**的位置**。

```
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        int left = 0, right = nums.size();
        while(left < right) {
            int mid = left + (right - left)/2;
            if(nums[mid] < target) {
                left = mid + 1;
            }else{
                right = mid;
            }
        }
        return left;
    }
};
```

**[278. First Bad Version](https://leetcode.com/problems/first-bad-version/)**

即**找到第一个**出现bad version**的位置**。

```
// Forward declaration of isBadVersion API.
bool isBadVersion(int version);

class Solution {
public:
    int firstBadVersion(int n) {
        int left = 0, right = n;
        while (left < right) {
            int middle = left + (right - left)/2;
            if (!isBadVersion(middle))left = middle + 1;
            else right = middle;
        }
        return left;
    }
};
```

通用模版如下，右边界初始定义为arr.size()，而不是arr.size() - 1，因为有边界case例如本次序列全部**不**满足条件。我们返回arr.size()，表示没有找到该项。

```
class Solution {
public:
    int findFirstFitItem(...arr, ) {
        int left = 0, right = arr.size();
        while(left < right) {
            int middle = left + (right - left)/2;
            if (不满足条件(mid))left = middle + 1;
            else right = middle;
        }
        return left;
    }
};
```

### Find the last item satisfying some condition - 找到最后一个满足某个条件的选项

[满足, 满足, ... , ->**满足**<-, 不满足, ..., 不满足, 不满足]

如果我们想找一个按一定规则排好序的序列中，**最后一个**满足某个条件的选项，则最后答案索引需要减去一，即left - 1。因为按上述条件，首先我们从代码层面得找到是第一个**不满足**条件的索引，然后它前面的就是最后一个满足条件的选项。

以下右边界初始定义为arr.size()，而不是arr.size() - 1，因为有边界case例如本次序列全部满足条件。我们返回arr.size() - 1，因为最后left也会等于arr.size()。

如果序列全是不满足的条件的，最终答案会返回-1。

```
class Solution {
public:
    int findLastFitItem(...arr, ) {
        int left = 0, right = arr.size();
        while (left < right) {
            int middle = left + (right - left)/2;
            if (满足条件(mid))left = middle + 1;
            else right = middle;
        }
        return left - 1;
    }
};
```

[满足, 满足, ... , ->**满足**<-, 不满足, ..., 不满足, 不满足]

这里还有另一种写法，把middle定义成(right + left + 1)/2;, 这样更新**右**边界值时需要把right赋值为middle - 1,而不是middle本身以免造成死循环。这样最后结果索引值是left。

```
class Solution {
public:
    int findLastFitItem(...arr, ) {
        int left = 0, right = arr.size();
        while(left < right) {
            int middle = (right + left + 1)/2;
            if(满足条件(mid))left = middle;
            else right = middle - 1;
        }
        return left;
    }
};
```

## Examples

**[528. Random Pick with Weight](https://leetcode.com/problems/random-pick-with-weight/)**

Use the pre-sum array to indicate the accumulated probability. Then use rand() to do binary search to find which range it falls in.

```
class Solution {
public:
    vector<int> preSum;
    Solution(vector<int>& w) {
        int sum = 0;
        for (int weight: w) {
            sum += weight;
            preSum.push_back(sum);
        }
    }
    // 1,4
    // 0, 1,2,3 
    
    int pickIndex() {
        int left = 0, right = preSum.size();
        int target = rand() % preSum.back();
        while (left < right) {
            int middle = left + (right - left)/2;
            if (preSum[middle] <= target)left = middle + 1;
            else right = middle;
        }
        return left;
    }
};
```

**[378. Kth Smallest Element in a Sorted Matrix](https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix/)**

Find the kth smallest number in a matrix where each of the rows and columns is sorted in ascending order.

Given a number, find the index position of its order in the whole matrix. Use binary search method to find which number fits that given index.

```
class Solution {
public:
    int findSmallestNumber(vector<vector<int>>& matrix, int target) {
        int k = 0;
        int n = matrix.size();
        int m = matrix[0].size();
        for(int i = 0;i < n;i++) {
            for(int j = 0;j < m;j++) {
                if(matrix[i][j] <= target)k++;
                else break;
            }
        }
        return k;
    }
    int kthSmallest(vector<vector<int>>& matrix, int k) {
        int n = matrix.size();
        int m = matrix[0].size();
        int left = matrix[0][0], right = matrix[n - 1][m - 1];
        while(left < right) {
            int middle = left + (right - left)/2;
            int kth = findSmallestNumber(matrix, middle);
            if(kth < k) {
                left = middle + 1;
            }else {
                right = middle;
            }
        }
        return left;
    }
};
```

## Reference

[1] <https://leetcode.com/discuss/general-discussion/786126/python-powerful-ultimate-binary-search-template-solved-many-problems>
