# Add Two Numbers

You are given two linked lists representing two non-negative numbers. The digits are stored in reverse order and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.

```
Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)
Output: 7 -> 0 -> 8
```

## 题解

比较简单，需要注意的是几个特例：

> 1. 两个链表为NULL时，返回NULL。
> 2. 进位可能需要多生成一个节点。
> 3. 链表的长度有可能不一样。

```c++
truct ListNode {
  int val;
  ListNode *next;
  ListNode(int x) : val(x), next(NULL) {}
};

ListNode *addTwoNumbers(ListNode *l1, ListNode *l2) {
  if (l1 == NULL) return l2;
  if (l2 == NULL) return l1;
  ListNode *head = new ListNode(0);
  ListNode *cur = head;
  int carry = 0;
  int sum = 0;
  int val1, val2;
  while (l1 != NULL || l2 != NULL) {
    val1 = l1 == NULL ? 0 : l1->val;
    val2 = l2 == NULL ? 0 : l2->val;
    sum = val1 + val2 + carry;
    carry = sum > 9 ? 1 : 0;
    cur->next = new ListNode(sum % 10);
    cur = cur->next;
    if (l1) l1 = l1->next;
    if (l2) l2 = l2->next;
  }
  if (carry) cur->next = new ListNode(1);
  return head->next;
}
```

