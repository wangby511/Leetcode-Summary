# LRU & LFU

## LRU

**[146. LRU Cache](https://leetcode.com/problems/lru-cache/)**

```
struct node{
    int key;
    int value;
    node* prev;
    node* next;
    node(int u, int v){
        key = u;
        value = v;
        prev = NULL;
        next = NULL;
    }
};
class LRUCache {
private:
    node* start;
    node* end;
    unordered_map<int, node*> dict;
    int size;
    int cap;
    
public:
    LRUCache(int capacity) {
        start = new node(-1,-1);
        end = new node(-1,-1);
        start->next = end;
        end->prev = start;
        size = 0;
        cap = capacity;
    }
    void _delete(node *t){
        node *p = t->prev;
        node *q = t->next;
        p->next = q;
        q->prev = p;
    }
    
    void moveToHead(node *t){
        node *first = start->next;
        start->next = t;t->next = first;
        first->prev = t;t->prev = start;
    }
    
    int get(int key) {
        if(dict.count(key) == 0)return -1;
        node *t = dict[key];
        int ans = t->value;
        _delete(t);
        moveToHead(t);
        return ans;
    }
    
    void put(int key, int value) {
        if(dict.count(key) != 0){
            node *t = dict[key];
            t->value = value;
            _delete(t);
            moveToHead(t);
        } else {
            node *t = new node(key,value);
            moveToHead(t);
            dict[key] = t;
            if(++size > cap){
                node *last = end->prev;
                int k = last->key;
                dict.erase(k);
                _delete(last);
                size--;
            }
        }
    }
    //2019.08.31 AFTERNOON 基本上是bug free
    //2020.04.25
};

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache* obj = new LRUCache(capacity);
 * int param_1 = obj->get(key);
 * obj->put(key,value);
 */
```

**[LFU]()**

```
struct node{
    int key;
    int value;
    int freq;
    node* prev;
    node* next;
    node(int u, int v){
        key = u;
        value = v;
        freq = 1;
        prev = NULL;
        next = NULL;
    }
};
class LFUCache {
    map<int, node*> frequency;
    unordered_map<int, node*> frequencyToEnd;
    unordered_map<int, node*> mapping;
    int size;
    int limit;
public:
    LFUCache(int capacity) {
        size = 0;
        limit = capacity;
    }
    void removeFromList(int oldFreq, node *t) {
        node *p = t->prev;
        node *q = t->next;
        p->next = q;
        q->prev = p;
        if(p->value == -1 && q->value == -1) {
            p->next = q->next = NULL;
            p->prev = q->prev = NULL;
            delete p;
            delete q;
            frequency.erase(oldFreq);
            frequencyToEnd.erase(oldFreq);
        }
    }
    void initialize(int freq) {
        node *start = new node(-1, -1);
        node *end = new node(-1, -1);
        start->next = end;
        end->prev = start;
        frequency[freq] = start;
        frequencyToEnd[freq] = end;
    }
    void insertToNewList(int newFreq, node *t) {
        if(frequency.count(newFreq) == 0) {
            initialize(newFreq);
        }
        node *start = frequency[newFreq];
        node *prevHead = start->next;
        start->next = t;
        t->prev = start;
        t->next = prevHead;
        prevHead->prev = t;
    }
    int get(int key) {
        if(limit == 0 || mapping.count(key) == 0)return -1;
        node* currentNode = mapping[key];
        int value = currentNode->value;
        int oldFreq = currentNode->freq;
        removeFromList(oldFreq, currentNode);
        insertToNewList(oldFreq + 1, currentNode);
        currentNode->freq = oldFreq + 1;
        return value;
    }
    
    void removeLastNode() {
        int leastFrequency = frequency.begin()->first;
        node *t = frequencyToEnd[leastFrequency]->prev;
        removeFromList(leastFrequency, t);
        mapping.erase(t->key);
        --size;
    }
    
    void put(int key, int value) {
        if(limit == 0)return;
        if(mapping.count(key) != 0) {
            node* currentNode = mapping[key];
            currentNode->value = value;
            int oldFreq = currentNode->freq;
            removeFromList(oldFreq, currentNode);
            insertToNewList(oldFreq + 1, currentNode);
            currentNode->freq = oldFreq + 1;
        } else {
            if(size == limit) {
                removeLastNode();
            }
            node* newNode = new node(key, value);
            insertToNewList(1, newNode);
            mapping[key] = newNode;
            size++;
        }
    }
    
    void printAll() {
        for(auto it: frequency) {
            cout<<it.first<<":";
            node *t = it.second;
            while(t) {
                cout<<"("<<t->key<<","<<t->value<<"),";
                t = t->next;
            }
            cout<<endl;
        }
        cout<<endl;
    }
};

/**
 * Your LFUCache object will be instantiated and called as such:
 * LFUCache* obj = new LFUCache(capacity);
 * int param_1 = obj->get(key);
 * obj->put(key,value);
 */
 ```
 