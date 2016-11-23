# 445-Add Two Numbers II

You are given two linked lists representing two non-negative numbers. The most significant digit comes first and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

Follow up:
What if you cannot modify the input lists? In other words, reversing the lists is not allowed.

Example:

```
Input: (7 -> 2 -> 4 -> 3) + (5 -> 6 -> 4)
Output: 7 -> 8 -> 0 -> 7
```

## 题解

比2-Add Two Numbers稍微麻烦一点。简单的想法是反转两个链表，就变成了2-Add Two Numbers的问题，转换有两种方法，一种式直接在原来的链表上转换，一种是利用栈来转换。

### 利用栈

```c++
struct ListNode {
  int val;
  ListNode *next;
  ListNode(int x) : val(x), next(NULL) {}
};

ListNode *addTwoNumbers(ListNode *l1, ListNode *l2) {
  if (l1 == NULL) return l2;
  if (l2 == NULL) return l1;
  stack<int> st1, st2;
  ListNode *head = NULL;
  ListNode *cur = NULL;
  int sum = 0;
  int carry = 0;
  while (l1 != NULL) {
    st1.push(l1->val);
    l1 = l1->next;
  }
  while (l2 != NULL) {
    st2.push(l2->val);
    l2 = l2->next;
  }
  //用两个while逻辑上差了些，但是减少了判断次数
  while (!st1.empty()) {
    if (!st2.empty()) {
      sum = st1.top() + st2.top() + carry;
      carry = sum > 9 ? 1 : 0;
      cur = head;
      head = new ListNode(sum % 10);
      head->next = cur;
      st1.pop();
      st2.pop();
    } else {
      sum = st1.top() + carry;
      carry = sum > 9 ? 1 : 0;
      cur = head;
      head = new ListNode(sum % 10);
      head->next = cur;
      st1.pop();
    }
  }
  while (!st2.empty()) {
    sum = st2.top() + carry;
    carry = sum > 9 ? 1 : 0;
    cur = head;
    head = new ListNode(sum % 10);
    head->next = cur;
    st2.pop();
  }
  
  if (carry) {
    cur = head;
    head = new ListNode(1);
    head->next = cur;
  }
  return head;
}
```

### 直接反转链表

有两种办法：

1. 遍历节点，依次把节点插到head之前，每次插入后需要维护好head指针。
2. 用3个指针来实现，反转next之后，3指针移动一个节点。

### 不需要反转链表的方法

>1. 先遍历两个链表，计算两个链表的长度
>
>2. 根据链表长度，对应节点相加
>
>3. 利用两个指针实现进位。
>
>   对于进位，当前位需要进位时，高1位如果不是就9，直接进1位就结束，如果是9，需要进位到依次的高1位不是9才停止，也就是只要知道需要进位的位前的第一个不为9的位，就知道了进位的范围了。
>
>   - piont1从头开始遍历
>     - 如果point1->val< 9令piont2 = point1
>     - 如果point1>9，对point2到point1的节点加1，模10
>

>如果head大于10需要先生成一个节点。point1->val< 9的判断成功的次数有点多啊，不知道有没有优化的方法。

```c++
ListNode *addTwoNumbers(ListNode *l1, ListNode *l2) {
  if (l1 == NULL) return l2;
  if (l2 == NULL) return l1;

  int len1 = 0;
  int len2 = 0;
  for (ListNode *p = l1; p != NULL; p = p->next) ++len1;
  for (ListNode *p = l2; p != NULL; p = p->next) ++len2;
  if (len1 < len2) {
    int tmp = len1;
    len1 = len2;
    len2 = tmp;
    ListNode *p = l1;
    l1 = l2;
    l2 = p;
  }
  ListNode *head = new ListNode(0);
  ListNode *cur = head;
  for (int i = len1 - len2; i != 0; --i) {
    cur->next = new ListNode(l1->val);
    cur = cur->next;
    l1 = l1->next;
  }
  while (l1) {
    cur->next = new ListNode(l1->val + l2->val);
    cur = cur->next;
    l1 = l1->next;
    l2 = l2->next;
  }

  //有可能最高进位，我习惯head是空，为了方便，如果进位就直接进到head
  //返回时通过head->val是0或1确定返回head还是head->next
  ListNode *bound = head;
  cur = head->next;
  while (cur) {
    if (cur->val < 9)
      bound = cur;
    else if (cur->val > 9) {
      while (bound != cur) {
        bound->val = (bound->val + 1) % 10;
        bound = bound->next;
      }
      cur->val -= 10;
    }
    cur = cur->next;
  }

  if (head->val == 1)
    return head;
  else
    return head->next;
}
```
