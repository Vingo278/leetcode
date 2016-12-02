# 336-Palindrome Pairs

Given a list of **unique** words, find all pairs of **distinct** indices `(i, j)` in the given list, so that the concatenation of the two words, i.e. `words[i] + words[j]` is a palindrome.

**Example 1:**
Given `words` = `["bat", "tab", "cat"]`
Return `[[0, 1], [1, 0]]`
The palindromes are `["battab", "tabbat"]`

**Example 2:**
Given `words` = `["abcd", "dcba", "lls", "s", "sssll"]`
Return `[[0, 1], [1, 0], [3, 2], [2, 4]]`
The palindromes are `["dcbaabcd", "abcddcba", "slls", "llssssll"]`

## 题解

通过hash表来来查找字符串对应下标。

对于每个字符串str：

1. 如果字符串是回文串，那么“”+str和str+“”必定是回文串，只需要从hash表中找“”对应的下标
2. 把字符串分成两半left和right，记left和right的逆串是left_r和right_r:
   * 如果left是回文串，right_r+str必定是回文串，只需要从hash表中找right_r对于的下标
   * 如果right是回文串，str+left_r必定是回文串，只需要从hash表中找left_r对应的下标

对于第2步，我以下的代码是利用Manacher算法求出每个字符对应的最大回文串的半径，通过坐标加减半径是否等于0或strlen(len)来判断left和right是否为回文串。假设有k字符串，每个字符串最大长度是n，一开始我以为利用Manacher算法的时间复杂度应该是O(k\*n)，但是由于取子串和反转子串的时间复杂度是O(n)，利用Manacher算法的最坏时间复杂度还是O(k\*n^2)。

LeetCode测试的时候发现，普通比较回文串的方法比用Manacher算法速度要快。对于普通匹配的方法的最快情况对应的也是Manacher的最坏情况，而Manacher会比较复杂一些，时间常数比较大。而在一般情况下，普通匹配在大部分情况下都是第一步匹配就不成功，匹配速度很快，况且LeetCode的数据一般都不大，复杂度不满足都经常AC。

但是，我还是想在练一下Manacher算法啊，为什么？几乎所有回文串问题都可以转化成回文子串问题，而回文子串问题，用Manacher算法一般都可以解决的。

注意：对于“”一定要跳过，因为它是回文串，会匹配自己，而且hash表中必定会找打自己的下标。

```c++
vector<int> getSubstringRadius(string s) {
  string str = "$#";
  for(int i = 0; i != s.size(); ++i) {
    str.push_back(s[i]);
    str.push_back('#');
  }
  str.push_back('~');
  int id = 0;
  int mx = 0;
  vector<int> p(str.size());
  for (int i = 1; i < p.size() - 1; ++i) {
    if (i < mx)
      p[i] = p[2 * id - i] < mx - i ? p[2 * id - i] : mx - i;
    else
      p[i] = 1;
    while (str[i + p[i]] == str[i - p[i]]) ++p[i];
    if (i + p[i] > mx) {
      mx = i + p[i];
      id = i;
    }
  }
  return p;
}

vector<vector<int>> palindromePairs(vector<string>& words) {
  unordered_map<string, int> hash;
  for (int i = 0; i != words.size(); ++i) hash[words[i]] = i;
  vector<vector<int>> result;
  string sub;
  for (int i = 0; i != words.size(); ++i) {
    if (words[i].size() == 0) continue; //""跳过，因为只能和自己成对
    vector<int> radius = getSubstringRadius(words[i]); //Manacher算法得出的数组
    //字符串是回文串时，找""
    if ((radius.size() / 2- radius[radius.size()/ 2]) / 2 == 0) {
      if (hash.find("") != hash.end()) {
        result.push_back(vector<int> {i, hash[""]});
        result.push_back(vector<int> {hash[""], i});
      }
    } else {
      //不是回文串，找字符串的逆串
      sub = words[i];
      reverse(sub.begin(), sub.end());
      if (hash.find(sub) != hash.end())
        result.push_back(vector<int> {i, hash[sub]});
    }
    //遍历左边的元素，如果左边是回文串
    for (int j = 2; j < radius.size() / 2; ++j) {
      if ((j - radius[j]) / 2 == 0) {
        sub = words[i].substr((j + radius[j]) / 2  - 1, words[i].size() - radius[j] + 1);
        reverse(sub.begin(), sub.end());
        if (hash.find(sub) != hash.end())
          result.push_back(vector<int> {hash[sub], i});
      }
    }
    //遍历右边的元素，如果右边是回文串
    for (int j = radius.size() / 2 + 1; j < radius.size() - 2; ++j) {
      if ((j + radius[j]) / 2 - 2 == words[i].size() - 1) {
        sub = words[i].substr(0, words[i].size() - radius[j] + 1);
        reverse(sub.begin(), sub.end());
        if (hash.find(sub) != hash.end())
          result.push_back(vector<int> {i, hash[sub]});
      }
    }
  }
  return result;
}
```

