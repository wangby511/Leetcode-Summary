# DoorDash VO 2022-09-22

Revisited 2022-11-09

## System Design Round

10:00AM (PDT) - 11:15AM (PDT)

Donation System - See https://github.com/wangby511/System-Design-Architecture-and-Infra/blob/main/design-donation.md for details.

## Behavior Questions Round

11:30AM (PDT) - 12:15PM (PDT)

## Coding Round One - Find Smallest Big Peak Every Time

12:45PM (PDT) - 1:45PM (PDT)

```
给一个数组，[2,5,4,9,1] 找到每一次最小的peak，就是比左右都大的数，然后输出
比如上例，输出就是5, 9, 4, 2, 1
```
From https://www.1point3acres.com/bbs/thread-922884-1-1.html

A big peak is defined as: the number is bigger than its both neighbor numbers.

My solution: Use a double linked list (borrowed the idea from **LRU**) with a priority queue. At the beginning we construct a double linked list with the given array and first find those peaks(bigger than its two neighbor nodes), put them into the priority queue - min heap.

Then when traversing each item, we pop an item from the queue and that node is the smallest peak number each time. At the same time, we check the two neighbors of that newly-chosen node
to see if they two satisfy the requirement of big peak. If satisfied, the neighbor node would be put into the priority queue.

TIME O(NlogN), SPACE O(N)

```
using namespace std;

struct Node {
    int val;
    Node* prev;
    Node* next;
    Node (int k) {
        val = k; prev = NULL; next = NULL;
    }
};

pair<Node*, Node*> constructDoubleLinkedList(const vector<int> array) {
    Node* start = new Node(-1);
    Node* end = new Node(-1);
    Node* previous = start;
    for (int x: array) {
        Node* current = new Node(x);
        previous->next = current;
        current->prev = previous;
        previous = current;
    }
    previous->next = end;
    end->prev = previous;
    return {start, end};
}

void removeNodeFromDoubleLinkedList(Node* current) {
    Node* previous = current->prev;
    Node* nextOne = current->next;
    previous->next = nextOne;
    nextOne->prev = previous;
    delete current;
}

bool isBigPeak(Node* current) {
    return current->prev != NULL && current->prev->val < current->val &&
          current->next != NULL && current->next->val < current->val;
}

vector<Node*> findBigPeak(Node* start) {
    vector<Node*> result;
    Node* current = start;
    while (current) {
        if(isBigPeak(current))result.push_back(current);
        current = current->next;
    }
    return result;
}

vector<int> testFindBigPeakEveryTime(const vector<int> array) {
    vector<int> result;
    pair<Node*, Node*> doubleLinkedListPair = constructDoubleLinkedList(array);
    Node* start = doubleLinkedListPair.first;
    Node* end = doubleLinkedListPair.second;

    // define priority queue (min-heap comparing Node's value)
    struct comp {
        bool operator()(const Node* a, const Node* b) {
            return a->val > b->val;
        }
    };
    priority_queue<Node*, vector<Node*>, comp> pq;
    
    // find all the big peaks at the first round
    vector<Node*> smallest = findBigPeak(start);
    for (Node* t: smallest) {
        pq.push(t);
    }

    for (int i = 0; i < array.size(); i++) {
        Node* current = pq.top();
        pq.pop();
        result.push_back(current->val);
        Node* left = current->prev;
        Node* right = current->next;

        // Remove the node from the double linked list
        removeNodeFromDoubleLinkedList(current);

        // Check if its two neighbor nodes satisfy the big peak after the current node has been removed
        if (isBigPeak(left)) pq.push(left);
        if (isBigPeak(right)) pq.push(right);
    }
    return result;
}

int main() {
    vector<int> array = {2, 5, 4, 9, 1};
    vector<int> result = testFindBigPeakEveryTime(array);
    for(int x: result) {
        cout<<x<<" ";
    }
    cout<<endl;

    // 5 9 4 2 1 
    
    return 0;
}
```

## Coding Round Two - Design File System

1:45PM (PDT) - 2:45PM (PDT)

See https://leetcode.com/problems/design-file-system and https://github.com/wangby511/Leetcode-Summary/blob/main/trie.md#similar-follow-up---file-system for details
