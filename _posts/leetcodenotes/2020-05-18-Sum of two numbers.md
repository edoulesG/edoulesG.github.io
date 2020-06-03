---
layout: notebook
title: "001.Sum of two numbers"
author: "martube"
categories: leetcode
tags: [leetcode-easy,Array,Hashmap]
image: 
bookclass: leetcode
---

# Sum of two numbers

## question

给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍。


示例:
```
给定 nums = [2, 7, 11, 15], target = 9

因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]
```

## tag

`Array` `Hashmap`

# test

生成0-100的随机数组 和target

```java
      //generate random number,put them into arrays
        int[] nums = new int[100];
        Random rand = new Random();
        for(int i=0;i<100;i++){
            nums[i] = rand.nextInt(101);
        }

        //generate target (pseudo-random)
        // first index
        int index1 = rand.nextInt(91);
        // second index
//        int index2 = rand.nextInt(101);
        int index2 = index1 + 6;

        int target = nums[index1] + nums[index2];


        System.out.println(target);

```


## my answer


### First

* brute force

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
       int resultindex1 = 0;
        int resultindex2 = 0;
        for(int i = 0;i < nums.length; i++){
            for(int j = 0;j < nums.length; j++){
                if(nums[i] + nums[j] == target && i != j){
                    resultindex1 = i;
                    resultindex2 = j;
//                    return new int[] {i,j};
                }
            }
        }
        return new int[] {resultindex1,resultindex2};
    }
}
```

哪怕是使用暴力解法，这段代码依旧有不足的地方

### Second

* brute force 优化版

```java 
class Solution {
    public int[] twoSum(int[] nums, int target) {
        for (int i = 0; i < nums.length; i++) {
            for (int j = i + 1; j < nums.length; j++) { //j=i+1 题目不能用两个相同的下标 
                if (nums[j] == target - nums[i]) { // 为什么用减法？
                    return new int[] { i, j };
                }
            }
        }
        throw new IllegalArgumentException("No two sum solution");
        //此异常属于继承与RuntimeExpcetion（运行时异常），最终都要捕获的，如果不捕获的话最后就会在控制台打印错误信息，并且程序终止了。
    }
}
```


### Third （Hash）

为了对运行时间复杂度进行优化，我们需要一种更有效的方法来检查数组中是否存在目标元素。如果存在，我们需要找出它的索引。**保持数组中的每个元素与其索引相互对应的最好方法是什么**？哈希表。

通过 **以空间换取速度**的方式，我们可以将查找时间从 O(n)O(n) 降低到 O(1)O(1)。哈希表正是为此目的而构建的，它支持以 近似 恒定的时间进行快速查找。我用“近似”来描述，是因为一旦出现冲突，查找用时可能会退化到 O(n)O(n)。但只要你仔细地挑选哈希函数，在哈希表中进行查找的用时应当被摊销为 O(1)O(1)。

一个简单的实现使用了两次迭代。在第一次迭代中，我们将每个元素的值和它的索引添加到表中。然后，在第二次迭代中，我们将检查每个元素所对应的目标元素（target - nums[i]）是否存在于表中。注意，该目标元素不能是 nums[i] 本身！

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {

        Map<Integer,Integer> hashmap = new HashMap<>();
        //第一次迭代
        for(int i = 0; i< nums.length; i++){
            hashmap.put(nums[i],i);
            //这里注意，与一般键值对不同，这里用了【可能重复】的项目来作为键。
            //导致了数据丢失的可能，但是从完成目的上来说，这个逻辑是对的，而且这个数据丢失，是不影响最终结果的。
        }

        for(int i=0;i<nums.length;i++){
            int complement = target - nums[i];
            //hashmap.containsKey(complement) 检查这个hashmao中是否包含这个键值
            if(hashmap.containsKey(complement) && hashmap.get(complement) != i){
                    return new int[] {i,hashmap.get(complement)};
            }
        }

        throw new IllegalArgumentException("Not two sum solution");
    }
}

```

### 第一遍小结

对于 【检查数组中是否存在目标元素】，最常用，也最不会出错的方法，就是使用迭代，一个一个检查过去。

而【在某种特定条件】下，比如 常见的 一个【基本类型】数组中，元素不重复。

这样就完全可以使用 建立索引的方式，来大大加速这个寻找过程。

> TODO 【数据结构】---哈希表查找算法?

而在本题中，虽然数组中可能存在相同的元素，但是建立索引不影响最终结果，这样的情形也是可以的。


##### 第一遍的延申思考

数组元素是int 类型的，我们可以这么玩；那String类型呢？对象呢？

似乎也是可以的....






