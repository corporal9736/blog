---
title: 'Leetcode No.169 Majority Element'
date: 2024-06-27
---

# Question:
Given an array `nums` of size `n`, return the majority element. Here we assume that majority will appear more than `n//2` times.

# Solution 1:
This solution is from [Zhihu](https://www.zhihu.com/question/49973163/answer/235921864) which explains about [Boyer–Moore majority vote algorithm](https://en.wikipedia.org/wiki/Boyer%E2%80%93Moore_majority_vote_algorithm).  
The basic idea is to pair two different elements and remove both of them. Repeating this process and the left element(s) will be the majority. Let's think about the worst case: all other kinds of elements are paired with the majority, then the majority will remain at least one by definition. And, in fact, some non-major elements will be paired and removed, thus more major elements will be left.  
Then we can take a look at the actual implementation. We can use a new array to temporarily store the elements to be paired. Let's call it `temp`. We get the first element from `nums` and then check `temp`. If `temp` is empty, just put the element in it. If the element is different from the first element in `temp`, remove the first element in `temp`. If the element is the same as the first element in `temp`, put the element in it.  
However, things could be easier, since we can see that the element(s) in `temp` will always be the same kind. The only difference is the number of elements. So we don't need an array, we only need the number and a counter. So this is what we finally get.
```python
class Solution:
    def majorityElement(self, nums: List[int]) -> int:
        count = 0 # record the number of element in temp array
        for i in range(len(nums)):
            if count == 0: # temp array is empty, put element
                major = nums[i] 
                count += 1
            elif major == nums[i]: # find same element, counter++
                count += 1
            else: # different element, counter--
                count -= 1
        return num
```

# Solution 2:
This is another interesting solution. If we sort the array `nums`. As there is a kind of element appears more than `n//2` times, the middle of this array must be it. Let's think about the worst case: the majority element is the biggest (or smallest), then the mid element in array `nums` will be the first (or last) one of the majority element. In other cases, the interval of majority element will of course cover the midpoint.  
However, this method is only useful when the cost of sorting the array is acceptable, or the array is already sorted.
