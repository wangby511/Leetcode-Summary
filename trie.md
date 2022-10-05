# Trie Tree

Created in 2022-04-30

## Trie Tree with Character (Children size 26)

**[208. Implement Trie (Prefix Tree)](https://leetcode.com/problems/implement-trie-prefix-tree/)**

```
struct Node {
    // char value;
    // vector<Node*> children;
    Node* children[26]; 
    bool isWord;
    Node() {
        memset(children, 0, sizeof(children));   
        isWord = false;
    }
};
class Trie {
public:
    Node *root;
    Trie() {
        root = new Node();
    }
    
    void insert(string word) {
        Node *p = root;
        for(char x: word) {
            if(p->children[x - 'a'] == NULL) {
                p->children[x - 'a'] = new Node();
            }
            p = p->children[x - 'a'];
        }
        p -> isWord = true;
    }
    
    bool search(string word) {
        Node *p = root;
        for(char x: word) {
            if(p->children[x - 'a'] == NULL) {
                return false;
            }
            p = p->children[x - 'a'];
        }
        return p->isWord;
    }
    
    bool startsWith(string prefix) {
        Node *p = root;
        for(char x: prefix) {
            if(p->children[x - 'a'] == NULL) {
                return false;
            }
            p = p->children[x - 'a'];
        }
        return true;
    }
};

/**
 * Your Trie object will be instantiated and called as such:
 * Trie* obj = new Trie();
 * obj->insert(word);
 * bool param_2 = obj->search(word);
 * bool param_3 = obj->startsWith(prefix);
 */
 ```

**[212. Word Search II](https://leetcode.com/problems/word-search-ii)**

Use trie tree + DFS to do word searching in a grid. 2021 11 30 Tiktok second round of phone interview.

```
struct Node {
    char value;
    vector<Node*> children;
    bool isWord;
    string word;
    Node() {
        children.resize(26);
        isWord = false;
        word = "";
    }
};
class Solution {
public:
    void insert(Node *root, string word) {
        Node *p = root;
        for(char x:word) {
            if(p->children[x - 'a'] == NULL) {
                p->children[x - 'a'] = new Node();
            }
            p = p->children[x - 'a'];
        }
        p -> isWord = true;
        p -> word = word;
    }
    void dfs(Node *root, vector<vector<char>>& board, int i, int j, vector<string>& result) {
        int n = board.size(), m = board[0].size();
        if(root == NULL || i < 0 || i >= n || j < 0 || j >= m || board[i][j] == ' ')return;
        char x = board[i][j];
        Node *p = root->children[x - 'a'];
        if(p && p->isWord) {
            result.push_back(p->word);
        }
        board[i][j] = ' ';
        dfs(p, board,i + 1,j, result);
        dfs(p, board,i - 1,j, result);
        dfs(p, board,i,j + 1, result);
        dfs(p, board,i,j - 1, result);
        board[i][j] = x;
    }
    vector<string> findWords(vector<vector<char>>& board, vector<string>& words) {
        Node *root = new Node();
        for(string word: words)insert(root, word);
        vector<string> ans;
        int n = board.size(), m = board[0].size();
        for(int i = 0;i < n;i++) {
            for(int j = 0;j < m;j++) {
                dfs(root, board, i, j, ans);
            }
        }
        unordered_set<string> st(ans.begin(), ans.end());
        return vector<string> (st.begin(), st.end());
    }
};
```

## Similar Follow Up - File System

**[1166. Design File System](https://leetcode.com/problems/design-file-system/)**

```
struct FileNode {
    unordered_map<string, FileNode*> children;
    int value;
    FileNode(int x) {
        value = x;
    }
};
class FileSystem {
public:
    FileNode *root;
    FileSystem() {
        root = new FileNode(-1);
    }
    
    bool createPath(string path, int value) {
        FileNode *p = root;
        istringstream pathStream(path);
        vector<string> names;
        string name;
        while(getline(pathStream, name, '/')) {
            if(name != "")names.push_back(name);
        }
        for(int i = 0;i < names.size() - 1;i++) {
            name = names[i];
            if(p->children.count(name) == 0)return false;
            p = p->children[name];
        }
        if(p->children.count(names.back()) != 0)return false;
        p->children[names.back()] = new FileNode(value);
        return true;
    }
    
    int get(string path) {
        FileNode *p = root;
        istringstream pathStream(path);
        string name;
        while(getline(pathStream, name, '/')) {
            if(name == "")continue;
            if(p->children.count(name) == 0)return -1;
            p = p->children[name];
        }
        return p->value;
    }
};

/**
 * Your FileSystem object will be instantiated and called as such:
 * FileSystem* obj = new FileSystem();
 * bool param_1 = obj->createPath(path,value);
 * int param_2 = obj->get(path);
 */
 ```
