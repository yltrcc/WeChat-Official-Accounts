# 题目链接

[https://leetcode-cn.com/problems/super-pow/](https://leetcode-cn.com/problems/super-pow/)

# 题目描述

你的任务是计算 `a^b` 对 `1337` 取模，`a` 是一个正整数，`b` 是一个非常大的正整数且会以数组形式给出。

# 题目示例

```text
示例 1：

输入：a = 2, b = [3]
输出：8
示例 2：

输入：a = 2, b = [1,0]
输出：1024
示例 3：

输入：a = 1, b = [4,3,3,8,5,2]
输出：1
示例 4：

输入：a = 2147483647, b = [2,0,0]
输出：1198
```

# 提示

*   `1 <= a <= 231 - 1
    `*   `1 <= b.length <= 2000
    `*   `0 <= b[i] <= 9
    `*   `b` 不含前导 0

# 分析

这么大的数一看就不能暴力来求解了，多半是个数学问题。

# 题解

## 题解一：倒序遍历

设 aa 的幂次为 nn。根据题意，nn 从最高位到最低位的所有数位构成了数组 bb。记数组 bb 的长度为 mm，有

$$
n=\sum_{i=0}^{m-1} 10^{m-1-i} \cdot b_{i}
$$

由于 $a^(x+y) = a ^ x * a^y$以及 $a^(x*y) = (a^x)^y$，得

![](https://secure1.wostatic.cn/static/qJ7sTgcM8hBDny3ukyqe1F/QQ截图20211206052257.png)



可以根据如下等式计算上式括号内的部分：



![](https://secure1.wostatic.cn/static/dR9X8c9DmVxH4Sz2BZijha/QQ截图20211206052331.png)



我们可以从$a^1
$开始，递推出 $a^(10^k)$

```Java
class Solution {
    static final int MOD = 1337;

    public int superPow(int a, int[] b) {
        int ans = 1;
        for (int i = b.length - 1; i >= 0; --i) {
            ans = (int) ((long) ans * pow(a, b[i]) % MOD);
            a = pow(a, 10);
        }
        return ans;
    }

    public int pow(int x, int n) {
        int res = 1;
        while (n != 0) {
            if (n % 2 != 0) {
                res = (int) ((long) res * x % MOD);
            }
            x = (int) ((long) x * x % MOD);
            n /= 2;
        }
        return res;
    }
}
```

## 题解二：秦九韶算法（正序遍历）

![](https://secure1.wostatic.cn/static/abcve8p7RmHVjSfSVcQZHL/QQ截图20211206052729.png)



```Java
class Solution {
    static final int MOD = 1337;

    public int superPow(int a, int[] b) {
        int ans = 1;
        for (int e : b) {
            ans = (int) ((long) pow(ans, 10) * pow(a, e) % MOD);
        }
        return ans;
    }

    public int pow(int x, int n) {
        int res = 1;
        while (n != 0) {
            if (n % 2 != 0) {
                res = (int) ((long) res * x % MOD);
            }
            x = (int) ((long) x * x % MOD);
            n /= 2;
        }
        return res;
    }
}
```

