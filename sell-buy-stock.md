## Sell/Buy stock 买卖股票问题总结

Created in 2021-12-11.

### 121. Best Time to Buy and Sell Stock

单次买卖。https://leetcode.com/problems/best-time-to-buy-and-sell-stock/

```
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int profit = 0; 
        int minprice = 1e9;
        for(int p: prices) {
            int prevProfit = profit;
            int prevMinprice = minprice;
            
            profit = max(prevProfit, p - prevMinprice);
            minprice = min(minprice, p);
        }
        return profit;
    }
};
```

```
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int result = 0;
        if(!prices.size())return 0;
        int minn = INT_MAX;
        for(int p: prices) {
            if(p < minn){
                minn = p;
            }else{
                result = max(res, p - minn);
            }
        }
        return result;
    }
};
```

### 122. Best Time to Buy and Sell Stock II

任意买卖。https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/

```
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int profit = 0;
        int balance = -1e9; // Holding a stock
        for(int p: prices) {
            int prevProfit = profit;
            int prevBalance = balance;
            profit = max(prevProfit, prevBalance + p);
            balance = max(prevBalance, prevProfit - p);
        }
        return profit;
    }
};
```

```
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int result = 0;
        int prev = prices[0];
        for(int i = 0;i < prices.size();i++) {
            int current = prices[i];
            result += max(0, current - prev);
            prev = current;
        }
        return result;
    }
};
```

### 714. Best Time to Buy and Sell Stock with Transaction Fee

任意次数买卖，含transaction fee。代码同上。https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/

```
class Solution {
public:
    int maxProfit(vector<int>& prices, int fee) {
        int hold = -1e9;
        int sold = 0;
        for(int p: prices) {
            int prevHold = hold;
            int prevSold = sold;
            hold = max(prevHold, prevSold - p);
            sold = max(prevSold, prevHold + p - fee);
        }
        return sold;
    }
};
```

### 309. Best Time to Buy and Sell Stock with Cooldown

任意买卖，sell之后有cooldown一天限制。https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/

```
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int hold = -prices[0]; // Max profit with stock hold in hand
        int sold = 0; // Max profit with stock sold TODAY
        int cooldown = 0; // Max profit in cool down time (sold yesterday)
        int fee = 0;
        int result = 0;
        for (int i = 1;i < prices.size();i++) {
            int p = prices[i];
            int prevHold = hold;
            int prevSold = sold;
            int prevCooldown = cooldown;
            
            sold = prevHold + p - fee;
            hold = max(prevHold, prevCooldown - p);
            cooldown = max(prevSold, prevCooldown);
            
            result = max(result, sold);
        }
        return result;
    }
};
```

```
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int len = prices.size();
        if(len <= 1) return 0;
  
        int b0 = -prices[0], b1 = b0;
        int s0 = 0, s1 = 0, s2 = 0;

        for(int i = 1; i < len; i++) {
            b0 = max(b1, s2 - prices[i]);
            s0 = max(s1, b1 + prices[i]);
            b1 = b0; 
            s2 = s1; 
            s1 = s0; 
        }
        return s0;
        // DAY -2    DAY -1      TODAY
        //   s2        s1          s0
        //             b1          b0
        // 2021 10 14 THURSDAY NIGHT
    }
};
```