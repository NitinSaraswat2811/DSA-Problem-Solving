class Solution {
    int n;
    int[] dp;
    public String stoneGameIII(int[] stoneValue) {
        n = stoneValue.length;
        dp = new int[n];

        int sum = 0;
        for (int i = 0; i < n; i++) {
            sum += stoneValue[i];
            dp[i] = -1;
        }

        int AliceScore = helper(stoneValue, 0);
        int BobScore = sum - AliceScore;

        if (AliceScore > BobScore) {
            return "Alice";
        } else if (BobScore > AliceScore) {
            return "Bob";
        }
        return "Tie";
    }

    public int helper(int[] stoneValue, int i) {
        if (i >= n) {
            return 0;
        }
        if(dp[i]!=-1) return dp[i];

        int result = Integer.MIN_VALUE;
        int sum = 0;
        for (int j = i; j < Math.min(i + 3, n); j++) {
            sum += stoneValue[j];
            int take1 = helper(stoneValue, j + 2);
            int take2 = Math.min(take1, helper(stoneValue, j + 3));
            int take3 = Math.min(take2, helper(stoneValue, j + 4));

            result = Math.max(result, sum + take3);
        }
        return dp[i] = result;
    }
}
