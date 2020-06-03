---
layout: post
title: "龟排三兄弟"
author: joonwhee
categories: technology
tags: [sort,Reprint,tool,Array]
image: home-bg.jpg
---

# 龟排三兄弟

> 本文大部分知识点来源于知乎专栏：[Java必学知识](https://zhuanlan.zhihu.com/joonwhee)

### [冒泡排序](https://zhuanlan.zhihu.com/p/35189443)

#### 思想

冒泡排序算法的运作如下：

比较两个相邻的数，如果前面的数大于后面的数，则将这两个数交换位置。第一次遍历后，最大的数会被放到数组的最后位置，即array[length - 1]。
第二次遍历时跳过最后一个元素，因为该元素通过第一次遍历已经确定是最大值。
持续每次对越来越少的元素重复上面的步骤，直到没有任何一对数字需要比较。

#### 代码

```java
public static void bubbleSort(int[] arr){
        for (int i = 0;i < arr.length - 1;i++){
            for (int j = 0;j < arr.length - 1 - i;j++){
                if(arr[j] > arr[j+1]){
                    int temp = arr[j];
                    arr[j] = arr[j+1];
                    arr[j+1] = temp;
                }
            }
        }
    }
```
#### 适用场景

由于冒泡排序的时间复杂度为：O(n^2)。因此，在进行量比较大的排序时，最好不要采用冒泡排序。经过简单的测试，在1000个数字以内的排序，用冒泡排序是可以接受的。1000个随机数使用冒泡排序耗时一般在5毫秒以内，但是当数字个数达到10000个时，耗时会达到100+毫秒， **简直是灾难**。


### [插入排序](https://zhuanlan.zhihu.com/p/35328552)

#### 思想

将一个数插入一个已经排好序的数据中。

第一次循环时，从第2个数开始处理。我们将第1个数作为已经排好序的数据：当第2个数 > 第1个数时，将第2个数放在第1个数后面一个位置；否则，将第2个数放在第1个数前面。此时，前两个数形成了一个有序的数据。
第二次循环时，我们处理第3个数。此时，前两个数形成了一个有序的数据：首先比较第3个数和第2个数，当第3个数 > 第2个数时，将第3个数放在第2个数后面一个位置并结束此次循环；否则，再和第1个数比较。如果第3个数 > 第1个数，则将第3个数插入第1个数和第2个数中间；否则，第3个数 < 第1个数，则将第3个数放在第1个数前面。此时，前三个数形成了一个有序的数据。
后续的数据同理处理，直至结束。


#### 代码
```java

public static void insertSort(int[] arr){
        for (int i = 1;i < arr.length;i++){
            //当arr[i] 比arr[i-1] 小，才处理
            if(arr[i] < arr[i-1]){
                int temp = arr[i];
                int j = i;
                while (j > 0 && temp < arr[j-1]){
                    arr[j] = arr[j-1];
                    j--;
                }
                arr[j] = temp;
            }
        }
    }
```
#### 适用场景

时间复杂度为：O(n^2)。
插入排序和冒泡排序一样。在进行量比较大的排序时，最好不要使用。在1000个数字以内的排序，用插入排序是可以接受的。1000个随机数使用插入排序耗时一般在5毫秒以内，但是当数字个数达到10000个时，耗时会达到30+毫秒，有比较明显的耗时，需要慎重使用。





### [选择排序](https://zhuanlan.zhihu.com/p/35611378)

#### 思想

每一次从待排序的数据元素中选出最小的一个元素，存放在序列的起始位置，直到全部待排序的数据元素排完。

第一次循环时，i = 0 首先将代表着“最小值下标”的minIndex赋值为第一个数字的数组下标，即minIndex = 0。

然后从第2个元素开始与array[minIndex]进行比较，如果有比array[minIndex]小的数，则将minIndex赋值为这个数的下标。当所有元素比较完后，再将array[0]与array[minIndex]交换位置，则arary[0]的值即为最小的数。
第二次循环时，从array[1]开始处理，最后比较出最小的数与array[1]交换位置，过程同步骤1。
直至处理完倒数第二个，结束处理。

#### 代码
```java
public static void selectSort(int[] arr){
        for (int i = 0;i < arr.length -1 ;i++){
            //记录当次遍历最小数的位置

            int minIndex = i;
            for (int j = i + 1;j < arr.length; j++) {
                // 如果arr[j] 比arr[minIndex] 小,则将minIndex 修改为j
                if(arr[j] < arr[minIndex]){
                    minIndex = j;
                }
            }
            if(i != minIndex){
                //将当次遍历的最小值放到对应位置
                int temp = arr[i];
                arr[i] = arr[minIndex];
                arr[minIndex] = temp;
            }
        }
    }
```

#### 适用场景

选择排序和冒泡排序、插入排序一样。在进行量比较大的排序时，最好不要使用。在1000个数字以内的排序，用选择排序是可以接受的。1000个随机数使用选择排序耗时一般在5毫秒以内，但是当数字个数达到10000个时，耗时会达到30+毫秒，有比较明显的耗时，需要慎重使用。