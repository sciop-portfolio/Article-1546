## Solution
#### Approach 1: Greedy with a collection of prefixes
###### Intuition
As a general rule, any greedy approach to a dynamic programming problem requires us to explicitly make sure that the steps taken lead us to a correct solution. Greedy is only feasible when we have **proof** that greedy is correct.

We can be sure that the first valid subarray '[i<sub>1</sub>, j<sub>1</sub>]' we find, starting from the left, belongs to a best set of non-overlapping subarrays:
  - 'j<sub>1</sub>' is the smallest of all 'j<sub>k</sub>'.
  - If we split 'nums' at 'j<sub>1</sub>', 
    - To the left of 'j<sub>1</sub>' there is no more than one non-overlapping subarray.
    - Any valid subarray that begins on the left side and ends on the right has no non-overlapping subarrays to its left. The remaining space to it''s right is a subarray of our remaining space. Therefore, any set of subarrays that we can find in that remaining space is also a set of subarrays of our space. Any solution that includes that subarray as a member has a size smaller or equal to the one we selected.
    

###### Algorithm
<Algorithm description>

###### Complexity Analysis
<Complexity Analysis>
