# LeetCode Contest 291

Finished in 2022-04-30

Rank 744 / 19223

Score 18

Total Finish Time 0:44:19 + 0:05:00 x 2 = 0:54:19

**[2259. Remove Digit From Number to Maximize Result](https://leetcode.com/contest/weekly-contest-291/problems/remove-digit-from-number-to-maximize-result/)**

```
class Solution {
public:
    string removeDigit(string number, char digit) {
        int length = number.size();
        string result = "";
        for(int i = 0;i < length;i++) {
            if(number[i] != digit)continue;
            string newString = number.substr(0, i) + number.substr(i + 1);
            if(newString > result) result = newString;
        }
        return result;
    }
};
```

**[2260. Minimum Consecutive Cards to Pick Up](https://leetcode.com/contest/weekly-contest-291/problems/minimum-consecutive-cards-to-pick-up/)**

```
class Solution {
public:
    int minimumCardPickup(vector<int>& cards) {
        int length = cards.size();
        unordered_map<int,vector<int>> index;
        int result = length + 1;
        for(int i = 0;i < length;i++) {
            index[cards[i]].push_back(i);
        }
        for(auto it: index) {
            int num = it.first;
            int prev = -1;
            for(int j : it.second) {
                if(prev != -1) {
                    result = min(result, j - prev + 1);
                }
                prev = j;
            }
        }
        return (result == length + 1)? -1: result;
        
    }
};
```

**[2261. K Divisible Elements Subarrays](https://leetcode.com/contest/weekly-contest-291/problems/k-divisible-elements-subarrays/)**

```
class Solution {
public:
    int countDistinct(vector<int>& nums, int k, int p) {
        int length = nums.size();
        int begin = 0;
        int total = 0;
        unordered_map<string,int> cnt;
        for(int i = 0; i < length;i++) {
            if(nums[i] % p == 0)total++;
            while(total > k && begin <= i) {
                if(nums[begin++] % p == 0) {
                    total--;
                }
            }
            string temp = "";
            for(int j = i;j >= begin;j--) {
                temp = to_string(nums[j]) + "," + temp;
                cnt[temp]++;
            }

        }
        return cnt.size();
    }
};
```

**[2262. Total Appeal of A String](https://leetcode.com/contest/weekly-contest-291/problems/total-appeal-of-a-string/)**

```
class Solution {
public:
    long long appealSum(string s) {
        int length = s.size();
        unordered_map<char, vector<int>> index;
        for(int i = 0;i < length;i++) {
            index[s[i]].push_back(i);
        }
        long long result = 0;
        for(auto &[character, indexArray]: index) {
            int size = indexArray.size();
            indexArray.push_back(length);
            for(int j = 0;j < size;j++) {
                long long left = indexArray[j] - 0 + 1;
                long long right = indexArray[j + 1] - 1 - indexArray[j] + 1;
                result += left * right;
            }
        }
        return result;
    }
};
```
