## Longest Increasing Subsequence

Created in 2021-12-19.

### 300. Longest Increasing Subsequence

https://leetcode.com/problems/longest-increasing-subsequence/

Solution One

```
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size();
        vector<int> dp(n, 1);
        int result = 1;
        for(int i = 0;i < n;i++){
            for(int j = 0;j < i;j++){
                if(nums[j] < nums[i]){
                    dp[i] = max(dp[i], dp[j] + 1);
                }
            }
            result = max(result, dp[i]);
        }
        return result;
    }
};
```

Time Complexity - O(n^2)

Space Complexity - O(n)

Solution Two

```
class Solution {
public:
    int _lower_bound(vector<int>& tails,int x) {
        int l = 0,r = tails.size();
        while(l < r) {
            int m = (l + r)/2;
            if(tails[m] < x)l = m + 1;
            else r = m;
        }
        return l;
    }
    int lengthOfLIS(vector<int>& nums) {
        vector<int> tails;
        for(int x: nums) {
            auto it = lower_bound(tails.begin(), tails.end(), x);
            if(it == tails.end())tails.push_back(x);
            else *it = x;
        }
        return tails.size();
    }
};
```

Time Complexity - O(nlogn) We use binary search for n numbers.

Space Complexity - O(n) We use an array to store the LIS.

The length of tails will be equal to the length of the longest subsequence we have found to that point.
The last element of tails will be the last element of that subsequence. (However, the earlier elements may no longer be part of that sequence -- tails is not actually the subsequence itself.)

At the end, the length of tails will be our solution.

Tails will be sorted at all times. Each new element is inserted into tails, replacing the smallest element in tails that is not smaller than it (which we can find with a binary search). If that element is larger than the last element of tails, then we extend tails by one -- maintaining both properties.

Extract LIS by using pair mode to remember previous node's index in original array. (Patience Algorithm)

上述算法中的tails并非所要求的LIS序列具体数字，但它的长度正好等于LIS的长度，且最后一个数字就是LIS的最后一个数字。tails[i]存储的是长度为i+1的所有上升子序列中尾部数值最小的那个值。

```
Tails is an array storing the smallest tail of all increasing subsequences with length i+1 in tails[i].
For example, say we have nums = [4,5,6,3], then all the available increasing subsequences are:

len = 1   :      [4], [5], [6], [3]   => tails[0] = 3
len = 2   :      [4, 5], [5, 6]       => tails[1] = 5
len = 3   :      [4, 5, 6]            => tails[2] = 6
```

接下来讲述如何求具体的LIS中的值。用binary search算法时每次选择tails数组中插入的位置时，插入数字的指针指向前一个位置的数字，如果为空则没有指针。

E.g. [2,7,18,9,4,1]:
前面三个数字依次按顺序插入tails，变成[2,7,18]。
接下来插入9时，9会有一个指针指向7，因为它要插入的位置是18的位置，之后变成[2,7,9]。
同样，再插入4时，4会有一个指针指向2，因为它要插入的位置是7的位置，之后变成[2,4,9]。
再插入1时，因为2前面没有数字，所以1不设置指针。

所以指针情况时7->2, 18->7, 9->7, 4->2。
最后tails的数字是9，所以LIS的最后数字也是9，顺着指针依次往前推，9->7,7->2，则LIS具体就是[2,7,9]了，这和tails的[2,4,9]并不一致。

```
class Solution {
public:
    int _lower_bound(vector<vector<int>>& temp,int x) {
        int l = 0,r = temp.size();
        while(l < r) {
            int m = (l + r)/2;
            if(temp[m][0] < x)l = m + 1;
            else r = m;
        }
        return l;
    }
    int lengthOfLIS(vector<int>& nums) {
        // Analysize LIS 's size
        vector<vector<int>> temp;
        unordered_map<int,int> index;
        for(int i = 0;i < nums.size();i++) {
            int x = nums[i];
            auto it = _lower_bound(temp, x);
            if(it == temp.size()){
                if(temp.size())index[i] = temp.back()[1];
                temp.push_back({x, i});
            }else {
                if(it - 1 >= 0)index[i] = temp[it - 1][1];
                temp[it] = {x, i};
            }
        }

        // Extract LIS
        vector<int> lis = {temp.back()[0]};
        int position = temp.back()[1];
        while(index.count(position)) {
            lis.push_back(nums[index[position]]);
            position = index[position];
        }
        reverse(lis.begin(), lis.end());
        // for(int x:lis)cout<<x<<" ";cout<<endl;
        return temp.size();
    }
};
```

2021/12/18的Leetcode contest 272的最后一题也用到了这个思想，只不过条件稍加改动，并非严格递增，可以相等。把lower_bound换成upper_bound即可。
前者是找出第一个大于等于目标数字的下标，后者是找出第一个严格大于目标数字的下标，前提是已经排好序的数组。

https://leetcode.com/problems/minimum-operations-to-make-the-array-k-increasing/

### Reference

[1] https://www.cs.princeton.edu/courses/archive/spring13/cos423/lectures/LongestIncreasingSubsequence.pdf

[2] https://segmentfault.com/a/1190000003819886

[3] https://leetcode.com/problems/longest-increasing-subsequence/discuss/74824/JavaPython-Binary-search-O(nlogn)-time-with-explanation
