# 214-Shortest Palindrome

Given a string S, you are allowed to convert it to a palindrome by adding characters in front of it. Find and return the shortest palindrome you can find by performing this transformation.

For example:

Given `"aacecaaa"`, return `"aaacecaaa"`.

Given `"abcd"`, return `"dcbabcd"`.

## 题解

问题可以转化为求以第一个元素开始的最大回文子串，只要知道了以第一个元素开始的最大回文子串，那么只要在原字符串前面逆序插入出去以第一个元素开始的最大回文子串后剩下的元素。

昨天刚好做过最大文子串的问题，用Manacher算法可以在时间复杂度O(n)和空间复杂度O(n)内解决。

```c++
string shortestPalindrome(string s) {
  //Manacher求出以每个字符为中心的回文串长度
  string str;
  str.push_back('$');
  str.push_back('#');
  for (int i = 0; i !=  s.size(); ++i) {
    str.push_back(s[i]);
    str.push_back('#');
  }
  str.push_back('~');
  int id = 0;
  int mx = 0;
  vector<int> p(str.size() / 2 + 1);
  for (int i = 1; i != p.size(); ++i) {
    if (i < mx)
      p[i] = mx - i < p[2 * id - i] ? mx - i : p[2 * id- i];
    else
      p[i] = 1;
    while (str[i - p[i]] == str[i + p[i]]) ++p[i];
    if (i + p[i] > mx) {
      mx = i + p[i];
      id = i;
    }
  }
  int index = 0;
  int len = 1;
  //找出最后一个以第一个字符开始的回文串
  for (int i = 1; i != p.size(); ++i) {
    if ((i - p[i]) / 2 == 0) {
      len = p[i];
      index = i;
    }
  }
  index = (index - len) / 2;
  --len;
  //生成最终结果的回文串
  string result;
  for (int i = s.size() - 1; i >= index + len; --i) result.push_back(s[i]);
  result += s;
  return result;
}
```

