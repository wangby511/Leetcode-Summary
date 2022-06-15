# Lowest Common Ancestor

CREATED 2022/06/07 UPDATED 2022/06/13

According to the definition of LCA on Wikipedia: "The lowest common ancestor is defined between two nodes p and q as the lowest node in T that has both p and q as descendants (where we allow a node to be a descendant of itself)."

**[235. Lowest Common Ancestor of a Binary Search Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)**

Binary search tree case:

```
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */

class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        TreeNode* result = root;
        while(root) {
            // if(p->val == root->val || q->val == root->val) return root;
            // if(p->val < root->val && q->val > root->val) return root;
            // if(p->val > root->val && q->val < root->val) return root;
            
            if(p->val < root->val && q->val < root->val) root = root->left;
            else if(p->val > root->val && q->val > root->val) root = root->right;
            else return root;
        }
        return NULL;
    }
};
```

**[236. Lowest Common Ancestor of a Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/)**

Normal binary tree case:

```
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(!root || root == p || root == q)return root;
        TreeNode* left = lowestCommonAncestor(root->left, p, q);
        TreeNode* right = lowestCommonAncestor(root->right, p, q);
        if(left && right)return root;
        else if(!left && right)return right;
        else if(left && !right)return left;
        return NULL;
    }
};
```

**[1644. Lowest Common Ancestor of a Binary Tree II](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree-ii/)**

Node p or q could not exist in binary tree:

```
class Solution {
public:
    TreeNode* findNode(TreeNode* root, TreeNode* p) {
        if(!root || root == p)return root;
        TreeNode* left = findNode(root->left, p);
        TreeNode* right = findNode(root->right, p);
        if(left || right)return left?left:right;
        return NULL;
    }
    TreeNode* helper(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(!root || root == p || root == q)return root;
        TreeNode* left = helper(root->left, p, q);
        TreeNode* right = helper(root->right, p, q);
        if(left && right)return root;
        else if(!left && right)return right;
        else if(left && !right)return left;
        return NULL;
    }
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(!findNode(root, p) || !findNode(root, q))return NULL;
        return helper(root, p, q);
    }
};
```

```
class Solution {
public:
    int counter = 0;
    TreeNode* helper(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(!root)return NULL;
        TreeNode* left = helper(root->left, p, q);
        TreeNode* right = helper(root->right, p, q);
        // 需要改成post order来强制遍历所有树的节点
        if(root == p || root == q) {
            counter++;
            return root;   
        }
        if(left && right)return root;
        else if(!left && right)return right;
        else if(left && !right)return left;
        return NULL;
    }
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        TreeNode* result = helper(root, p, q);
        return counter == 2 ? result : NULL;
    }
};
```

**[1650. Lowest Common Ancestor of a Binary Tree III](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree-iii/)**

Simplied case of providing parent node pointer:

```
class Solution {
public:
    Node* lowestCommonAncestor(Node* p, Node * q) {
        vector<Node*> path1, path2;
        while(p) {
            path1.push_back(p);
            p = p->parent;
        }
        while(q) {
            path2.push_back(q);
            q = q->parent;
        }
        Node* result = NULL;
        for(int i = path1.size() - 1, j = path2.size() - 1; i >= 0 && j >= 0;i--,j--) {
            if(path1[i] != path2[j]) {
                return result;
            }
            result = path1[i];
        }
        if(path1.size() <= path2.size())return path1[0];
        return path2[0];
    }
};
```

It is the same idea of Leetcode 160: finding the intersection of two linked lists.

