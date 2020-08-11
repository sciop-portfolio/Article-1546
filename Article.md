# Solution
### Approach 1: Greedy with a set of prefixes
##### Intuition

This problem involves a certain buildup of complexity. A brute force approach would force us to:
  - Find not one subarray of `nums` with `sum == target` but all possible *valid subarrays*.
  - Find all combinations of valid subarrays where none overlaps with each other.
  - Loop through our collection of collections of subarrays to find out which one has the biggest size.
  
Here we will try to greedily cut `nums` into sections that we know contain valid subarrays, as often as we can, and count the number of cuts. We will assume that the first subarray we find belongs to the best collection. Then, starting at the next index, the first subarray we find also belongs to the best collection.

We need to make sure that our solution is correct. If we can prove that the first chosen subarray is the first of a best collection, it is easy to see that `max(nums) == 1 + max(nums_after_cut)`. 

At each cut, we are effectively choosing one subarray that's entirely inside the current section. If this subarray doesn't conflict with any other subarray, then it belongs to a best collection of subarrays. If it does conflict, then any alternative choice could be just as good as the one we are taking, but it could never be better: 
 - let <code>(i<sub>st</sub>, i<sub>end</sub>)</code> be the starting and ending indexes of our first choice, <code>(alt<sub>st</sub>, alt<sub>end</sub>)</code> our alternative. 
 - By definition, <code>i<sub>end</sub> <= alt<sub>end</sub></code>, or the cut would have happened sooner.
 - Therefore, <code>(alt<sub>end</sub> + 1, nums.length-1)</code> is a subarray of <code>(i<sub>end</sub> + 1, nums.length-1)</code>.
 - Any collection of subarrays of a subarray of some `A` is a collection of subarrays of `A`.
 - In conclusion, `max(after_alternative_cut) <= max(after_current_cut)`.

Any non-conflicting subarray is after the cut, and we can deal with it once we get there, any one that conflicts is not better than the one we are choosing. The chosen subarray is the first of a best collection of subarrays.

Now, we could `return 1 + maxNonOverlapping(nums_after_cut, target);`, or simply update a counter and reset all auxiliary variables.
    

##### Algorithm
We need to loop through `nums` from the beginning, and at each point we want to check if there is a subarray of `nums` that ends where we are, that adds up to `target`. 

We can maintain a list of (sums of) prefixes:
```
{
nums[0] + nums[1] + ... + nums[i-1],
          nums[1] + ... + nums[i-1],
                                ...,
                          nums[i-1],
                                  0
}
```
...and check at each point if we have a prefix where `prefix + nums[i] == target`.
A naive approach would be to do just that:
```
Add 0 to the list;
for each prefix {
  if(prefix + nums[i] == target) {
    answer++;
    empty the list;
  }
  else prefix = prefix + nums[i];
}
```

This approach would take us O(n<sup>2</sup>) time (O(n) per cycle).

However, this is wasting a lot of time: we are adding the same number again and again. Instead, we could:
- Use a set as our container of prefixes. Checking if a set has an element `e` where `e == target - nums[i]` takes us O(1) time. Also, in a better-than-worse-case scenario, it would save us some space and time, if we have multiple prefixes that add up to the same number.
- Instead of saving the sum of all elements that belong to our prefix, we save the sum of all that **don't belong**:
```
{
0,
nums[0],
nums[0] + nums[1],
...
nums[0] + nums[1] + ... + nums[i-1]
}
```
We define an integer `sum` of all elements up to the current one, so that `sum - complement_to_the_prefix` equals `prefix`. If `sum - complement_to_the_prefix == target - nums[i]`, then we have found a valid subarray. At the end of each cycle, `sum == new_complement` without any other intervention, so we just have to add `sum` to our set, and that costs us O(1) time and O(1) space. Earlier complements don't change as we move to the right.
```
sum = sum + nums[i];
if(our set contains (sum - target)) {
  answer++;
  empty the set;
  sum = 0;
}
add sum to the set;
```

##### Analysis of constraints
Within the given constraints, we will never have our `sum` variable be bigger than <code>10<sup>4</sup> * 10<sup>5</sup> = 10<sup>9</sup></code>, nor will any element of our set be bigger than that value. 4-byte signed integers would do fine. `Answer` will also never be bigger than <code>10<sup>5</sup></code>. In addition, there is no concern for an empty `nums`, and `target` is within reasonable constraints. There is no need to check for any special boundary cases.

We do need to pay attention to when `nums[i] == target`, e.g. subarrays of size 1. Empty subarrays are explicitly forbidden by the problem description, and with good reason, given that `target` can be 0.

##### Complexity Analysis
Worst case, we never find a valid subarray and our set of complements keeps growing by one at each point. We always need to traverse the whole loop, and each time we need to make one lookup and one addition to our set.

Time complexity:  ***O(n)***

Space complexity: ***O(n)***
