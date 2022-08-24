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
