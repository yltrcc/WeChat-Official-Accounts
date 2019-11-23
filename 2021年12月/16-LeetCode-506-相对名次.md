# 每日一句
I just have to live my truth.
我必须活出真实的自己。

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




# 美文佳句

你总要相信，无论今天过得有多难，它总会过去；无论今天过得有多好，它也会过去。当你回过头看来时可能会有这样的感受，其实日子本没有好坏之分，不同的无非是你有没有留下成长的足迹和记忆。

人生没有捷径，路要一步一步走，山要一座一座翻，坡要一个一个过。今天的苦你逃避了，明天就要吃更大的苦；今天的难你跳过了，明天你就会面对更大的难。那些迎难而上的日子，或许恰恰是你成长路上最好的养料。

曾看过这样一段话：把每一个黎明当作你生命的开始，把每一个黄昏当作你生命的小结，让我们都能为自己留下一点可爱的脚印和心灵得到充实的痕迹。

与朋友们共勉。