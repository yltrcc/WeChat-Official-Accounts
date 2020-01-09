# 题目链接

[https://leetcode-cn.com/problems/relative-ranks/](https://leetcode-cn.com/problems/relative-ranks/)

# 题目描述

给你一个长度为 n 的整数数组 score ，其中 score[i] 是第 i 位运动员在比赛中的得分。所有得分都 互不相同 。

运动员将根据得分 决定名次 ，其中名次第 1 的运动员得分最高，名次第 2 的运动员得分第 2 高，依此类推。运动员的名次决定了他们的获奖情况：

- 名次第 1 的运动员获金牌 "Gold Medal" 。

- 名次第 2 的运动员获银牌 "Silver Medal" 。

- 名次第 3 的运动员获铜牌 "Bronze Medal" 。

- 从名次第 4 到第 n 的运动员，只能获得他们的名次编号（即，名次第 x 的运动员获得编号 "x"）。

使用长度为 n 的数组 answer 返回获奖，其中 answer[i] 是第 i 位运动员的获奖情况。

# 题目示例

```text
示例 1：
输入：score = [5,4,3,2,1]
输出：["Gold Medal","Silver Medal","Bronze Medal","4","5"]
解释：名次为 [1st, 2nd, 3rd, 4th, 5th] 。

示例 2：
输入：score = [10,3,8,9,4]
输出：["Gold Medal","5","Bronze Medal","Silver Medal","4"]
解释：名次为 [1st, 5th, 3rd, 2nd, 4th] 。
```

# 题解

暴力：先排序，遍历输出。由于数组下标天然就可排序，从最大值依次减一。算法技巧：数组下标

```Java
class Solution {
    public String[] findRelativeRanks(int[] score) {
        int len = score.length;
        int max = -1;
        for(int i=0; i<len; i++)
            max = Math.max(max, score[i]);
        String[] s = new String[len];
        int[] a = new int[max+1];
        for(int i=0; i<len; i++)Q {
            a[score[i]] = i + 1;
        }
        int count = 1;
        for(int i=max; i>=0; i--) {
            if(a[i] != 0) {
                switch(count) {
                    case 1:
                        s[a[i]-1] = "Gold Medal";
                        break;
                    case 2:
                        s[a[i]-1] = "Silver Medal";
                        break;
                    case 3:
                        s[a[i]-1] = "Bronze Medal";
                        break;
                    default:
                        s[a[i]-1] = count + "";
                        break;
                }
                count++;
            }
        }

        return s;
    }
    
}
```