**[160. Intersection of Two Linked Lists](https://leetcode.com/problems/intersection-of-two-linked-lists/)**

```
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        int lengthA = 0;
        int lengthB = 0;
        ListNode *startA = headA;
        ListNode *startB = headB;
        while(startA) {
            lengthA++;
            startA = startA->next; 
        }
        while(startB) {
            lengthB++;
            startB = startB->next; 
        }
        startA = headA;
        startB = headB;
        int diffLength = abs(lengthA - lengthB);
        if(lengthA > lengthB) {
            for(int k = 0; k < diffLength; k++)startA = startA->next;
        } else if(lengthA < lengthB) {
            for(int k = 0; k < diffLength; k++)startB = startB->next;
        }
        while(startA != startB) {
            startA = startA->next; 
            startB = startB->next; 
        }
        return startA;
    }
};
```

**[1676. Lowest Common Ancestor of a Binary Tree IV](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree-iv/)**

Change two nodes p, q to a bunch of nodes:

```
class Solution {
public:
    unordered_set<TreeNode*> st;
    TreeNode* helper(TreeNode* root, vector<TreeNode*> &nodes) {
        // if(!root || root == p || root == q)return root;
        if(!root || st.find(root) != st.end())return root;
        TreeNode* left = helper(root->left, nodes);
        TreeNode* right = helper(root->right, nodes);
        if(left && right)return root;
        else if(!left && right)return right;
        else if(left && !right)return left;
        return NULL;
    }
    TreeNode* lowestCommonAncestor(TreeNode* root, vector<TreeNode*> &nodes) {
        for(TreeNode* target: nodes) st.insert(target);
        return helper(root, nodes);
    }
};
```

**[1123. Lowest Common Ancestor of Deepest Leaves](https://leetcode.com/problems/lowest-common-ancestor-of-deepest-leaves/)**

Find the deepest leaves first in the whole tree, and then find their common ancestor:

```
class Solution {
public:
    int maxdepth = 0;
    vector<vector<TreeNode*>> totalpath;
    void dfs(TreeNode* root, int level, vector<TreeNode*>& path){
        if(!root)return;
        path.push_back(root);
        if(maxdepth < level){
            maxdepth = level;
            totalpath = {path};
        }else if(maxdepth == level){
            totalpath.push_back(path);
        }
        dfs(root->left,level + 1,path);
        dfs(root->right,level + 1,path);
        path.pop_back();
    }
    TreeNode* lcaDeepestLeaves(TreeNode* root) {
        vector<TreeNode*> path;
        dfs(root, 0, path);
        TreeNode* previous = root;
        maxdepth = totalpath[0].size();
        for(int d = 0;d < maxdepth;d++){
            TreeNode* result = totalpath[0][d];
            for(int j = 0;j < totalpath.size();j++){
                if(totalpath[j][d] != result)return previous;
            }
            previous = result;
        }
        return previous;
    }
};
```

Leetcode 1123 is the same as Leetcode 865:

**[865. Smallest Subtree with all the Deepest Nodes](https://leetcode.com/problems/smallest-subtree-with-all-the-deepest-nodes/)**

return the subtree of a node which is the lca of all the deepest nodes:

Solution 1:

```
class Solution {
public:
    unordered_map<TreeNode*, int> distanceToLeaf;
    int recordMaxDepth(TreeNode* root) {
        if(!root)return 0;
        int leftDepth = recordMaxDepth(root->left);
        int rightDepth = recordMaxDepth(root->right);
        return distanceToLeaf[root] = 1 + max(leftDepth, rightDepth);
    }
    TreeNode* subtreeWithAllDeepest(TreeNode* root) {
        recordMaxDepth(root);
        TreeNode* result = root;
        while(root) {
            int l = distanceToLeaf[root->left], r = distanceToLeaf[root->right];
            if(l == r)return root;
            else if(l > r)root = root->left;
            else root = root->right;
        }
        return NULL;
    }
};
```

Solution 2:

```
class Solution {
public:
    TreeNode* result = NULL;
    int maxDepth = 0;
    int postOrder(TreeNode* root, int level) {
        if(!root)return level;
        int leftDepth = postOrder(root->left, level + 1);
        int rightDepth = postOrder(root->right, level + 1);
        
        if(leftDepth == rightDepth) {
            if(leftDepth >= maxDepth) {
                maxDepth = leftDepth;
                result = root;
            }
        }
        return max(leftDepth, rightDepth);
    }
    TreeNode* subtreeWithAllDeepest(TreeNode* root) {
        postOrder(root, 0);
        return result;
    }
};
```

## Reference

[1] [树的基础算法(四) -- LCA](https://www.youtube.com/watch?v=UQJ5lLTeqlA)
