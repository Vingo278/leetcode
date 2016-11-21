# 1-TwoSum

Given an array of integers, return **indices** of the two numbers such that they add up to a specific target.

You may assume that each input would have **exactly** one solution.

```
Given nums = [2, 7, 11, 15], target = 9,

Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1].
```

## 题解

用hash表可以线性时间解决。

```c++
vector<int> twoSum(vector<int>& nums, int target) {
  unordered_map<int, int> hash_table;
  for (int i = 0; i != nums.size(); ++i) {
    if (hash_table.find(target-nums[i]) != hash_table.end())
      return vector<int> {hash_table[target-nums[i]], i};
    hash_table[nums[i]] = i;
  }
  return vector<int> {};
}
```

$O(n\log n)$时间解决的方法，见15-3Sum，虽然时间复杂度比hash表差，但可以用来解决kSum问题。