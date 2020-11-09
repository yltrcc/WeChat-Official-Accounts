# 每日一句
No victory comes without a price.
凡是成功就要付出代价。

# 题目链接

[http://codeforces.com/problemset/problem/743/C](http://codeforces.com/problemset/problem/743/C)

# Description

Vladik and Chloe decided to determine who of them is better at math. Vladik claimed that for any positive integer *n* he can represent fraction  as a sum of three distinct positive fractions in form .

Help Vladik with that, i.e for a given *n* find three distinct positive integers *x*, *y* and *z* such that . Because Chloe can't check Vladik's answer if the numbers are large, he asks you to print numbers not exceeding 109.

If there is no such answer, print -1.

# Input

The single line contains single integer *n* (1 ≤ *n* ≤ 104).


# Output

If the answer exists, print 3 distinct numbers *x*, *y* and *z* (1 ≤ *x*, *y*, *z* ≤ 109, *x* ≠ *y*, *x* ≠ *z*, *y* ≠ *z*). Otherwise print -1.

If there are multiple answers, print any of them.

# Examples

```text
实例一：
3
2 7 42

实例二：
7
7 8 56

```



# Thinking

从样例二可以看出本题的构造方法。

显然 n，n+1,n(n+1) 为一组合法解。特殊地，当 n = 1 时，无解，这是因为 n+1 与 n(n+1) 此时相等。

至于构造思路是怎么产生的，大概就是观察样例加上一点点数感了吧。此题对于数学直觉较强的人来说并不难。

# answer

```C++
#include<bits/stdc++.h>
using namespace std;

long long n;
int main()
{
    cin>>n;
    if(n==1)cout<<"-1"<<endl;
    else cout<<n<<" "<<n+1<<" "<<n*(n+1)<<endl;
}
```

# 美文佳句
没有谁的生活是一帆风顺的。在凡常的日子里，我们无可避免地要面对许多烦恼和不如意，即便没有近忧也可能会有远虑。

遇到风便是风，遇到雨便是雨。我们无法决定天气的好与坏，但我们可以决定用怎样的心态和心情去对待每一天。

人在年少时总觉得日子太长，一天仿佛是十年的光景。但在年长后就会觉得日子变短，十年仿若在弹指一挥间。

在这个世上，每一天的光阴都不应该被挥霍。你好好去耕耘一天，就得一天的收获；你好好去努力一天，就得一天的成绩。若你虚掷和浪费，最终时光反馈给你的就只会是终生无法弥补的遗憾和悔恨。