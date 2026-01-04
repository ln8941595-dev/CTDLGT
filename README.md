#include <iostream>
#include <vector>
#include <string>

using namespace std;

struct Node {
    string title;
    int pages;
    vector<Node*> children;

    Node(string t, int p) : title(t), pages(p) {}
};

// 1. Xác định số chương của cuốn sách (Level 1)
int countChapters(Node* root) {
    if (!root) return 0;
    return root->children.size();
}

// 2. Tìm chương dài nhất (nhiều trang nhất)
Node* findLongestChapter(Node* root) {
    if (!root || root->children.empty()) return nullptr;
    Node* longest = root->children[0];
    for (Node* child : root->children) {
        if (child->pages > longest->pages) {
            longest = child;
        }
    }
    return longest;
}

// 3. Tìm và xoá một mục, sau đó cập nhật lại số trang của các mục cha
bool removeAndUpdate(Node* current, string targetTitle, int &pagesRemoved) {
    for (auto it = current->children.begin(); it != current->children.end(); ++it) {
        if ((*it)->title == targetTitle) {
            pagesRemoved = (*it)->pages; // Lưu lại số trang để trừ đi
            delete *it;
            current->children.erase(it);
            current->pages -= pagesRemoved; // Cập nhật cha trực tiếp
            return true;
        }
        // Đệ quy xuống cấp dưới
        if (removeAndUpdate(*it, targetTitle, pagesRemoved)) {
            current->pages -= pagesRemoved; // Cập nhật lại tổ tiên
            return true;
        }
    }
    return false;
}

void printMenu(Node* node, int level = 0) {
    for(int i=0; i<level; i++) cout << "  ";
    cout << "+ " << node->title << " (" << node->pages << " trang)" << endl;
    for(auto child : node->children) printMenu(child, level + 1);
}

int main() {
    Node* book = new Node("Giao trinh C++", 500);
    Node* c1 = new Node("Chuong 1", 100);
    c1->children.push_back(new Node("Muc 1.1", 40));
    c1->children.push_back(new Node("Muc 1.2", 60));
    
    Node* c2 = new Node("Chuong 2", 200);
    c2->children.push_back(new Node("Muc 2.1", 200));
    
    book->children.push_back(c1);
    book->children.push_back(c2);

    cout << "--- Muc luc ban dau ---" << endl;
    printMenu(book);

    int removed = 0;
    if (removeAndUpdate(book, "Muc 1.1", removed)) {
        cout << "\nDa xoa Muc 1.1 (40 trang). Cap nhat lai so trang..." << endl;
    }

    cout << "\n--- Muc luc sau khi xoa ---" << endl;
    printMenu(book);

    return 0;
}
