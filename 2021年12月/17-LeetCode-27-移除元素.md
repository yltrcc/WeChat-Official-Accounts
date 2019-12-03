# 每日一句
I'm trying everything.
我什么都要试试。

题目链接

[https://leetcode-cn.com/problems/remove-element/](https://leetcode-cn.com/problems/remove-element/)

# 题目描述

给你一个数组 nums 和一个值 val，你需要 原地 移除所有数值等于 val 的元素，并返回移除后数组的新长度。

不要使用额外的数组空间，你必须仅使用 O(1) 额外空间并 原地 修改输入数组。

元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。

**说明：**





为什么返回数值是整数，但输出的答案是数组呢?

请注意，输入数组是以「引用」方式传递的，这意味着在函数里修改输入数组对于调用者是可见的。

你可以想象内部操作如下:

```text
// nums 是以“引用”方式传递的。也就是说，不对实参作任何拷贝
int len = removeElement(nums, val);

// 在函数里修改输入数组对于调用者是可见的。
// 根据你的函数返回的长度, 它会打印出数组中 该长度范围内 的所有元素。
for (int i = 0; i < len; i++) {
    print(nums[i]);
}

```



# 题目示例

```text
示例 1：

输入：nums = [3,2,2,3], val = 3
输出：2, nums = [2,2]
解释：函数应该返回新的长度 2, 并且 nums 中的前两个元素均为 2。
你不需要考虑数组中超出新长度后面的元素。
例如，函数返回的新长度为 2 ，而 nums = [2,2,3,3] 或 nums = [2,2,0,0]，也会被视作正确答案。

示例 2：

输入：nums = [0,1,2,2,3,0,4,2], val = 2
输出：5, nums = [0,1,4,0,3]
解释：函数应该返回新的长度 5, 并且 nums 中的前五个元素为 0, 1, 3, 0, 4。
注意这五个元素可为任意顺序。你不需要考虑数组中超出新长度后面的元素。
```



# 分析

双指针法模板题。

具体算法请参考：双指针法



# 题解

```Java
class Solution {
    public int removeElement(int[] nums, int val) {
        
        if(nums==null || nums.length == 0) {
            return 0;
        }
        if(nums.length == 1) {
            return nums[0] == val ? 0 : 1;
        }
        int fast = 0;
        int slow = 0;
        int length = nums.length;
        while(fast != length) {
            if (nums[fast] != val) {
                nums[slow++] = nums[fast];
            }
            fast ++;
        }
        return slow;
    }
}
```




# 美文佳句

在你面前，我无法做到完美，可我渴望和你一起书写属于我们的完美童话；我无法对你做到俯首帖耳，可我愿意站成一棵挺立的小树，哪怕只是陪衬你的风景；我无法承担你所有的苦痛，可我希望走进你的心里，读懂你的心灵，无言地守着你，疼着你。