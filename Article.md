# Solution
### Approach 1: Greedy with a set of prefixes
##### Intuition

This problem involves a certain buildup of complexity. A brute force approach would force us to:
  - Find not one subarray of `nums` with `sum == target` but all possible *valid subarrays*.
  - Find all combinations of valid subarrays where none overlaps with each other.
  - Loop through our collection of collections of subarrays to find out which one has the biggest size.
  
Here we will try to greedily cut `nums` into sections that we know contain valid subarrays, as often as we can, and count the number of cuts. We will assume that the first subarray we find belongs to the best collection. Then, starting at the next index, the first subarray we find also belongs to the best collection.

We need to make sure that our solution is correct. If we can prove that the first chosen subarray is the first of a best collection, it is easy to see that `max(nums) == 1 + max(nums_after_cut)`. 

At each cut, we are effectively choosing one subarray that's entirely inside the current section. There could be more than one, but all of them end at the last position of the cut. If not, we would have found them earlier. We don't care about which one we choose, these are all equally acceptable alternatives, all of them overlap, and besides, we only care about the number of subarrays. We simply do `answer++`.

We could also have a valid subarray that conflicts with these, that ends after our cut. This alternative choice could be just as good as the one we are taking, but it could never be better: let <code>(i<sub>1</sub>, i<sub>2</sub>)</code> be the starting and ending indexes of our first choice, <code>(j<sub>1</sub>, j<sub>2</sub>)</code> our alternative. By definition, <code>i<sub>2</sub> < j<sub>2</sub></code>. Therefore, <code>(j<sub>2</sub> + 1, nums.length-1)</code> is a subarray of <code>(i<sub>2</sub> + 1, nums.length-1)</code>. Any collection of subarrays of a subarray of some `A` is a collection of subarrays of `A`. `max(after_alternative_cut) <= max(after_current_cut)`.

Any non-conflicting subarray is after the cut, and we can deal with it once we get there, any one that conflicts is not better than the one we are choosing. The chosen subarray is the first of a best collection of subarrays.
    

##### Algorithm
We need to loop through `nums` from the beginning, and at each point we want to check if there is a subarray of `nums` that ends where we are, that adds up to `target`. 

We can maintain a list of (sums of) prefixes:
```
{
nums[0] + nums[1] + ... + nums[i-1],
          nums[1] + ... + nums[i-1],
                                ...,
                          nums[i-1]
}
```
At each point we would check if we have a prefix where `prefix + nums[i] == target`.
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
}
else add sum to the set;
```

##### Analysis of constraints
Within the given constraints, we will never have our `sum` variable be bigger than <code>10<sup>4</sup> * 10<sup>5</sup> = 10<sup>9</sup></code>, nor will any element of our set be bigger than that value. 4-byte signed integers would do fine. `Answer` will also never be bigger than <code>10<sup>5</sup></code>. Also, there is no concern for an empty `nums`, and `target` is within reasonable constraints. There is no need to check for any special boundary cases.

We do need to pay attention to when `nums[i] == target`, e.g. subarrays of size 1. Empty subarrays are explicitly forbidden by the problem description, and with good reason, given that `target` can be 0.

##### Complexity Analysis
Worst case, we never find a valid subarray and our set of complements keeps growing by one at each point. We always need to traverse the whole loop, and each time we need to make one lookup and one addition to our set.

Time complexity:  ***O(n)***

Space complexity: ***O(n)***
