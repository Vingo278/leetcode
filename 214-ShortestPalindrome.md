# 214-Shortest Palindrome

Given a string S, you are allowed to convert it to a palindrome by adding characters in front of it. Find and return the shortest palindrome you can find by performing this transformation.

For example:

Given `"aacecaaa"`, return `"aaacecaaa"`.

Given `"abcd"`, return `"dcbabcd"`.

## 题解

### 转化成最大回文串

问题可以转化为求以第一个元素开始的最大回文子串，只要知道了以第一个元素开始的最大回文子串，那么只要在原字符串前面逆序插入除去以第一个元素开始的最大回文子串后剩下的元素。

昨天刚好做过最大文子串的问题，用Manacher算法可以在时间复杂度O(n)和空间复杂度O(n)内解决。用Manacher求每个元素为中心的回文串半径时，只需要求前一半即可，因为以后一一半的任意元素为中心的回文子串不可能从第一个元素开始。

```c++
string shortestPalindrome(string s) {
  //Manacher求出以每个字符为中心的回文串长度
  string str = "$#";
  for (int i = 0; i !=  s.size(); ++i) {
    str.push_back(s[i]);
    str.push_back('#');
  }
  str.push_back('~');
  int id = 0;
  int mx = 0;
  //只需要求前一半字符的p[]数组中的值。
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

### 利用KMP求next数组的方法

解决该问题可以转化成寻找以第一个元素开始的最大的回文子串。

我们知道回文串的逆序串等于大本身，记字符串为str，逆串为reverse，以第一个元素开始的最大的回文子串将匹配到reverse的后缀，如果把令str+reverse称为新串，则有最大匹配的前缀和后缀的长度是以第一个元素开始的最大回文子串。KMP算法中的next数组保存的就是该元素之前的子串的最大匹配前缀和后缀的长度，我们就可以利用该算法来求解该问题。

KMP算法中的next数组是包括整串的最大匹配前缀和后缀的长度，所以解决该问题的next需要比KMP的next大1，最终next[strlen(str+reverse)]就是第一个元素开始的最大的回文子串的长度。

由于我们用来next数组的字符串是str+reverse拼接的，最终有可能最大匹配前缀和后缀的长度超过了str的长度，所以最终求得的next[strlen(str+rever)]如果大于str的长度，就需要减去str的长度。当然也可以在拼接的时候在str和reverse中间插入一个特殊字符（str中不会出现的字符），即str+‘#’+reverse，来避免最大匹配前缀和后缀的长度超过str的长度。

```c++
string shortestPalindrome(string s) {
  //生成新string
  string reverse_s = s;
  reverse(reverse_s.begin(), reverse_s.end());
  string str = s + '#' + reverse_s;
  //求next数组
  vector<int> next(str.size() + 1);
  next[0] = -1;
  int k = -1;
  int j = 0;
  while (j < next.size()) {
    while (k >= 0 && str[k] != str[j]) k = next[k];
    ++j;
    ++k;
    next[j] = k;
  }
  //需要逆序插到前面的子串
  string pre = s.substr(next[str.size()], s.size() - next[str.size()]);
  reverse(pre.begin(), pre.end());
  return pre + s;
}
```

