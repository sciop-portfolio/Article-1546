# Solution
### Approach 1: Greedy with a collection of prefixes
##### Intuition

This problem entails a certain buildup of complexity. A brute force approach would force us to:
  - Find not one substring of `nums` with `sum == target` but all possible *valid substrings*.
  - Find all combinations of valid substrings where none overlaps with each other.
  - Loop through our collection of collections of substrings to find out which one has the biggest size.
  
Here we will try to greedily cut `nums` into sections that we know contain valid substrings, as often as we can, and count the number of cuts.

As a general rule, any greedy approach to a dynamic programming problem requires us to explicitly make sure that the steps taken lead us to a correct solution. Greedy is only good in this kind of problems when we have **proof** that our particular greedy solution is correct.

We can be sure that the first valid [subarray](https://www.techiedelight.com/difference-between-subarray-subsequence-subset/) <code>[i<sub>1</sub>, j<sub>1</sub>]</code> we find, starting from the left, belongs to a best collection of non-overlapping subarrays:
  - <code>j<sub>1</sub></code> is the smallest of all <code>j<sub>k</sub></code>.
  - If we split `nums` into two subarrays <code>[0, j<sub>1</sub>]</code> and <code>[j<sub>1</sub> + 1, nums.length - 1]</code>, 
    - To the left side there isn't more than one non-overlapping valid subarray. There could be more than one valid subarray, but all of them end at <code>j<sub>1</sub></code>.
    - Any valid subarray <code>[i<sub>m</sub>, j<sub>m</sub>]</code> that begins on the left side and ends on the right has no valid non-overlapping subarrays to the left of <code>i<sub>m</sub></code>. The remaining space to the right of <code>j<sub>m</sub></code> is a subarray of our remaining space, since by definition <code>j<sub>1</sub> < j<sub>m</sub></code>. Therefore, any collection of subarrays that we can find in that remaining space (<code>[j<sub>m</sub> + 1, nums.length - 1]</code>) is also a collection of subarrays of our space (<code>[j<sub>1</sub> + 1, nums.length - 1]</code>). Any collection that includes <code>[i<sub>m</sub>, j<sub>m</sub>]</code> as a member has a size smaller or equal to the one we are extracting. Remember that **we only want the maximum size**, not the subarrays themselves.
  - At this point, we can recursively find the rest of the collection by returning `maxNonOverlapping(remaining_nums_after_j1, target) + 1`, or better yet, reset our auxiliary variables and continue traversing `nums` from <code>j<sub>1</sub> + 1</code>.
    

##### Algorithm
We need to loop through `nums` from the beginning, and at each point we want to check if there is a subarray of `nums` that ends where we are, that adds up to `target`. 

We can maintain a list of (sums of) prefixes, and at each point check if we have a prefix where `prefix + nums[i] == target`.
A naive approach would be to do just that:
```
for each prefix {
  if(prefix + nums[i] == target) {
    answer++;
    empty our collection;
  }
  else prefix = prefix + nums[i];
}
if(we didn't find any match) {
  add nums[i] to the collection
}
```

This approach would take us O(n<sup>2</sup>) time (O(n) per cycle).

However, this is wasting a lot of time: we are adding the same number again and again. Instead, we could:
- Use a set as our container of prefixes. Checking if a set has an element `e` where `e == target - nums[i]` takes us O(1) time. Also, in a better-than-worse-case scenario, it would save us some space and time.
- Instead of saving the sum of all elements that belong to our prefix, we save the sum of all that **don't belong**. We define an integer `sum` of all elements up to the current one, so that `sum - complement_to_the_prefix` equals `prefix`. If `sum - complement_to_the_prefix == target - nums[i]`, then we have found a valid subarray. At the end of each cycle, `sum == new_complement` without any other intervention, so we just have to add `sum` to our set, and that costs us O(1) time and O(1) space.
```
sum = sum + nums[i];
if(our set contains (sum - target)) {
  answer++;
  empty the set;
}
else add sum to the set;
```

##### Analysis of constraints
Within the given constraints, we will never have our `sum` variable be bigger than <code>10<sup>4</sup> * 10<sup>5</sup> = 10<sup>9</sup></code>, nor will any element of our set be bigger than that value. 4-byte signed integers would do fine. Also, there is no concern for an empty `nums`, and `target` is within reasonable constraints. There is no need to check for any special boundary cases.

We do need to pay attention to when `nums[i] == target`, e.g. subarrays of size 1. Empty subarrays are explicitly forbidden by the problem description, and with good reason, given that `target` can be 0.

##### Complexity Analysis
Worst case, we never find a valid subarray and our set of complements keeps growing by one at each point. We always need to traverse the whole loop, and each time we need to make one lookup and one addition to our set.

Time complexity:  ***O(n)***

Space complexity: ***O(n)***
