# 4-Median of Two Sorted Arrays

There are two sorted arrays **nums1** and **nums2** of size m and n respectively.

Find the median of the two sorted arrays. The overall run time complexity should be O(log (m+n)).

**Example 1:**

```
nums1 = [1, 3]
nums2 = [2]

The median is 2.0
```

**Example 2:**

```
nums1 = [1, 2]
nums2 = [3, 4]

The median is (2 + 3)/2 = 2.5
```

## 题解：

### O(m+n)的解法

很简单的题，像归并一样比较两个最小的数去比较就可以了，注意特殊测试集和边界。

```
nums1 = []
nums2 = []

nums1 = []
nums2 = [1]

nums1 = [1]
nums2 = []
```

这种解法虽然简单，但是感觉代码写的不是很好。

```c++
int lengthOfLongestSubstring(string s) {
  //初始化hash为-1，就不用对第一个元素特殊处理了
  vector<int> hash(128, -1);
  int longest = 0;
  int len = 0;
  int start = -1;
  for (int i = 0; i < s.size(); ++i) {
    if (hash[s[i]] >= start) {
      start = hash[s[i]] + 1;
      len = i - start;
    }
    hash[s[i]] = i;
    ++len;
    longest = len > longest ? len : longest;
  }
  return longest;
}

double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
  int len = nums1.size() + nums2.size();
  //偶数时需要找到len/2和len/2+1，奇数时需要找到len/2+1;
  //由于len小于2时，len/2+1会导致数组越界
  int len_half = len < 2 ? len : len / 2 + 1;
  int idx1 = 0;
  int idx2 = 0;
  int m1 = 0;
  int m2 = 0;
  int tmp;
  for (int i = 0; i != len_half; ++i) {
    if (idx1 == nums1.size()) {
      tmp = nums2[idx2];
      ++idx2;
    }
    else if (idx2 == nums2.size()) {
      tmp = nums1[idx1];
      ++idx1;
    } else {
      if (nums1[idx1] < nums2[idx2]) {
        tmp = nums1[idx1];
        ++idx1;
      } else {
        tmp = nums2[idx2];
        ++idx2;
      }
    }
    if (i == len_half - 2) m1 = tmp;
    if (i == len_half - 1) m2 = tmp;
  }
  //len小于2返回的是m2，注释部分加不加返回也刚好正确了
//  if (len < 2)
//    return m2;
  if (len % 2 == 1)
    return (double)m2;
  else
    return (m1 + m2) / 2.0;
}
```

### O(log(m+n))解法

把问题转化成第k小的问题，对于两个排序好的数组：

1. 令mid1 = min(array1.size, k/2)， mid2 = k - mid1

2. 比较array1[mid1]和array[mid2]，

   * 如果arraya[mid1]比较小，则说第k小数不在arrry1[1,mid1]之间，只需要从array1[mid1+1]之后和array2找第k-mid1小数即可，显然可以递归实现。

   * array2[mid2]比较大，同理。

3. k=1时，直接比较，结束递归。一个数组为空时，可以提前结束递归。

```c++
int findKSmallestSortedArrays(vector<int>::iterator l1, vector<int>::iterator r1,
                              vector<int>::iterator l2, vector<int>::iterator r2, int k) {
  //一个数组为空时迅速结束递归
  if (l1 == r1) return *(l2 + k - 1);
  if (l2 == r2) return *(l1 + k - 1);
  //k=1，递归结束
  if (k == 1) return *l1 < *l2 ? * l1 : *l2;
  int mid1 = r1 - l1 < k / 2 ? r1 - l1 : k / 2;
  int mid2 = k - mid1;
  int val1 = *(l1 + mid1 - 1);
  int val2 = *(l2 + mid2 - 1);
  if (val1 < val2)
    return findKSmallestSortedArrays(l1 + mid1, r1, l2, r2, k - mid1);
  else
    return findKSmallestSortedArrays(l1, r1,l2 + mid2, r2, k - mid2);
}

double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
  int size = nums1.size() + nums2.size();
  if (size == 0) return 0.0;
  if (size % 2 == 1)
    return findKSmallestSortedArrays(nums1.begin(), nums1.end(), nums2.begin(), nums2.end(), size / 2 + 1);
  else
    return (findKSmallestSortedArrays(nums1.begin(), nums1.end(), nums2.begin(), nums2.end(), size / 2)
          + findKSmallestSortedArrays(nums1.begin(), nums1.end(), nums2.begin(), nums2.end(), size / 2 + 1))
          / 2.0;
}
```

