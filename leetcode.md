```c
1. 两数之和

给定一个整数数组 nums 和一个整数目标值 target 请你在该数组中找出 和 为目标值 target  的那两个整数 并返回它们的数组下标 可以假设每种输入只会对应一个答案并且你不能使用两次相同的元素可以按任意顺序返回答案
```

```c
输入：nums = [2,7,11,15], target = 9
输出：[0,1]
解释：因为 nums[0] + nums[1] == 9  返回 [0, 1]  
```

```c
? 暴力枚举
    
最容易想到的方法是枚举数组中的每一个数 x 寻找数组中是否存在 target - x 当我们使用遍历整个数组的方式寻找 target - x 时 需要注意到每一个位于 x 之前的元素都已经和 x 匹配过 因此不需要再进行匹配 而每一个元素不能被使用两次 所以我们只需要在 x 后面的元素中寻找 target - x
```

```c
int* twoSum(int* nums, int numsSize, int target, int* returnSize) {
    for (int i = 0; i < numsSize; ++i) {
        for (int j = i + 1; j < numsSize; ++j) {
            if (nums[i] + nums[j] == target) {
                int* ret = malloc(sizeof(int) * 2);
                ret[0] = i, ret[1] = j;
                *returnSize = 2;
                return ret;
            }
        }
    }
    *returnSize = 0;
    return NULL;
}
```

```c
时间复杂度：O(N^2) 其中 N 是数组中的元素数量 最坏情况下数组中任意两个数都要被匹配一次
空间复杂度：O(1)
```

```c
? 哈希表

注意到方法一的时间复杂度较高的原因是寻找 target - x 的时间复杂度过高 因此 我们需要一种更优秀的方法 能够快速寻找数组中是否存在目标元素 如果存在 我们需要找出它的索引 使用哈希表 可以将寻找 target - x 的时间复杂度降低到从 O(N) 降低到 O(1) 这样我们创建一个哈希表 对于每一个 x 我们首先查询哈希表中是否存在 target - x 然后将 x 插入到哈希表中 即可保证不会让 x 和自己匹配
```

```c
#include "uthash.h" 
#include <stdlib.h>

struct hashTable {
    int key;
    int val;
    UT_hash_handle hh;
};

struct hashTable* hashtable;

struct hashTable* find(int ikey) {
    struct hashTable* tmp;
    HASH_FIND_INT(hashtable, &ikey, tmp);
    return tmp;
}

void insert(int ikey, int ival) {
    struct hashTable* it = find(ikey);
    if (it == NULL) {
        struct hashTable* tmp = malloc(sizeof(struct hashTable));
        tmp->key = ikey, tmp->val = ival;
        HASH_ADD_INT(hashtable, key, tmp);
    } else {
        it->val = ival;
    }
}

int* twoSum(int* nums, int numsSize, int target, int* returnSize) {
    hashtable = NULL;
    for (int i = 0; i < numsSize; i++) {
        struct hashTable* it = find(target - nums[i]);
        if (it != NULL) {
            int* ret = malloc(sizeof(int) * 2);
            ret[0] = it->val, ret[1] = i;
            *returnSize = 2;
            return ret;
        }
        insert(nums[i], i);
    }
    *returnSize = 0;
    return NULL;
}
```

```c
时间复杂度：O(N) 其中 N 是数组中的元素数量 对于每一个元素 x 我们可以 O(1) 地寻找 target - x 
空间复杂度：O(N) 其中 N 是数组中的元素数量 主要为哈希表的开销 
```



```c
26. 删除数组重复项

给你一个 非严格递增排列 的数组 nums  请你原地删除重复出现的元素 使每个元素 只出现一次 返回删除后数组的新长度 元素的 相对顺序 应该保持 一致 然后返回 nums 中唯一元素的个数 考虑 nums 的唯一元素的数量为 k 你需要做以下事情确保你的题解可以被通过 更改数组 nums 使 nums 的前 k 个元素包含唯一元素 并按照它们最初在 nums 中出现的顺序排列 nums 的其余元素与 nums 的大小不重要 返回 k
```

```c
输入：nums = [1,1,2]
输出：2 nums = [1,2,_]
解释：函数应该返回新的长度 2 并且原数组 nums 的前两个元素被修改为 1 2 不需要考虑数组中超出新长度后面的元素

输入：nums = [0,0,1,1,1,2,2,3,3,4]
输出：5 nums = [0,1,2,3,4]
解释：函数应该返回新的长度 5 并且原数组 nums 的前五个元素被修改为 0 1 2 3 4 不需要考虑数组中超出新长度后面的元素
```

```c
int removeDuplicates(int* nums, int numsSize) {
    if (numsSize == 0) {
        return 0;
    }
    int fast = 1, slow = 1;
    while (fast < numsSize) {
        if (nums[fast] != nums[fast - 1]) {
            nums[slow] = nums[fast];
            ++slow;
        }
        ++fast;
    }
    return slow;
}
```

