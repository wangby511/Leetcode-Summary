# LeetCode Contest 303

Finished in 2022-07-23

Rank 804 / 24338

Score 18

Total Finish Time 0:47:41 + 0:05:00 x 2 = 0:57:41

https://leetcode.com/contest/weekly-contest-303/ranking

**[2351. First Letter to Appear Twice](https://leetcode.com/problems/first-letter-to-appear-twice/)**

```
class Solution {
public:
    char repeatedCharacter(string s) {
        unordered_map<char, int> cnt;
        for(char x:s) {
            if(++cnt[x] == 2)return x;
        }
        return ' ';
    }
};
```

**[2352. Equal Row and Column Pairs](https://leetcode.com/problems/equal-row-and-column-pairs/)**

```
class Solution {
public:
    int equalPairs(vector<vector<int>>& grid) {
        int n = grid.size();
        int m = grid[0].size();
        unordered_map<string, int> indexRow;
        for(int i = 0;i < n;i++) {
            string row = "";
            for(int x: grid[i]) {
                row += to_string(x) + "|";
            }
            indexRow[row]++;
        }
        int result = 0;
        for(int j = 0;j < m;j++) {
            string column = "";
            for(int i = 0;i < n;i++) {
                column += to_string(grid[i][j]) + "|";
            }
            result += indexRow[column];
        }
        return result;
    }
};
```

**[2353. Design a Food Rating System](https://leetcode.com/problems/design-a-food-rating-system/)**

```
struct foodInfo {
    int rating;
    string food;
    int version;
};
class FoodRatings {
public:
    unordered_map<string, string> index;
    unordered_map<string, int> version;
    struct comp {
        bool operator()(const foodInfo& a, const foodInfo& b){
            if(a.rating == b.rating)return a.food > b.food;
            return a.rating < b.rating;
        }
    };
    unordered_map<string, priority_queue<foodInfo, vector<foodInfo>, comp> > menu;
    FoodRatings(vector<string>& foods, vector<string>& cuisines, vector<int>& ratings) {
        int n = foods.size();
        for(int i = 0;i < n;i++) {
            string food = foods[i];
            string cuisine = cuisines[i];
            index[food] = cuisine;
            menu[cuisine].push({ratings[i], food, ++version[food]});
        }
    }
    
    void changeRating(string food, int newRating) {
        string cuisine = index[food];
        menu[cuisine].push({newRating, food, ++version[food]});
    }
    
    string highestRated(string cuisine) {
        while(menu[cuisine].size()) {
            foodInfo current = menu[cuisine].top();
            string food = current.food;
            if(version[food] != current.version) {
                menu[cuisine].pop();
            }else{
                return food;
            }
        }
        return "";
    }
};

/**
 * Your FoodRatings object will be instantiated and called as such:
 * FoodRatings* obj = new FoodRatings(foods, cuisines, ratings);
 * obj->changeRating(food,newRating);
 * string param_2 = obj->highestRated(cuisine);
 */
```

**[2354. Number of Excellent Pairs](https://leetcode.com/problems/number-of-excellent-pairs/)**

```
class Solution {
public:
    long long countExcellentPairs(vector<int>& nums, int k) {
        unordered_map<int, int> cnt;
        for(int x: nums)cnt[x]++;
        vector<int> arr;
        long long result = 0;
        for(auto it: cnt)arr.push_back(it.first);
        int n = arr.size();
        vector<long long> bits(101, 0);
        for(int i = 0;i < n;i++) {
            int bitCountA = __builtin_popcount(arr[i]);
            for(int j = 100; j >= 0;j--) {
                if(j + bitCountA >= k) {
                    result+=bits[j] * 2;
                }
            }
            bits[bitCountA]++;
            if(bitCountA * 2 >= k) result++;
        }
        return result;
    }
};
```
