# Tree

CREATED 2022/07/26

## Traverse Order

### Pre-Order

```
1. Visit the root node.
2. Traverse the left subtree, i.e., call Preorder(left-subtree)
3. Traverse the right subtree, i.e., call Preorder(right-subtree) 
```

```
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        stack<TreeNode*> st;
        vector<int> result;
        if(root)st.push(root);
        while(st.size() > 0) {
            TreeNode* current = st.top();
            st.pop();
            result.push_back(current->val);
            if(current->right)st.push(current->right);
            if(current->left)st.push(current->left);
        }
        return result;
    }
};
```

### In-Order

```
1. Traverse the left subtree, i.e., call Inorder(left-subtree)
2. Visit the root node.
3. Traverse the right subtree, i.e., call Inorder(right-subtree)

Especially, in the case of binary search trees (BST), Inorder traversal gives nodes in non-decreasing order.
```

```
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        stack<TreeNode*> st;
        vector<int> result;
        while(root || !st.empty()){
            while(root){
                st.push(root);
                root = root->left;
            }
            root = st.top();
            st.pop();
            result.push_back(root->val);
            root = root->right; 
        }
        return result;
    }
};
```
