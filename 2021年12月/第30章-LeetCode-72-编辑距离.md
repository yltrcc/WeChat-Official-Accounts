# 每日一句
A flower cannot blossom without sunshine, and man cannot live without love.  
花没有阳光就不能盛开，人没有爱就不能生存。



### 题目来源

[https://leetcode-cn.com/problems/edit-distance/](https://leetcode-cn.com/problems/edit-distance/)

### 题目描述

```text
给你两个单词 word1 和 word2，请你计算出将 word1 转换成 word2 所使用的最少操作数 。

你可以对一个单词进行如下三种操作：

插入一个字符
删除一个字符
替换一个字符
```

### 示例

```text
示例一：
输入：word1 = "horse", word2 = "ros"
输出：3
解释：
horse -> rorse (将 'h' 替换为 'r')
rorse -> rose (删除 'r')
rose -> ros (删除 'e')

示例二：
输入：word1 = "intention", word2 = "execution"
输出：5
解释：
intention -> inention (删除 't')
inention -> enention (将 'i' 替换为 'e')
enention -> exention (将 'n' 替换为 'x')
exention -> exection (将 'n' 替换为 'c')
exection -> execution (插入 'u')

```

### 题解

#### 题解1: 动态规划算法

具体算法思想请看：动态规划算法


```Java
/**
*  步骤一：定义数组元素的含义
*    定义dp[m][n] 将 word1 转换成 word2 所使用的最少操作数, m, n分别表示两个单词的长度
*  步骤二：找出初始值并设置边界条件
*    dp[0][0] = 0, 分别处于首行和首列的位置均可以直接求出值
*    dp[i][0] = i; m>i>=1  dp[0][i] = i; n>i>=1, m,n 表示两个单词的长度
*  步骤三：找出数组元素之间的关系式
*    结果：
*      1. A==B时：dp[i][j] = dp[i-1][j-1]; i>=1,j>=1
*      2. A!=B时：
*        在单词 A 中插入一个字符：dp[i][j] = dp[i][j-1] + 1;
*        在单词 A 中删除一个字符: dp[i][j] = dp[i-1][j] + 1;
*        修改单词A的一个字符：dp[i][j] = dp[i-1][j-1] + 1;
*        so,最小操作数为三个操作的最小值：dp[i][j] = min(dp[i][j-1], dp[i-1][j], dp[i-1][j-1]) i>=1,j>=1
**/
class Solution {

    public int min(int a, int b, int c) {
        return Math.min(a, Math.min(b, c));
    }

    public int minDistance(String word1, String word2) {
        int m = word1.length();
        int n = word2.length();
        int[][] dp = new int[510][510];
        //初始值以及边界条件
        if(m * n == 0) {
            return n+m;
        }
        dp[0][0] = 0;
        for(int i=1; i<=m; i++) {
            dp[i][0] = dp[i-1][0] + 1;
        }
        for(int i=1; i<=n; i++) {
            dp[0][i] = dp[0][i-1] + 1;
        }
        //关系式
        for(int i=1; i<=m; i++) {
            for(int j=1; j<=n; j++) {
                if(word1.charAt(i-1) != word2.charAt(j-1)) {
                    dp[i][j] = min(dp[i-1][j], dp[i-1][j-1], dp[i][j-1]) + 1;
                }else {
                    dp[i][j] = dp[i-1][j-1];
                }
            }
        }
        return dp[m][n];
    }
}

```

```Java

/**
*  打印路径
**/
class Solution {

    int[][] dp = new int[510][510];

    public int min(int a, int b, int c) {
        return Math.min(a, Math.min(b, c));
    }

    public int minDistance(String word1, String word2) {
        int m = word1.length();
        int n = word2.length();
        
        //初始值以及边界条件
        if(m * n == 0) {
            return n+m;
        }
        dp[0][0] = 0;
        for(int i=1; i<=m; i++) {
            dp[i][0] = dp[i-1][0] + 1;
        }
        for(int i=1; i<=n; i++) {
            dp[0][i] = dp[0][i-1] + 1;
        }
        //关系式
        for(int i=1; i<=m; i++) {
            for(int j=1; j<=n; j++) {
                if(word1.charAt(i-1) != word2.charAt(j-1)) {
                    dp[i][j] = min(dp[i-1][j], dp[i-1][j-1], dp[i][j-1]) + 1;
                }else {
                    dp[i][j] = dp[i-1][j-1];
                }
            }
        }
       
        printPath(word1, word2, m, n);
        return dp[m][n];
    }

    public int printPath(String word1, String word2, int i, int j) {
        if(i == j & i==0) {
            return 0;
        }
        if(word1.charAt(i-1) == word2.charAt(j-1)) {
  
            System.out.println("A: " + word1.substring(0, i) + ";B: " + word2.substring(0, j) + " 末尾字符相同");
            return printPath(word1, word2, i-1, j-1);
        }else {
            
            if(dp[i-1][j] == dp[i][j] - 1) {
                System.out.println("A: " + word1.substring(0, i) + " 删除一个字符: " + word1.charAt(i-1) +" 变成 B: " + word2.substring(0, j));
                return printPath(word1, word2, i-1, j);
            }
            if(dp[i][j-1]== dp[i][j] - 1) {
                System.out.println("A: " + word1.substring(0, i) + " 插入一个字符: " + word2.charAt(j-1) +" 变成 B: " + word2.substring(0, j));
                return printPath(word1, word2, i, j-1);
            }
            if(dp[i-1][j-1] == dp[i][j] - 1) {
                System.out.println("A: " + word1.substring(0, i) + " 修改一个字符变成 B: " + word2.substring(0, j));
                return printPath(word1, word2, i-1, j-1);
            }
        }

        return 0;
    }

}
```

```Java
/**
*  优化：二维dp转一维dp
**/
class Solution {

    public int min(int a, int b, int c) {
        return Math.min(a, Math.min(b, c));
    }

    public int minDistance(String word1, String word2) {
        int m = word1.length();
        int n = word2.length();
        int[] dp = new int[n + 1];

        //初始值以及边界条件
        if(m * n == 0) {
            return n+m;
        }
        dp[0] = 0;
        //初始化首行
        for(int i=1; i<=n; i++) {
            dp[i] = i;
        }
        //关系式
        for(int i=1; i<=m; i++) {
            int pre = dp[0];
            dp[0] = i;
            for(int j=1; j<=n; j++) {
                int tmp = pre;
                pre = dp[j];
                if(word1.charAt(i-1) == word2.charAt(j-1)) {
                    dp[j] = tmp;
                }else {
                    dp[j] = min(dp[j], dp[j-1], tmp) + 1;
                }

            }
        }
        return dp[n];
    }

    

}
```

# 美文佳句

梦想不在纸上、嘴上，而在抽屉里、心里；梦想不是滔滔不绝，而是专注、专注，暂时保持沉默。
