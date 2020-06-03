---
layout: notebook
title: "002.Median of Two Sorted Arrays"
author: "martube"
categories: leetcode
tags: [leetcode-hard,Array,binary-search,divide-and-conquer]
image: 
bookclass: leetcode
---

# Median of Two Sorted Arrays

## question

给定两个大小为 m 和 n 的正序（从小到大）数组 nums1 和 nums2。

请你找出这两个正序数组的中位数，并且要求算法的时间复杂度为 O(log(m + n))。

你可以假设 nums1 和 nums2 不会同时为空。

## tag

`Array` `binary-search` `divide-and-conquer`

**示例 1**:

```
nums1 = [1, 3]
nums2 = [2]

则中位数是 2.0

```

**示例 2**:

```
nums1 = [1, 2]
nums2 = [3, 4]

则中位数是 (2 + 3)/2 = 2.5

```


## test

生成两个随机数组 参考Sum of two number

## my answer

### First

* 思路:先将两个数组拼接为一个数组，然后对其进行从小到大的sort，最后将问题变成在一个数组中寻找中位数（估计时间复杂度会超）关键点在于数组的排序

```java
int[] nums1 = new int[]{0,8,11};
int[] nums2 = new int[]{1,6,9,30};

//将两个数组拼接为一个数组
int[] allnums = new int[nums1.length + nums2.length];
System.arraycopy(nums1,0,allnums,0,nums1.length);
System.arraycopy(nums2,0,allnums,nums1.length,nums2.length);

// 冒泡排序 -- 光是执行这一段的时间复杂度就已经是 O(n2)了
for(int i=0;i<allnums.length-1;i++){   
   for(int j=i+1;j<allnums.length;j++){   
       if (allnums[i]>allnums[j]){   
           int temp=allnums[i];   
           allnums[i]=allnums[j];   
           allnums[j]=temp;   
       }
    }
}

//寻找中位数下标
if(allnums.length % 2 == 1){
    //中位数下标为 1个
    //length-1/2 + 1 -1
    int m = (allnums.length-1)/2;
    return allnums[m];
}else{
    //中位数下标为 2个
    //length/2 -1  length/2+1 -1
    int m1 = allnums.length/2 - 1;
    int m2 = allnums.length/2;
    return Math.round((allnums[m1] + allnums[m2])*100/2)/100; 
}

```
> 时间复杂度为O((n+m)2)....

脑海里实在没有更效率的办法了，看看别人的解法吧

### Second

还是合并后找到中位数，不过用到一个更好的排序方式 -- 归并排序

> 归并排序:将两个 **有序数列**合并成一个有序数列，我们称之为"归并"。

两个有序数组的合并也是归并排序中的 **一部分** ，然后根据奇数，还是偶数，返回中位数。

```java

public double findMedianSortedArrays(int[] nums1, int[] nums2) {
    int[] nums;
    int m = nums1.length;
    int n = nums2.length;

    //m 为空时
    if (m == 0) {
        if (n % 2 == 0) {
            return (nums2[n / 2 - 1] + nums2[n / 2]) / 2.0;
        } else {

            return nums2[n / 2];
        }
    }
    //n 为空时
    if (n == 0) {
        if (m % 2 == 0) {
            return (nums1[m / 2 - 1] + nums1[m / 2]) / 2.0;
        } else {
            return nums1[m / 2];
        }
    }

    //两个有序数组的 归并排序
    int count = 0;
    int i = 0, j = 0;
    while (count != (m + n)) {
        if (i == m) {
            while (j != n) {
                nums[count++] = nums2[j++];
            }
            break;
        }
        if (j == n) {
            while (i != m) {
                nums[count++] = nums1[i++];
            }
            break;
        }

        if (nums1[i] < nums2[j]) {
            nums[count++] = nums1[i++];
        } else {
            nums[count++] = nums2[j++];
        }
    }

    //合并完成后 找到中位数
    if (count % 2 == 0) {
        return (nums[count / 2 - 1] + nums[count / 2]) / 2.0;
    } else {
        return nums[count / 2];
    }
}
```
> 时间复杂度为O((n+m))，算是一个优化版的暴力法，但还是达不到题目要求的O(log(m + n))


### 第一遍小结

1.在学习第二种解的时候，了解到一个新的排序方式： 归并排序，两个有序数组的合并是归并排序中的 **一部分** ，但是对于一个完整的乱序整数列来说，归并排序有更完成的写法（https://zhuanlan.zhihu.com/p/36075856）

在本题中，归并排序并不复杂，因为是两个已经排序好的数列，每两个元素一组进行比较。


2. 这道题并没有结束，题目要求是把复杂度降到log级别，之后再回过头来看









