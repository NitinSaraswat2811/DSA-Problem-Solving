class Solution {
    int n;
    int[][] dp;
    public boolean stoneGame(int[] piles) {
        n = piles.length;
        dp = new int[n][n];
        for(int i = 0;i<n;i++){
            Arrays.fill(dp[i],-1);
        }
       return helper(piles,0,n-1)>0;
    }
    public int helper(int[] piles,int i, int j){
        if(i>j){
            return 0;
        }
        if(dp[i][j]!=-1) return dp[i][j];

         int take_i = piles[i]-helper(piles,i+1,j);
         int take_j = piles[j]-helper(piles,i,j-1);

        return dp[i][j] = Math.max(take_i,take_j);
    }
}
