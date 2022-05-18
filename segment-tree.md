# Segment Tree

我希望面试永远用不到它。 —— In memory of 2019/11/18 Google Onsite Interviews (Extra Two Rounds).

**[315. Count of Smaller Numbers After Self](https://leetcode.com/problems/count-of-smaller-numbers-after-self/submissions/)**

```
struct SegmentTreeNode {
    int start, end;
    SegmentTreeNode* left;
    SegmentTreeNode* right;
    int count;
    SegmentTreeNode(int a, int b):start(a),end(b),count(0),left(NULL),right(NULL){}
};
class Solution {
public:
    SegmentTreeNode* buildTree(vector<int>& nums, int start, int end) {
        if(start > end) return NULL;
        SegmentTreeNode* root = new SegmentTreeNode(nums[start], nums[end]);
        if(start == end) {
            return root;
        }
        int mid = start + (end - start) / 2;
        root->left = buildTree(nums, start, mid);
        root->right = buildTree(nums, mid+1, end);
        return root;
    }
    void plusOne(SegmentTreeNode *root, int value) {
        if (root == NULL || value < root->start || value > root->end) return;
        root->count += 1;
        plusOne(root->left, value);
        plusOne(root->right, value);
    }
    
    int query(SegmentTreeNode *root, int min, int max) {
        if (root == NULL || max < root->start || min > root->end) return 0;
        if (min <= root->start && max >= root->end) return root->count;
        return query(root->left, min, max) + query(root->right, min, max);
    }
    
    vector<int> countSmaller(vector<int>& nums) {
        int length = nums.size();
        map<int,int> cnt;
        for(int x:nums)cnt[x]++;
                
        // 注意建树时去除重复元素 & 排序
        vector<int> dedup;
        for(auto it:cnt) dedup.push_back(it.first);
        
        SegmentTreeNode* root = buildTree(dedup, 0, dedup.size() - 1);
        vector<int> result(length, 0);

        for(int i = length - 1; i >= 0; i--){
            result[i] = query(root, INT_MIN, nums[i] - 1);
            plusOne(root, nums[i]);
        }
        return result;
    }
};
```
