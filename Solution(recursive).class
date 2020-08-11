class Solution {
    public int maxNonOverlapping(int[] nums, int target) {
        return maxNonOverlapping(nums, target, 0);
    }
    
    public int maxNonOverlapping(int[] nums, int target, int index) {
    	Set<Integer> complements = new HashSet();
        complements.add(0);
        int answer = 0, sum = 0;
        for(int i = index; i < nums.length; i++) {
            sum += nums[i];
            if(complements.contains(sum - target)) {
                return 1 + maxNonOverlapping(nums, target, i + 1);
            }
            complements.add(sum);
        }
        return 0;
    }
}
