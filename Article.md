## Solution
#### Approach 1: Greedy with a collection of prefixes
###### Intuition
As a general rule, any greedy approach to a dynamic programming problem requires us to explicitly make sure that the steps taken lead us to a correct solution. Greedy is only feasible when we have **proof** that greedy is correct.

We can be sure that the first valid subarray <code>[i<sub>1</sub>, j<sub>1</sub>]</code> we find, starting from the left, belongs to a best set of non-overlapping subarrays:
  - <code>j<sub>1</sub></code> is the smallest of all <code>j<sub>k</sub></code>.
  - If we split `nums` at <code>j<sub>1</sub></code>, 
    - To the left of <code>j<sub>1</sub></code> there is no non-overlapping subarray.
    - Any valid subarray that begins on the left side and ends on the right has no non-overlapping subarrays to its left. The remaining space to it's right is a subarray of our remaining space. Therefore, any set of subarrays that we can find in that remaining space is also a set of subarrays of our space. Any set that includes that subarray as a member has a size smaller or equal to the one we selected.
    

###### Algorithm
<Algorithm description>

###### Complexity Analysis
<Complexity Analysis>
