# LeetCode Contest 287

Finished in 2022-04-02

Rank 618 / 19930

Score 18

Total Finish Time 0:48:59 + 0:05:00 x 2 = 0:58:59

https://leetcode.com/contest/weekly-contest-287/ranking

**[2224. Minimum Number of Operations to Convert Time](https://leetcode.com/problems/minimum-number-of-operations-to-convert-time/)**

```
class Solution {
public:
    int convertTime(string current, string correct) {
        int time1 = ((current[0] - '0') * 10 + (current[1] - '0')) * 60 + (current[3] - '0') * 10 + (current[4] - '0');
        int time2 = ((correct[0] - '0') * 10 + (correct[1] - '0')) * 60 + (correct[3] - '0') * 10 + (correct[4] - '0');
        vector<int> diffs = {60, 15, 5, 1};
        int diff = time2 - time1, result = 0;
        for(int eachDiff: diffs) {
            result += diff / eachDiff;
            diff %= eachDiff;
        }
        return result;
    }
};
```

**[2225. Find Players With Zero or One Losses](https://leetcode.com/problems/find-players-with-zero-or-one-losses/)**

```
class Solution {
public:
    vector<vector<int>> findWinners(vector<vector<int>>& matches) {
        vector<int> notLost, lostOnce;
        unordered_map<int,int> lostTimes;
        for(vector<int> & match: matches) {
            lostTimes[match[1]]++;
            lostTimes[match[0]]+=0;
        }
        for(auto it: lostTimes) {
            if(it.second == 0) {
                notLost.push_back(it.first);
            }else if(it.second == 1) {
                lostOnce.push_back(it.first);
            }
        }
        sort(notLost.begin(), notLost.end());
        sort(lostOnce.begin(), lostOnce.end());
        return {notLost, lostOnce};
    }
};
```

**[2226. Maximum Candies Allocated to K Children](https://leetcode.com/problems/maximum-candies-allocated-to-k-children/)**

Binary search

```
class Solution {
public:
    bool check(vector<int>& candies, long long each, long long k) {
        long long total = 0;
        for(int candy: candies) {
            total += candy/each;
        }
        return total >= k;
    }
    int maximumCandies(vector<int>& candies, long long k) {
        long long sum = 0;
        for(int x: candies)sum += x;
        if(sum < k)return 0;
        
        long long left = 1, right = sum + 1;
        while(left < right) {
            long long mid = left + (right - left)/2;
            if(check(candies, mid, k))left = mid + 1;
            else right = mid;
        }
        return left - 1;    
    }
};
```

**[2227. Encrypt and Decrypt Strings](https://leetcode.com/problems/encrypt-and-decrypt-strings/)**

```
class Encrypter {
public:
    unordered_map<char, string> convert;
    unordered_map<string, int> allowedUnConvert;
    Encrypter(vector<char>& keys, vector<string>& values, vector<string>& dictionary) {
        int length = keys.size();
        for(int i = 0;i < length;i++) {
            convert[keys[i]] = values[i];
        }
        for(string d: dictionary) {
            allowedUnConvert[encrypt(d)]++;
        }
    }
    
    string encrypt(string word1) {
        string result = "";
        for(char x: word1) {
            result += convert[x];
        }
        return result;
    }
    
    int decrypt(string word2) {
        return allowedUnConvert[word2];
    }
};

/**
 * Your Encrypter object will be instantiated and called as such:
 * Encrypter* obj = new Encrypter(keys, values, dictionary);
 * string param_1 = obj->encrypt(word1);
 * int param_2 = obj->decrypt(word2);
 */
```
