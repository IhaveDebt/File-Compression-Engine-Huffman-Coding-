#include <iostream>
#include <queue>
#include <unordered_map>
#include <vector>
#include <fstream>
using namespace std;

struct Node {
    char ch;
    int freq;
    Node *left, *right;
    Node(char c, int f) : ch(c), freq(f), left(nullptr), right(nullptr) {}
};

struct Compare {
    bool operator()(Node* a, Node* b) { return a->freq > b->freq; }
};

void buildCodes(Node* root, string code, unordered_map<char, string>& codes) {
    if (!root) return;
    if (!root->left && !root->right) codes[root->ch] = code;
    buildCodes(root->left, code + "0", codes);
    buildCodes(root->right, code + "1", codes);
}

int main() {
    string text = "hello compression world!";
    unordered_map<char, int> freq;
    for (char c : text) freq[c]++;

    priority_queue<Node*, vector<Node*>, Compare> pq;
    for (auto& p : freq) pq.push(new Node(p.first, p.second));

    while (pq.size() > 1) {
        Node *a = pq.top(); pq.pop();
        Node *b = pq.top(); pq.pop();
        Node *merged = new Node('\0', a->freq + b->freq);
        merged->left = a; merged->right = b;
        pq.push(merged);
    }

    unordered_map<char, string> codes;
    buildCodes(pq.top(), "", codes);
    cout << "Huffman Codes:\n";
    for (auto& p : codes) cout << p.first << " = " << p.second << endl;
}
