# k个一组翻转链表


套两个循环

一个大循环更新外部的节点p0
一定要记住整个翻转的前一个节点就是p0
for(;lenth>=k;lenth-=k)

需要背下翻转每次的操作 只需要处理两个指针 
一个小循环进行翻转
小循环只需要处理pre和cur,而且每次只处理一次翻转操作

LineNode* nxt = cur->next;
cur->next = pre;
pre = cur：
cur = nxt；

以及要记住的一些性质
```cpp
/*
pre指向翻转链表最后一个节点
cur指向翻转链表的最后一个节点的下一个节点，也即是翻转链表的第一个节点，到最后也就是nullptr了 也是要开始的第一个节点
p0指向要翻转链表的上一个节点，也就是一开始的p0->next
*/
struct ListNode{
    int val;
    ListNode* next;
    ListNode() : val(0),next(nullptr){};
    ListNode(int val) : val(val),next(nullptr){};
    ListNode(int val,ListNode*next) : val(val),next(next){};

}
class Solution {
public:
    ListNode* reverseKGroup(ListNode* head, int k) {
        //遍历获取长度
        int lenth = 0;
        ListNode* temp_ptr = head; //3rd 复盘：遍历要从head开始
        while(temp_ptr != nullptr){
            temp_ptr = temp_ptr->next;
            lenth += 1;
        }
        ListNode* dummy = new ListNode(0);
        dummy->next = head;
        ListNode* p0 = dummy;
        ListNode* pre = nullptr;
        ListNode* cur = head;
        for(;lenth>=k;lenth-=k){ //注意边界
            //外层循环 计算多少次翻转
            for(int i{0};i<k;i++){ //注意边界
                //内层循环：进行每次翻转 注意内层循环的翻转链表就是最简单的翻转链表的逻辑


                // 注意内层循环的翻转链表就是最简单的翻转链表的逻辑

                
                ListNode* nxt_inside = cur->next;
                cur->next = pre;
                pre = cur;
                cur = nxt_inside;
            }
            ListNode* nxt_outside = p0->next;
            p0->next = pre;
            nxt_outside->next = cur;
            p0 = nxt_outside; //3rd 复盘：赋值要用临时变量 和内循环一样
        }
        return dummy->next;
    }
};
```