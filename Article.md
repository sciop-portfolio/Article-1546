# Solution
### Approach 1: Greedy with a collection of prefixes
##### Intuition
As a general rule, any greedy approach to a dynamic programming problem requires us to explicitly make sure that the steps taken lead us to a correct solution. Greedy is only feasible when we have **proof** that our particular greedy solution is correct.

We can be sure that the first valid subarray <code>[i<sub>1</sub>, j<sub>1</sub>]</code> we find, starting from the left, belongs to a best set of non-overlapping subarrays:
  - <code>j<sub>1</sub></code> is the smallest of all <code>j<sub>k</sub></code>.
  - If we split `nums` at <code>j<sub>1</sub></code>, 
    - To the left of <code>j<sub>1</sub></code> there is no non-overlapping subarray.
    - Any valid subarray that begins on the left side and ends on the right has no non-overlapping subarrays to its left. The remaining space to it's right is a subarray of our remaining space. Therefore, any set of subarrays that we can find in that remaining space is also a set of subarrays of our space. Any set that includes that subarray as a member has a size smaller or equal to the one we are extracting. Remember that we only want the *maximum size*, not the set itself.
  - At this point, we can recursively find the rest of the set by returning `maxNonOverlapping(...) + 1`, or better yet, reset our auxilliary variables and continue traversing `nums` from <code>j<sub>1</sub> + 1</code>.
    

##### Algorithm
We need to loop through `nums` from the beginning, and at each point we want to check if there is a substring of `nums` that ends where we are, that adds up to `target`. 

We can maintain a collection of (partial sums of) prefixes, and at each point check if we have a prefix where `prefix + nums[i] == target`.
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

This approach would take us O(n<sup>2</sup>) time, both the checking and the updating.

However, this is wasting a lot of time: we are adding the same number again and again. Instead, we could:
- Use a set as our container of prefixes. Checking if a Set has an element `e` where `e == target - nums[i]` takes us O(1) time.
- Instead of saving the sum of all elements that belong to our subarray, we save the sum of all that *don't belong*. We define an integer `sum` of all elements up to the current one, so that `sum - complement-to-the-prefix` equals `prefix`. If `sum + nums[i] - complement-to-the-prefix == target`, then we have found a valid subarray.
```
sum = sum + nums[i];
if(our set contains (sum - target)) {
  answer++;
  empty the set;
}
else add sum to the set;
```

##### Analysis of constraints
Within the given constraints, we will never have our `sum` variable be bigger than <code>10<sup>4</sup> * 10<sup>5</sup> = 10<sup>9</sup></code>, nor will any element of our set be bigger than that value. 4-byte signed integers would do fine. There isn't also the possibility of an empty `nums`, and `target` is within reasonable constraints. There is no need to check for any special boundary conditions.

##### Complexity Analysis
Worst case, we never find a valid subarray and our set of complements keeps growing by one at each point. We always need to traverse the whole loop, and each time we need to make one lookup and one update to our set.

Time complexity: ***O(n)***

Space complexity: ***O(n)***
