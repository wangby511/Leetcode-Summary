# Trie Tree

Created in 2022-04-30

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