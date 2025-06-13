# 大雪深埋

双指针法 删除倒数第N个节点 先放出一条狗之后再说

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        //双指针法
        //快的先走 和慢的n个差值
        //慢的那个就是要删除的节点
        ListNode* dummy = new ListNode(0);
        dummy->next = head;
        ListNode* fast = dummy;
        ListNode* slow = dummy;
        while((n--) && (fast != nullptr)){
            fast=  fast->next;
        }
        fast = fast->next;//再走一步 岔开n+1个节点
        while((fast != nullptr) && (slow != nullptr)){
            fast = fast->next;
            slow = slow->next;
        }
        ListNode* tmp_ptr = slow->next;
        slow->next = tmp_ptr->next;
        delete tmp_ptr;
        tmp_ptr = nullptr;
        return dummy->next;
    }
};
```

