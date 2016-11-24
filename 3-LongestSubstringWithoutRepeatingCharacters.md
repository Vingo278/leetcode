# 3-Longest Substring Without Repeating Characters

Given a string, find the length of the longest substring without repeating characters.

Examples:

```
Given "abcabcbb", the answer is "abc", which the length is 3.

Given "bbbbb", the answer is "b", with the length of 1.

Given "pwwkew", the answer is "wke", with the length of 3. Note that the answer must be a substring, "pwke" is a subsequence and not a substring.
```

## 题解

假设string[0,j)的最大无重复子串是string[i, j)，那么string[0, j+1)的最大无重复子串就是：

1. 如果string[j]不在string[i, j)里面，则是string[i, j+1)
2. 如果string[j]在string[i, j)，则需要找到string[j]在string[i, j)中的下标，假设是index[string[j]]，那么显然最大无重复子串就是string[index[string[j]]+1, j+1)。

如何找到index[string[j]]就是关键了，简单粗暴的方法就是遍历，不过从我的写法就可看出，用一个hash表就可以解决了，只需要查找完index[string[j]]后，令index[string[j]]=j就可以一直维护好hash表了。

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

```



