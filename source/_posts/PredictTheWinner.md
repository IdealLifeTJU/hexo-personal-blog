---
title: LeetCode-486 预测赢家
date: 2019-9-16
categories: "湖中镜岛"
tags: ["算法实战"]
---

**什么是湖中镜岛**
&#8194;&#8194;&#8194;&#8194;听着！岛主刚刚发现了一个可怕的事实。在我们的小岛漂浮着的的湖水中，以整个湖面为对称面。存在着另一个茵尼斯弗利岛！每当天气晴朗，那些在岸上的人看到的湖水中岛的倒影，事实上是确实存在的。只不过，这座湖下之岛并不按自然的规律演变，它由一个庞大而精密的人工智能控制着，这个人工智能模拟自然的嬗变，力图令自己的造物比我们的小岛更完美……我们需要到这座完全由0和1构成的岛中去探险，掌握尽可能多的计算机学科的知识，然后弄清楚这个人工智能究竟想要干什么！

## LeetCode 486 预测赢家
&#8194;&#8194;&#8194;&#8194;实际上是在字节秋招笔试第三题看到的，当时递归AC了60%，DP做法没想出来。
&#8194;&#8194;&#8194;&#8194;给定一个表示分数的非负整数数组。 玩家1从数组任意一端拿取一个分数，随后玩家2继续从剩余数组任意一端拿取分数，然后玩家1拿，……。每次一个玩家只能拿取一个分数，分数被拿取之后不再可取。直到没有剩余分数可取时游戏结束。最终获得分数总和最多的玩家获胜。
&#8194;&#8194;&#8194;&#8194;给定一个表示分数的数组，预测玩家1是否会成为赢家。你可以假设每个玩家的玩法都会使他的分数最大化。

**示例1：**
```
输入: [1, 5, 2]
输出: False
解释: 一开始，玩家1可以从1和2中进行选择。
如果他选择2（或者1），那么玩家2可以从1（或者2）和5中进行选择。如果玩家2选择了5，那么玩家1则只剩下1（或者2）可选。
所以，玩家1的最终分数为 1 + 2 = 3，而玩家2为 5。
因此，玩家1永远不会成为赢家，返回 False。
示例 2:

输入: [1, 5, 233, 7]
输出: True
解释: 玩家1一开始选择1。然后玩家2必须从5和7中进行选择。无论玩家2选择了哪个，玩家1都可以选择233。
最终，玩家1（234分）比玩家2（12分）获得更多的分数，所以返回 True，表示玩家1可以成为赢家。
```

### 一、递归
&#8194;&#8194;&#8194;&#8194;从思路上来说最容易想，玩家一拿到了一个数，而玩家二会有两个选择，而他会选择留给玩家一的收益最小的。利用递归函数一步步做就可以了。
```java
class Solution {
    public boolean PredictTheWinner(int[] nums) {
        int output = prediction(nums, 0, nums.length-1);
        int sum = 0;
        for(int i=0; i<nums.length; i++){
            sum += nums[i];
        }
        if(output >= sum-output){
            return true;
        }
        return false;
    }
    
    public int prediction(int[] nums, int s, int e){
        if(s == e){
            return nums[s];
        }
        if(s + 1 == e){
            return Math.max(nums[s], nums[e]);
        }
        return Math.max(nums[s] + Math.min(prediction(nums, s+2, e), prediction(nums, s+1, e-1)), 
                        nums[e] + Math.min(prediction(nums, s, e-2), prediction(nums, s+1, e-1)));
    }
}
```
&#8194;&#8194;&#8194;&#8194;执行结果：通过，执行用时：38ms， 在所有Java提交中击败了15.43%的用户，内存消耗：34.6MB，在所有Java提交中击败了90.91%的用户。

### 二、动态规划
&#8194;&#8194;&#8194;&#8194;设`dp[i][j]`为从`i`到`j`范围内玩家一可以超出玩家二的分数，则最后结果为`dp[0][nums.length-1] > 0`。对于两端的分数设为`nums[start]`以及`nums[end]`，若选择`nums[start]`，则在玩家二选择时，他所获得的超出玩家一的分数为`dp[start+1][end]`；若选择`nums[end]`，则在玩家二选择时，他所获得的超出玩家一的分数为`dp[start][end-1]`。故`dp[start][end] = Math.max(nums[start]-dp[start+1][end], nums[end]-dp[start][end-1]);`至此状态转换方程完成。
&#8194;&#8194;&#8194;&#8194;当`start == end`时，`dp[start][end] = nums[start]`。
```java
class Solution {
    public boolean PredictTheWinner(int[] nums) {
        int n = nums.length;
        int[][] dp = new int[n][n];
        for(int i=0; i<n; i++){
            dp[i][i] = nums[i];
        }
        
        for(int i=n-2; i>=0; i--){
            for(int j=i+1; j<n; j++){
                dp[i][j] = Math.max(nums[i]-dp[i+1][j], nums[j]-dp[i][j-1]);
            }
        }
        
        return (dp[0][n-1] >= 0);
    }
}
```

执行结果：通过，执行用时：1 ms，在所有Java提交中击败了100.00%的用户，内存消耗：35.2 MB，在所有Java提交中击败了70.13%的用户。

