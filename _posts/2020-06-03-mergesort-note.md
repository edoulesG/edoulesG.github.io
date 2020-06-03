---
layout: post
title: "归并排序"
author: joonwhee
categories: technology
tags: [sort,Reprint,tool,Array]
image: cards.jpg
---

# 归并排序
> 本文大部分知识点来源于知乎专栏：[Java必学知识](https://zhuanlan.zhihu.com/joonwhee)
> 
> [归并排序](https://zhuanlan.zhihu.com/p/36075856)


#### 基本思想

归并排序的主要思想是分治法。主要过程是：

将n个元素从中间切开，**分成两部分**。（左边可能比右边多1个数）
将步骤1分成的两部分，再分别进行递归分解。直到所有部分的元素个数都为1。
从最底层开始逐步合并两个排好序的数列。

##### 思考

考虑一个问题，如何将两个有序数列合并成一个有序数列？

很简单，由于两个数列都已经有序，我们只需从两个数列的低位轮番拿出各自最小的数来PK就就行了，输的一方为小值，将这个值放入临时数列，然后输的一方继续拿出一个值来PK，直至有一方没有元素后，将另一方的所有元素依次接在临时数列后面即可。

此时，临时数列为两个数列的有序合并。

归并排序中的归并就是利用这种思想。对应的代码如下：

```java
/**
 * 合并两个有序数列 【的组合数列】 
 * array[first]~array[mid]为第一组
 * array[mid+1]~array[last]为第二组
 * temp[]为存放两组比较结果的临时数组
 */
private static void mergeArray(int array[], int first, int mid, int last, int temp[]) {
    int i = first, j = mid + 1; // i为第一组的起点, j为第二组的起点
    int m = mid, n = last; // m为第一组的终点, n为第二组的终点
    int k = 0; // k用于指向temp数组当前放到哪个位置
    while (i <= m && j <= n) { // 将两个【有序序列】循环比较, 填入数组temp
        if (array[i] <= array[j])
            temp[k++] = array[i++];
        else
            temp[k++] = array[j++];
    }
    while (i <= m) { // 如果比较完毕, 第一组还有数剩下, 则全部填入temp
        temp[k++] = array[i++];
    }
    while (j <= n) {// 如果比较完毕, 第二组还有数剩下, 则全部填入temp
        temp[k++] = array[j++];
    }
    for (i = 0; i < k; i++) {// 将排好序的数填回到array数组的对应位置
        array[first + i] = temp[i];
    }
}

```
   
其实这段思想如果在一个特殊情况--有两个 **分开**的有序数组，要将其合并成一个有序数组，可以这么写：

```java

public int[] mergeAndSort2Array(int[] nums1, int[] nums2) {
    int[] nums;
    int m = nums1.length;
    int n = nums2.length;

    //两个有序数组的 归并排序
    int count = 0;
    int i = 0, j = 0;
    while (count != (m + n)) {

        // 当有一方拿完了 把另一方剩下的加在数组末尾
        if (i == m) {
            while (j != n) {
                nums[count++] = nums2[j++];
            }
            break;
        }
        // 当有一方拿完了 把另一方剩下的加在数组末尾
        if (j == n) {
            while (i != m) {
                nums[count++] = nums1[i++];
            }
            break;
        }
        // 没拿完的情况 进行比较
        if (nums1[i] < nums2[j]) {
            nums[count++] = nums1[i++];
        } else {
            nums[count++] = nums2[j++];
        }
    }

    return nums;
}
```
#### 示意图

![mergeSort](/assets/img/technology/20200603mergeSort.webp)

#### 代码

```java
public class MergeSort {
    public static void mergeSort(int[] array) {
        if (array == null || array.length == 0)
            return;
        int[] temp = new int[array.length];
        mergeSort(array, 0, array.length - 1, temp);
 
    }
    // 归并
    private static void mergeSort(int array[], int first, int last, int temp[]) {
        if (first < last) {
            int mid = (first + last) / 2;
            mergeSort(array, first, mid, temp); // 递归归并左边元素
            mergeSort(array, mid + 1, last, temp); // 递归归并右边元素
            mergeArray(array, first, mid, last, temp); // 再将二个有序数列合并
        }
    }
 
    /**
     * 合并两个有序数列
     * array[first]~array[mid]为第一组
     * array[mid+1]~array[last]为第二组
     * temp[]为存放两组比较结果的临时数组
     */
    private static void mergeArray(int array[], int first, int mid, int last, int temp[]) {
        int i = first, j = mid + 1; // i为第一组的起点, j为第二组的起点
        int m = mid, n = last; // m为第一组的终点, n为第二组的终点
        int k = 0; // k用于指向temp数组当前放到哪个位置
        while (i <= m && j <= n) { // 将两个有序序列循环比较, 填入数组temp
            if (array[i] <= array[j])
                temp[k++] = array[i++];
            else
                temp[k++] = array[j++];
        }
        while (i <= m) { // 如果比较完毕, 第一组还有数剩下, 则全部填入temp
            temp[k++] = array[i++];
        }
        while (j <= n) {// 如果比较完毕, 第二组还有数剩下, 则全部填入temp
            temp[k++] = array[j++];
        }
        for (i = 0; i < k; i++) {// 将排好序的数填回到array数组的对应位置
            array[first + i] = temp[i];
        }
    }
}




```


#### 适用场景
归并排序的时间复杂度为O(nlogn),归并排序需要一个跟待排序数组同等空间的临时数组，因此，使用归并排序时需要考虑是否有空间上的限制。

如果没有空间上的限制，归并排序是一个不错的选择。在本人的电脑测试，100万的随机数字，归并排序大约耗时150毫秒。