```c
27. 移除元素
    
给你一个数组 nums 和一个值 val 你需要原地移除所有数值等于 val 的元素 元素的顺序可能发生改变 然后返回 nums 中与 val 不同的元素的数量 假设 nums 中不等于 val 的元素数量为 k 要通过此题 您需要执行以下操作 更改 nums 数组 使 nums 的前 k 个元素包含不等于 val 的元素 nums 的其余元素和 nums 的大小并不重要 返回 k
```

```c
输入：nums = [3,2,2,3], val = 3
输出：2 nums = [2,2,_,_]

输入：nums = [0,1,2,2,3,0,4,2], val = 2
输出：5 nums = [0,1,4,0,3,_,_,_]
解释：你的函数应该返回 k = 5 并且 nums 中的前五个元素为 0 0 1 3 4 注意这五个元素可以任意顺序返回
你在返回的 k 个元素之外留下了什么并不重要因此它们并不计入评测
```

```c
? 双指针

由于题目要求删除数组中等于 val 的元素 因此输出数组的长度一定小于等于输入数组的长度 我们可以把输出的数组直接写在输入数组上 可以使用双指针 右指针 right 指向当前将要处理的元素 左指针 left 指向下一个将要赋值的位置 如果右指针指向的元素不等于 val 它一定是输出数组的一个元素 我们就将右指针指向的元素复制到左指针位置 然后将左右指针同时右移 如果右指针指向的元素等于 val 它不能在输出数组 此时左指针不动 右指针右移一位 整个过程保持不变的性质是：区间 [0,left) 中的元素都不等于 val 当左右指针遍历完输入数组以后，left 的值就是输出数组的长度 这样的算法在最坏情况下（输入数组中没有元素等于 val）左右指针各遍历了数组一次
```

```c
int removeElement(int* nums, int numsSize, int val) {
    int left = 0;
    for(int right = 0; right < numsSize; ++right){
       if(nums[rigth] != val){
           nums[left] = nums[right];
           ++left;
       }
    }
    return left;
}
```

```c
时间复杂度：O(n) 其中 n 为序列的长度。我们只需要遍历该序列至多两次
空间复杂度：O(1) 我们只需要常数的空间保存若干变量
```

```c
? 双指针优化

如果要移除的元素恰好在数组的开头 例如序列 [1,2,3,4,5] 当 val 为 1 时 我们需要把每一个元素都左移一位 注意到题目中说元素的顺序可以改变 实际上我们可以直接将最后一个元素 5 移动到序列开头 取代元素 1 得到序列 [5,2,3,4] 同样满足题目要求 这个优化在序列中 val 元素的数量较少时非常有效 实现方面 我们依然使用双指针 两个指针初始时分别位于数组的首尾 向中间移动遍历该序列

如果左指针 left 指向的元素等于 val 此时将右指针 right 指向的元素复制到左指针 left 的位置 然后右指针 right 左移一位 如果赋值过来的元素恰好也等于 val 可以继续把右指针 right 指向的元素的值赋值过来 左指针 left 指向的等于 val 的元素的位置继续被覆盖 直到左指针指向的元素的值不等于 val 为止 当左指针 left 和右指针 right 重合的时候 左右指针遍历完数组中所有的元素 这样的方法两个指针在最坏的情况下合起来只遍历了数组一次 与方法一不同的是 方法二避免了需要保留的元素的重复赋值操作
```

```c
int removeElement(int* nums, int numsSize, int val) {
    int left = 0;
    int right = numsSize;
    while(left < right){
       if(nums[left] == val){
           nums[left] = nums[right - 1];
           --right;
           continue;
       }
       ++left;
    }
    return left;
}
```

```c
时间复杂度：O(n) 其中 n 为序列的长度 我们只需要遍历该序列至多一次
空间复杂度 O(1) 我们只需要常数的空间保存若干变量
```

```c
35. 搜索插入位置

给定一个排序数组和一个目标值 在数组中找到目标值并返回其索引 如果目标值不存在于数组中返回它将会被按顺序插入的位置

输入: nums = [1,3,5,6], target = 5
输出: 2
示例 2:

输入: nums = [1,3,5,6], target = 2
输出: 1
示例 3:

输入: nums = [1,3,5,6], target = 7
输出: 4
```

```c
int searchInsert(int* nums, int numsSize, int target) {
    int left=0,right=numsSize-1;
    while(left<=right){
        int mid=(left+right)/2;
        if(nums[mid]<target){
            left=mid+1;
        }else{
            right=mid-1;
        }
    }
    return left;
}
```

```c
时间复杂度：O(logn) 其中 n 为数组的长度 二分查找所需的时间复杂度为 O(logn)
空间复杂度：O(1) 我们只需要常数空间存放若干变量
```

