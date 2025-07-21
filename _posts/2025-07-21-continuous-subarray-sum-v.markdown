---
title: "Continuous Subarray Sum V"
date: 2025-07-21
categories:
  - blog
tags:
  - leetcode
  - two-pointers
  - array
  - dynamic-programming
excerpt: "Welcome to my personal page — a space where I experiment with technology to create change, share the lessons I learn, and build in the open."
author_profile: true
read_time: true
show_date: true
toc: true
---
### Continuous Subarray Sum V

**Description:**

An array is said to be a good subarray when there is a consecutive subarray of length at least 1 and satisfies one of the following conditions:

- for all 1 <= i < n, subarray[i] - subarray[i - 1] == 1, i.e., the elements of the subarray are incremented by the difference 1
- For all 1 <= i < n, subarray[i] - subarray[i - 1] == -1, i.e., the elements of the subarray are decremented by the difference 1

And we define that the value of an array is the sum of its elements, for example:

- The array [3, 4, 5] is a good subarray with a value of 12
- The array [9, 8] is a good subarray with a value of 17
- array [1, 3, 5]is not a good subarray because the above condition is not satisfied
- The array [1, 1, 1]is not a good subarray because the above condition is not satisfied

Now given a non-negative integer array n of length n, return the sum of the values of all good subarrays of this array, and since the answer will be large, return the value which is modulo the value to 10^9 + 7

- 1≤nums.length≤10^5, 1≤nums.length≤10^5
- 1≤nums[i]≤10^5, 1≤nums[i]≤10^5
- `1 ≤ nums.length ≤ 10^5`
- `1 ≤ nums[i] ≤ 10^5`

Example

Example 1:

```text
Input:
nums = [1, 2, 3]
Output:
20
Explanation:
The good subarrays that meet the conditions are:
[1], [2], [3], [1, 2], [2, 3], [1, 2, 3]
The sum of the values of all good subarrays is:
1 + 2 + 3 + 3 + 3 + 5 + 6 = 20
```

Example 2:

```text
Input:
nums = [2, 4, 6]
Output:
12
Explanation:
The good subarrays that meet the conditions are:
[2], [4], [6]
The sum of the values of all good subarrays is:
2 + 4 + 6 = 12
```

---

### Brute-Force Approach (O(N<sup>2</sup>))

A straightforward approach is to use two pointers to find all subarrays that meet the criteria of a good subarray, and then summing all of their values. This involves nested loops, leading to a time complexity of O(N<sup>2</sup>). As all subarrays of length 1 are good, this case will be handled separately by summing all elements of `nums`. While this works for smaller arrays, it will time out for larger inputs. 

```java
public class Solution {
    public int getSum(int[] nums) {
        long sum = 0;
        for (int n : nums) {
            sum += n;
        }
        int L = 0;
        while (L < nums.length) {
            int R = L + 1;
            int value = nums[L];
            while (R < nums.length) {
                if (nums[R] - nums[R - 1] != 1 && nums[R] - nums[R - 1] != -1) {
                    break;
                }
                value += nums[R];
                sum += value;
                R++;
            }
            L++;
        }
        return (int) (sum % (1000000000 + 7));
    }
}
```

---

### Optimized Approach (O(N))

To significantly improve performance, we can avoid finding and summing all possible subarrays. The core idea is that if a subarray `arr` is good, then *any* subarray formed from it will also be good. In the brute force approach, we need to find the values of all subarrays of `arr`. This approach is inefficient, since the number of subarrays that can be formed from `arr` is on the scale of O(N<sup>2</sup>). An optimization can be made by instead iterating through each element of the `arr` and finding the number of subarrays that contain that element, which can be done in constant time. If we know the number of occurrences of each element, then we can find the sum of the values of all subarrays of `arr` by summing each element multiplied by the number of its occurrences. This optimization allows finding the value of `arr` and its subarrays in O(N) time.

**Formula:**

In an array `arr`, the number of times an element occurs in its subarrays can be calculated by summing three cases:

- **Element at start:** If `arr[i]` is the starting point of a potential subarray, then the subarray can end at any point following the element. This gives us `nums.length - i - 1` possible end points.

- **Element at end:** Conversely, if `arr[i]` is the ending point of a potential subarray, then the subarray must be start at any element preceding it.  This provides `i` possible start points.

- **Element in between** For elements appearing *between* the start and end, we consider all pairs of starting and ending positions. Again, there are `i` possible starting points and `arr.length - i - 1` possible end points. Since we are *pairing* start and end points, we will multiply, so the number of occurrences for an element in this middle position is `i * (arr.length - i - 1)`.

To get the total number of occurrences for each element across all possible subarrays, we sum the three cases: `numOccurrences = (arr.length - i - 1) + (i) + (i * (arr.length - i - 1))`, which simplifies to `arr.length - 1 + i * (arr.length - i - 1)`.

**Algorithm:**

To make use of our optimization, we find the longest good subarrays in `nums`, and store them in a 2-dimensional list. For each good subarray `arr` in this list, we can find the sum of itself and all of its subarrays by iterating through each of its elements. The amount each element contributes to the sum will be itself multiplied by its occurrences, so `contribution = arr[i] * (arr.length - 1 + i * (arr.length - i - 1))`.

### Final Solution

```java
public class Solution {
    /**
     * @param nums: An integer array
     * @return: Sum of all good subarray elements
     */
    public int getSum(int[] nums) {
        // write your code here
        long sum = 0;
        for(int n : nums) {
            sum += n;
        }
        List<List<Integer>> goodSubarrays = getGoodSubarrays(nums);
        for(List<Integer> subarray : goodSubarrays) {
            for(int i = 0; i < subarray.size(); i++) {
                long numOccurences =  i * (subarray.size() - i - 1) + i + subarray.size() - i - 1;
                sum += (subarray.get(i) * numOccurences);
            }
        }
        return (int) (sum % (1000000000 + 7));
    }
    public List<List<Integer>> getGoodSubarrays(int[] nums) {
        List<List<Integer>> goodSubarrays = new ArrayList<>();
        int L = 0;
        int R = 0;
        while(L < nums.length) {
            List<Integer> goodArray = new ArrayList<>();
            while(R < nums.length) {
                if(R != L && nums[R] - nums[R - 1] != 1) {
                    break;
                }
                goodArray.add(nums[R++]);
            }
            if(goodArray.size() > 1) {
                goodSubarrays.add(goodArray);
            }
            L = R;
        }
        L = 0;
        R = 0;
        while(L < nums.length) {
            List<Integer> goodArray = new ArrayList<>();
            while(R < nums.length) {
                if(R != L && nums[R] - nums[R - 1] != -1) {
                    break;
                }
                goodArray.add(nums[R++]);
            }
            if(goodArray.size() > 1) {
                goodSubarrays.add(goodArray);
            }
            L = R;
        }
        return goodSubarrays;
    }
}
```

**Time Complexity:**

O(N), where N is the length of `nums`. Finding the lognest good subarrays uses two pointers which iterate through the array once. This process must be done twice, once for increasing good subarrays, and once for decreasing good subarrays. The calculation of occurrences for each element takes constant time. In the worst case, all of the elements in `nums` are in a good subarray, requiring N calculations.
