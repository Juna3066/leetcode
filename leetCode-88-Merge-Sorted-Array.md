# 题目描述（简单难度）

![](https://windliang.oss-cn-beijing.aliyuncs.com/88.jpg)

给两个有序数组，把第二个数组合并到第一个数组中，保持有序。可以注意到第一个数组已经为我们多开辟了第二个数组所需要的空间。

# 解法一 直接法

简单粗暴，nums1 作为被插入的数组，然后遍历 nums2。用两个指针 i 和 j ，i 指向 nums1 当前判断的数字，j 指向 num2 当前遍历的数字。如果 j 指向的数字小于 i 指向的数字，那么就做插入操作。否则的话后移 i ，找到需要插入的位置 。

```java
1 2 3 0 0 0  | 2 5 6  //当前 j 指向的数字不小于 i 指向的数字，无需插入，后移 i
^              ^
i              j

1 2 3 0 0 0  | 2 5 6  //当前 j 指向的数字不小于 i 指向的数字，无需插入后移 i
  ^            ^
  i            j

1 2 3 0 0 0  | 2 5 6  //当前 j 指向的数字小于 i 指向的数字，将当前数字插入到 nums1 中
    ^          ^
    i          j
    
1 2 2 3 0 0  | 2 5 6  //插入完成后，j 进行后移，同时由于在 i 前边插入了数字，i 后移回到原来的数字
    ^          ^
    i          j 
    
1 2 2 3 0 0  | 2 5 6  //当前 j 指向的数字不小于 i 指向的数字，无需插入后移 i
      ^          ^
      i          j 

1 2 2 3 0 0  | 2 5 6  //由于 nums1 完成遍历，将剩余的 nums2 直接插入
        ^        ^
        i        j 
        
1 2 2 3 5 6  | 2 5 6  
          ^        ^
          i        j 
```



```java
public void merge(int[] nums1, int m, int[] nums2, int n) {
    int i = 0, j = 0;
    //遍历 nums2
    while (j < n) {
        //判断 nums1 是否遍历完
        //（nums1 原有的数和当前已经插入的数相加）和 i 进行比较
        if (i == m + j) {
            //将剩余的 nums2 插入
            while (j < n) {
                nums1[m + j] = nums2[j];
                j++;
            }
            return;
        }
        //判断当前 nums2 是否小于 nums1
        if (nums2[j] < nums1[i]) {
            //nums1 后移数组，空出位置以便插入
            for (int k = m + j; k > i; k--) {
                nums1[k] = nums1[k - 1];
            }
            nums1[i] = nums2[j];
            //i 和 j 后移
            j++;
            i++;
        //当前 nums2 不小于 nums1， i 后移
        }else{ 
       	    i++;  
        }
    }
}
```

时间复杂度：极端情况下，如果每次都需要插入，那么是 O（n²）。

空间复杂度：O（1）。

# 解法二

两个有序数组的合并，其实就是归并排序中的一个步骤。回想下，我们当时怎么做的。

我们当时是新开辟一个和 nums1 + nums2 等大的空间，然后用两个指针遍历 nums1 和 nums2，依次选择小的把它放到新的数组中。

这道题中，nums1 其实就是我们最后合并好的大数组，但是如果 nums1 当作上述新开辟的空间，那原来的 nums1 的数字放到哪里呢？放到 nums1 的末尾。这样我们就可以完全按照归并排序中的思路了，用三个指针就可以了。

```java
1 2 3 0 0 0 0 | 2 5 6 7 //将 nums1 的数据放到 nums1 的末尾
   
1 2 3 0 1 2 3 | 2 5 6 7 //i 和 j 分别指向两组数据的开头，k 指向代插入位置
^       ^       ^      	    
k       i       j

1 2 3 0 1 2 3 | 2 5 6 7 //此时 i 指向的数据小，把它插入，然后 i 后移，k 后移
^       ^       ^      	    
k       i       j

1 2 3 0 1 2 3 | 2 5 6 7 //此时 i 指向的数据小，把它插入，然后 i 后移，k 后移
  ^       ^     ^      	    
  k       i     j
  
1 2 3 0 1 2 3 | 2 5 6 7 //此时 j 指向的数据小，把它插入，然后 j 后移，k 后移
    ^       ^   ^      	    
    k       i   j
    
1 2 2 0 1 2 3 | 2 5 6 7 //此时 i 指向的数据小，把它插入，然后 i 后移，k 后移
      ^     ^     ^      	    
      k     i     j
      
1 2 2 3 1 2 3   | 2 5 6 7 //此时 i 遍历完，把 nums2 全部加入
        ^     ^     ^      	    
        k     i     j
        
1 2 2 3 5 6 7   | 2 5 6 7 
      
```

```java
public void merge(int[] nums1, int m, int[] nums2, int n) {
    //将 nums1 的数字全部移动到末尾
    for (int count = 1; count <= m; count++) {
        nums1[m + n - count] = nums1[m - count];
    }
    int i = n; //i 从 n 开始
    int j = 0; 
    int k = 0;
    //遍历 nums2
    while (j < n) {
        //如果 nums1 遍历结束，将 nums2 直接加入
        if (i == m + n) {
            while (j < n) {
                nums1[k++] = nums2[j++];
            }
            return;
        }
        //哪个数小就对应的添加哪个数
        if (nums2[j] < nums1[i]) {
            nums1[k] = nums2[j++];
        } else {
            nums1[k] = nums1[i++];
        }
        k++;
    }
}
```

时间复杂度： O（n）。

空间复杂度：O（1）。

可以注意到，我们只考虑如果 nums1 遍历结束，将 nums2 直接加入。为什么不考虑如果 nums2 遍历结束，将 nums1 直接加入呢？因为我们最开始的时候已经把 nums1 全部放到了末尾，所以不需要再赋值了。

# 解法三

本以为自己的解法二已经很机智了，直到看到了[这里](<https://leetcode.com/problems/merge-sorted-array/discuss/29522/This-is-my-AC-code-may-help-you>)，发现了一个神仙操作。

解法二中我们的思路是，把 nums1 当作合并后的大数组，依次从两个序列中选较小的数，此外，为了防止 nums1 原有的数字被覆盖，首先先把他放到了末尾。

那么，我们为什么不从 nums1 的末尾开始，依次选两个序列末尾较大的数插入呢？同样是 3 个指针，只不过变成哪个数大就对应的添加哪个数。

```java
public void merge3(int[] nums1, int m, int[] nums2, int n) {
    int i = m - 1; //从末尾开始
    int j = n - 1; //从末尾开始
    int k = m + n - 1; //从末尾开始
    while (j >= 0) {
        if (i < 0) {
            while (j >= 0) {
                nums1[k--] = nums2[j--];
            }
            return;
        }
        //哪个数大就对应的添加哪个数。
        if (nums1[i] > nums2[j]) {
            nums1[k--] = nums1[i--];
        } else {
            nums1[k--] = nums2[j--];
        }
    }
}
```

时间复杂度： O（n）。

空间复杂度：O（1）。

# 总

这道题看起来简单，但用到的思想很经典了。解法二中充分利用已有空间的思想，以及解法三中逆转我们的惯性思维，给定的数组从小到大，然后惯性上习惯从小到大，但如果逆转过来，从大的选，简直是神仙操作了！