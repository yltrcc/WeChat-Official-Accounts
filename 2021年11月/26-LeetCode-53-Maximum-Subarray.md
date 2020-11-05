
# 每日一句
```text
To be a sailor of the world, bound for all ports. 
做世界的水手，游遍所有的港口。
```

# 题目链接

[https://leetcode-cn.com/problems/maximum-subarray/](https://leetcode-cn.com/problems/maximum-subarray/)

# 题目描述

```Bash
给定一个整数数组 nums ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。
```

# 题目示例

```Bash
示例 1：

输入：nums = [-2,1,-3,4,-1,2,1,-5,4]
输出：6
解释：连续子数组 [4,-1,2,1] 的和最大，为 6 。
示例 2：

输入：nums = [1]
输出：1
示例 3：

输入：nums = [0]
输出：0
示例 4：

输入：nums = [-1]
输出：-1
示例 5：

输入：nums = [-100000]
输出：-100000
 
```

# 题目提示

```Bash
1 <= nums.length <= 105
-104 <= nums[i] <= 104
 

进阶：如果你已经实现复杂度为 O(n) 的解法，尝试使用更为精妙的 分治法 求解。
```

# 题目分析

```Bash
⼦问题
  只考虑第⼀个元素，则最⼤⼦段和为其本身 DP[0] = nums[0]
  考虑前两个元素，最⼤⼦段和为 nums[0],num[1]以及 nums[0] + num[1] 中最⼤值 设为DP[1]
  考虑前三个元素，如何求其最⼤⼦段和？还是分为两种情况讨论，第三个元素在最后的字串内吗？
  若第三个元素也包含在最后的字串内，则DP[2] = Max(DP[1]+nums[2] , nums[2])
确认状态
  DP[i] 为 以nums[i]结尾的⼦段的最⼤最短和
  例如 DP[1]则为以nums[1]结尾的最⼤字段和
初始状态
  dp[0] = nums[0]
  dp[1] = max(dp[0]+nums[1] , nums[1])
状态转移⽅程：
  dp[i] = max(dp[i-1]+nums[i],nums[i])
```

# 题目代码

```Java
public class lc53_MaximumSubarray {
   public int maxSubArray(int[] nums) {
   int len = nums.length;
   if(len == 0)
   return 0;
   int [] dp = new int[len];
   dp[0] = nums[0];
   int max = dp[0];
   for (int i = 1; i<len;i++){
   dp[i] = (dp[i-1]+nums[i] > nums[i]) ? dp[i-1]+nums[i] : nums[i];
   if (dp[i]>max)
   max = dp[i];
   }
   return max;
   }
}
```

# 好词佳句
```text
有一天，我们可不可以如此幸福——一起去想去的地方看美丽风景，一起吃想吃的小吃再细细回味，在每一处留下我们的足迹与回忆。

有一天，我们可不可以如此幸福　　有一天，我们可不可以如此幸福——去爬从前说过要一起去的山，彼此依偎看天际明亮的星，对着流星许下相依相守的诺言。

有一天，我们可不可以如此幸福——买一所不大不小的房子，一起设计一起装饰，一起置办家里所有的东西，把温馨渗透在点点滴滴中。

有一天，我们可不可以如此幸福——晚上有你温暖的怀抱，偶尔像小孩子一样吵着要你唱歌，然后你无奈却宠溺的摸摸我的头，让我听着你的声音入眠。
```

# 发表地址

* [微信公众号](http://mp.weixin.qq.com/s?__biz=MzI5ODYyOTE0OQ==&mid=2247483863&idx=1&sn=261e4660b6a7b94bf034367d89c02aeb&chksm=eca3a0a5dbd429b381a68aa1250c50465c9d8f715dd1bf0e14ed35aab36afa0b42d1cc754ed1#rd)