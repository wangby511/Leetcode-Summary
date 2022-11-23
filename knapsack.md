# Knapsack

CREATED 2022-07-15

背包问题

## General Solution Template

Capacity of Knapsack: W (given)
Objective: Maximize profit.

If the same item can be used only once.
```
int dp[W + 1] = {0};
for (int i=0; i<n; ++i)
    for (int j=W; j>=weight[i]; --j)
        dp[j] = max(dp[j], value[i] + dp[j - weight[i]]);
return dp[W];
```

// if the same item can be used multiple times, change the inner for-loop traverse direction to increasing order
```
int dp[W + 1] = {0};
for (int i=0; i<n; ++i)
    for (int j=weight[i]; j <= W; ++j)
        dp[j] = max(dp[j], value[i] + dp[j - weight[i]]);
return dp[W];
```

Python approach:

```
def backpack(w: int, weight: List[int], values: List[int]):
    dp = [0] * (w + 1)
    n = len(weight)
    for i in range(n):
        for j in range(w, weight[i] - 1, -1):
            dp[j] = max(dp[j], values[i] + dp[j - weight[i]])
    return dp[w]
```

## Unbounded 0/1 Knapsack problems

Especially coin change problems. Below is the most common question.

**[322. Coin Change](https://leetcode.com/problems/coin-change/)**

We can use infinite number of each kind of coin, so we use increasing order for the loop of *for(int j = coin;j <= amount;j++)*.

Return **the fewest number of coins** that you need to make up that amount.

```
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        const int LIMIT = 1e9;
        vector<int> dp(amount + 1, LIMIT);
        dp[0] = 0;
        for(int coin : coins) {
            for(int j = coin; j <= amount; j++) {
                dp[j] = min(dp[j - coin] + 1, dp[j]);
            }
        }
        return dp[amount] == LIMIT ? -1 : dp[amount];
    }
};
```

**[518. Coin Change 2](https://leetcode.com/problems/coin-change-2/)**

Return **the number of combinations** that make up that amount.

```
class Solution {
public:
    int change(int amount, vector<int>& coins) {
        vector<int> dp(amount + 1, 0);
        dp[0] = 1;
        for(int coin : coins) { 
            // Don't swap the order of these 2 for-loops, otherwise we will count duplicate combinations.
            for(int j = coin; j <= amount; j++) {
                dp[j] += dp[j - coin];
            }
        }
        return dp[amount];
    }
};
```

**[377. Combination Sum IV](https://leetcode.com/problems/combination-sum-iv/)**

```
Example:

Input: nums = [1,2,3], target = 4

Output: 7

Explanation:
The possible combination ways are:
(1, 1, 1, 1)
(1, 1, 2)
(1, 2, 1)
(1, 3)
(2, 1, 1)
(2, 2)
(3, 1)
Note that different sequences are counted as different combinations.
```

This time we swap the two loop logics.

9223372036854775807 = 2 ^ 63 - 1. for 9223372036854775808, we use unsigned int type instead of long long.

```
class Solution {
public:
    int combinationSum4(vector<int>& nums, int target) {
        vector<unsigned int> dp(target + 1, 0);
        dp[0] = 1;
        for(int j = 1; j <= target; j++){
            for(int x: nums){
                if(j - x >= 0) dp[j] += dp[j - x];
            }
        }
        return dp[target];
    }
};
```

## Bounded 0/1 Knapsack problems

背包limit中选取value最大值问题

<https://www.testgears.com/#/searchQuestions/public/description/54542787930418317136783455968708289780>

```
Given a budget as double and item values as double array and calories in array as integers. Find the max number of calories for the given budget.

Example Input:

price = {1.25,1.0,0.75}

calories = {100,70,80}

budget = 3.70

Max calories = 340

Off all the combinations. $.75 times 3 is $2.25 and calories are 240

Now left with $1.45, so to maximize, take $1.25, the calories is 100

and add 240 + 100 = 340
```

Use general template and translate the unit from cent to integer before doing dynamic programming.

```
using namespace std;
int calculateMaxCalories(vector<double>& prices, vector<int>& calories, double budget) {
    vector<int> pricesInteger;
    for (double p: prices) {
        pricesInteger.push_back(int(p * 100));
    }
    int budgetInteger = int(budget * 100);
    vector<int> dp(budgetInteger + 1, 0);
    for (int i = 0;i < pricesInteger.size();i++) {
        int price = pricesInteger[i];
        int cal = calories[i];
        for (int j = price;j <= budgetInteger;j++) {
            dp[j] = max(dp[j], cal + dp[j - price]);
        }
    }
    return *max_element(dp.begin(), dp.end());
}

int main() {
    vector<double> prices = {1.25, 1.0, 0.75};
    vector<int> calories = {100, 70, 80};
    double budget = 3.70;
    cout<<calculateMaxCalories(prices, calories, budget)<<endl; // 340
    return 0;
}
```

## Partition the array into *Equal Sum Partition*

**[416. Partition Equal Subset Sum](https://leetcode.com/problems/partition-equal-subset-sum/)**

```
class Solution {
public:
    bool canPartition(vector<int>& nums) {
        int sum = 0;
        for(int x: nums)sum += x;
        if(sum % 2)return false;
        vector<bool> dp(sum/2 + 1, false);
        dp[0] = true;
        for (int x: nums) {
            for (int j = sum/2;j >= x;j--) {
                dp[j] = dp[j] || dp[j - x];
            }
        }
        return dp[sum/2];
        // TIME O(len(nums) * sum/2)
        // SPACE O(sum/2)    
    }
};
```

**[1049. Last Stone Weight II](https://leetcode.com/problems/last-stone-weight-ii/)**

Think of the final answer as a sum of weights with + or - sign symbols in front of each weight.

Use dynamic programming: for every possible sum with N stones, those sums +x or -x is possible.

```
class Solution {
public:
    int lastStoneWeightII(vector<int>& stones) {
        int sum = 0;
        for(int x: stones)sum += x;
        vector<bool> dp(1 + sum, false);
        dp[0] = true;
        for(int stone: stones) {
            for(int j = sum; j >= stone;j--) {
                dp[j] = dp[j] || dp[j - stone];
            }
        }
        int result = sum;
        for(int j = sum; j >= 0;j--) {
            if(dp[j]) {
                result = min(result, abs((sum - j) - j));
            }
        }
        return result;
    }
};
```

**[494. Target Sum](https://leetcode.com/problems/target-sum/)**

X is the sum of some numbers in the array, Y is the sum of the other ones.

X + Y = sum; X - Y = target; assume X >= Y;

so, X = (sum + target) / 2;

Objective: dp[i] : count the number of ways to achieve the target i

```
class Solution {
public:
    int findTargetSumWays(vector<int>& nums, int target) {
        int sum = 0;
        for(int x: nums)sum += x;
        if(sum < target)return 0;
        if((sum + target) % 2)return 0;
        int X = (sum + target) / 2;
        
        vector<int> dp(sum + 1, 0);
        dp[0] = 1;
        for(int x: nums) {
            for(int j = sum;j >= x;j--) {
                dp[j] += dp[j - x];
            }
        }
        return (X < 0)? 0 : dp[X];
    }
};
```

**[474. Ones and Zeroes](https://leetcode.com/problems/ones-and-zeroes/)**

Return the size of the largest subset of strs such that there are at most m 0's and n 1's in the subset.

Use 2D dynamic programming.

```
class Solution {
public:
    int findMaxForm(vector<string>& strs, int m, int n) {
        vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));

         for (string s : strs) {
             int numZeroes = 0, numOnes = 0;
             for (char c : s) {
                 if (c == '0') numZeroes++;
                 else numOnes++;
             }
             for (int i = m; i >= numZeroes; i--) {
                 for (int j = n; j >= numOnes; j--) {
                     dp[i][j] = max(dp[i][j], dp[i - numZeroes][j - numOnes] + 1);
                 }
             }
         }
        return dp[m][n];
    }
};
```

## Reference

[1] <https://leetcode.com/discuss/study-guide/1152328/01-Knapsack-Problem-and-Dynamic-Programming>

[2] <https://leetcode.com/discuss/study-guide/1200320/Thief-with-a-knapsack-a-series-of-crimes>
