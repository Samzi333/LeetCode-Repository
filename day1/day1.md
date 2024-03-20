# 刷题-数组

## 参考资料：

leetcode

[代码随想录 (programmercarl.com)](https://www.programmercarl.com/)

## 704.二分查找

**这道题目的前提是数组为有序数组**，同时题目还强调**数组中无重复元素**，因为一旦有重复元素，使用二分查找法返回的元素下标可能不是唯一的，这些都是使用二分法的前提条件，当大家看到题目描述满足如上条件的时候，可要想一想是不是可以用二分法了。

二分查找涉及的很多的边界条件，逻辑比较简单，但就是写不好。例如到底是 `while(left < right)` 还是 `while(left <= right)`，到底是`right = middle`呢，还是要`right = middle - 1`呢？



写二分法经常写乱，主要是因为**对区间的定义没有想清楚，区间的定义就是不变量**。要在二分查找的过程中，保持不变量，就是在while寻找中每一次边界的处理都要坚持根据区间的定义来操作，这就是**循环不变量**规则。

写二分法，区间的定义一般为两种，**左闭右闭即[left, right]，或者左闭右开即[left, right)**。

### 左闭右闭

区间的定义这就决定了二分法的代码应该如何写，因为定义target在[left, right]区间，所以有如下两点：

- while (left <= right) 要使用 <= ，因为left == right是有意义的（只有一个数），所以使用 <=
- if (nums[middle] > target) right 要赋值为 middle - 1，因为当前这个nums[middle]一定不是target，那么接下来要查找的左区间结束下标位置就是 middle - 1
- 当left == right时，就是在判断最后一个数是否符合（比如一些极端值，数组两端，都是在这里判断）
- 当left > right时，说明没有查找的数

<img src="https://code-thinking-1253855093.file.myqcloud.com/pics/20210311153055723.jpg" alt="704.二分查找" style="zoom:67%;" />



```
// 版本一，C++
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0;
        int right = nums.size() - 1; // 定义target在左闭右闭的区间里，[left, right]
        while (left <= right) { // 当left==right，区间[left, right]依然有效，所以用 <=
            int middle = left + ((right - left) / 2);// 防止溢出 等同于(left + right)/2
            if (nums[middle] > target) {
                right = middle - 1; // target 在左区间，所以[left, middle - 1]
            } else if (nums[middle] < target) {
                left = middle + 1; // target 在右区间，所以[middle + 1, right]
            } else { // nums[middle] == target
                return middle; // 数组中找到目标值，直接返回下标
            }
        }
        // 未找到目标值
        return -1;
    }
};

// 版本一，Java
class Solution {
    public int search(int[] nums, int target) {
        int first = 0;
        int last = nums.length -1;
        if (target == nums[last]){
            return last;
        }else if(target == nums[first]){
            return first;
        }
        int mid = (last - first)/2;
        while(first <= last){
            if (target == nums[mid]){
                return mid;
            }else if (target < nums[mid]){
                last = mid - 1;
                mid = first + (last - first)/2;
            }else{
                first = mid + 1;
                mid = first + (last - first)/2;
            }
        }
        return -1;
    }
}
```

注意在更新左右区间的时候，作差之后要加上原始的first，不然只是差值而不是具体坐标。

- 时间复杂度：O(log n)
- 空间复杂度：O(1)

### 左闭右开

如果说定义 target 是在一个在左闭右开的区间里，也就是[left, right) ，那么二分法的边界处理方式则截然不同。

有如下两点：

- while (left < right)，这里使用 < ,因为left == right在区间[left, right)是没有意义的
- if (nums[middle] > target) right 更新为 middle，因为当前nums[middle]不等于target，去左区间继续寻找，而寻找区间是左闭右开区间，所以right更新为middle，即：下一个查询区间不会去比较nums[middle]（所以不用-1）
- 当left == right -1时，就是在判断最后一个数
- 当left == right时，指代的空间就岔开了，没有符合的数。

<img src="https://code-thinking-1253855093.file.myqcloud.com/pics/20210311153123632.jpg" alt="704.二分查找1" style="zoom:67%;" />



```
// 版本二，C++
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0;
        int right = nums.size(); // 定义target在左闭右开的区间里，即：[left, right)
        while (left < right) { // 因为left == right的时候，在[left, right)是无效的空间，所以使用 <
            int middle = left + ((right - left) >> 1);
            if (nums[middle] > target) {
                right = middle; // target 在左区间，在[left, middle)中
            } else if (nums[middle] < target) {
                left = middle + 1; // target 在右区间，在[middle + 1, right)中
            } else { // nums[middle] == target
                return middle; // 数组中找到目标值，直接返回下标
            }
        }
        // 未找到目标值
        return -1;
    }
};

//Java
class Solution {
    public int search(int[] nums, int target) {
        int first = 0;
        int last = nums.length;
        if (target == nums[last-1]){
            return last-1;
        }else if(target == nums[first]){
            return first;
        }
        while(first < last){
            int mid = first + (last - first)/2;
            if (target == nums[mid]){
                return mid;
            }else if (target < nums[mid]){
                last = mid;
            }else{
                first = mid + 1;
            }
        }
        return -1;
    }
}
```

- 时间复杂度：O(log n)
- 空间复杂度：O(1)



### 其他题解

#### 35.搜索插入位置

```
//左闭右闭
class Solution {
    public int searchInsert(int[] nums, int target) {
        int left = 0;
        int right = nums.length - 1; 
        if(target <= nums[0]){
            return 0;
        }else if(target > nums[right]){
            return right + 1;
        }else if(target == nums[right]){
            return right;
        }

        while(left <= right){
            int mid = left + (right - left)/2;
            if (target == nums[mid]){
                return mid;
            }else if (target < nums[mid]){
                right = mid -1;
            }else if (target > nums[mid]){
                left = mid + 1;
            }
        }
        return right + 1;
    }
}

//官方解法
class Solution {
    public int searchInsert(int[] nums, int target) {
        int n = nums.length;
        int left = 0, right = n - 1, ans = n;
        while (left <= right) {
            int mid = ((right - left) >> 1) + left;
            if (target <= nums[mid]) {
            	//这里的用ans放在偏大的mid，是因为插入的时候一般是左插入，右边的数要右移，
            	//而且先赋值ans，right再-1，所以最后就不用-1。
                ans = mid;
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }
        return ans;
    }
}
//链接：https://leetcode.cn/problems/search-insert-position/solutions/333632/sou-suo-cha-ru-wei-zhi-by-leetcode-solution/
```

这道题的关键是，在left == right的时候，循环还会继续一次，得到一个left > right的情况，这个时候，插入的位置就是两者中间。

```
//左闭右开
class Solution {
    public int searchInsert(int[] nums, int target) {
        int left = 0;
        int right = nums.length; 
        if(target <= nums[0]){
            return 0;
        }else if(target > nums[right - 1]){
            return right;
        }else if(target == nums[right -1]){
            return right - 1;
        }

        while(left < right){
            int mid = left + (right - left)/2;
            if (target == nums[mid]){
                return mid;
            }else if (target < nums[mid]){
                right = mid;
            }else if (target > nums[mid]){
                left = mid + 1;
            }
        }
        return right;
    }
}
```

如果是左闭右开，就是定义right不一样，循环条件为left < right，最后返回的值为right。



#### 34. 在排序数组中查找元素第一和最后一位置

```
//第一遍自己写的，暴力的找到一个相等的值，然后开始左右搜索。
class Solution {
    public int[] searchRange(int[] nums, int target) {
        int right = nums.length - 1;
        int left = 0;
        int[] res = new int[]{-1, -1};
        while( left <= right ){
            int mid = left + (right - left)/2;
            if (target == nums[mid]){
                int i = 1;
                res[0] = mid;
                res[1] = mid;
                while((mid + i) < nums.length && target == nums[mid + i]){
                    res[1] = mid + i;
                    i++;
                }
                i = 1;
                while((mid - i) >= 0 && target == nums[mid - i]){
                    res[0] = mid - i;
                    i++;
                }
                return res;
            }else if (target < nums[mid]){
                right = mid - 1;
            }else if (target > nums[mid]){
                left = mid + 1;
            }
        }
        return res;
    }
}
```

第二种则是leetcode官方的写法：

```
class Solution {
    public int[] searchRange(int[] nums, int target) {
        int leftIdx = binarySearch(nums, target, true);
        int rightIdx = binarySearch(nums, target, false) - 1;
        if (leftIdx <= rightIdx && rightIdx < nums.length && nums[leftIdx] == target && nums[rightIdx] == target) {
            return new int[]{leftIdx, rightIdx};
        } 
        return new int[]{-1, -1};
    }

    public int binarySearch(int[] nums, int target, boolean lower) {
        int left = 0, right = nums.length - 1, ans = nums.length;
        while (left <= right) {
            int mid = (left + right) / 2;
            if (nums[mid] > target || (lower && nums[mid] >= target)) {
                right = mid - 1;
                ans = mid;
            } else {
                left = mid + 1;
            }
        }
        return ans;
    }
}
```

以左边界，调用函数为leftIdx = binarySearch(nums, target, true)；为例：
这里面并没有对nums[mid] == target的情况做处理而是继续执行，直到获得left > right的结果，然后将left此时的mid返回回去。

这里的mid表示的是**在left > right之前一步，符合while循环的最后一个mid**。并且这个mid符合 (nums[mid] > target || (lower && nums[mid] >= target))，lower为true，**也就是 nums[mid] >= target的情况**。

这个时候，由于对等于号的处理在右边界上，并且此时 right == left，所以mid - 1 = left - 1时，不符合**nums[mid -1] < target**。也就是我们寻找的目标数左边界。



下面是代码随想录的结果

寻找target在数组里的左右边界，有如下三种情况：

- 情况一：target 在数组范围的右边或者左边，例如数组{3, 4, 5}，target为2或者数组{3, 4, 5},target为6，此时应该返回{-1, -1}
- 情况二：target 在数组范围中，且数组中不存在target，例如数组{3,6,7},target为5，此时应该返回{-1, -1}
- 情况三：target 在数组范围中，且数组中存在target，例如数组{3,6,7},target为6，此时应该返回{1, 1}

这三种情况都考虑到，说明就想的很清楚了。

接下来，在去寻找左边界，和右边界了。

采用二分法来去寻找左右边界，为了让代码清晰，我分别写两个二分来寻找左边界和右边界。

**刚刚接触二分搜索的同学不建议上来就想用一个二分来查找左右边界，很容易把自己绕进去，建议扎扎实实的写两个二分分别找左边界和右边界**

**寻找右边界**

先来寻找右边界，至于二分查找，如果看过[704.二分查找 (opens new window)](https://programmercarl.com/0704.二分查找.html)就会知道，二分查找中什么时候用while (left <= right)，有什么时候用while (left < right)，其实只要清楚**循环不变量**，很容易区分两种写法。

那么这里我采用while (left <= right)的写法，区间定义为[left, right]，即左闭右闭的区间（如果这里有点看不懂了，强烈建议把[704.二分查找 (opens new window)](https://programmercarl.com/0704.二分查找.html)这篇文章先看了，704题目做了之后再做这道题目就好很多了）

确定好：计算出来的右边界是不包含target的右边界，左边界同理。

可以写出如下代码

```cpp
// c++ 版本 
//二分查找，寻找target的右边界（不包括target）
// 如果rightBorder为没有被赋值（即target在数组范围的左边，例如数组[3,3]，target为2），为了处理情况一
int getRightBorder(vector<int>& nums, int target) {
    int left = 0;
    int right = nums.size() - 1; // 定义target在左闭右闭的区间里，[left, right]
    int rightBorder = -2; // 记录一下rightBorder没有被赋值的情况
    while (left <= right) { // 当left==right，区间[left, right]依然有效
        int middle = left + ((right - left) / 2);// 防止溢出 等同于(left + right)/2
        if (nums[middle] > target) {
            right = middle - 1; // target 在左区间，所以[left, middle - 1]
        } else { // 当nums[middle] == target的时候，更新left，这样才能得到target的右边界
            left = middle + 1;
            rightBorder = left;
        }
    }
    return rightBorder;
}
```

**寻找左边界**

```cpp
// 二分查找，寻找target的左边界leftBorder（不包括target）
// 如果leftBorder没有被赋值（即target在数组范围的右边，例如数组[3,3],target为4），为了处理情况一
int getLeftBorder(vector<int>& nums, int target) {
    int left = 0;
    int right = nums.size() - 1; // 定义target在左闭右闭的区间里，[left, right]
    int leftBorder = -2; // 记录一下leftBorder没有被赋值的情况
    while (left <= right) {
        int middle = left + ((right - left) / 2);
        if (nums[middle] >= target) { // 寻找左边界，就要在nums[middle] == target的时候更新right
            right = middle - 1;
            leftBorder = right;
        } else {
            left = middle + 1;
        }
    }
    return leftBorder;
}
```

**处理三种情况**

左右边界计算完之后，看一下主体代码，这里把上面讨论的三种情况，都覆盖了

```cpp
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        int leftBorder = getLeftBorder(nums, target);
        int rightBorder = getRightBorder(nums, target);
        // 情况一
        if (leftBorder == -2 || rightBorder == -2) return {-1, -1};
        // 情况三
        if (rightBorder - leftBorder > 1) return {leftBorder + 1, rightBorder - 1};
        // 情况二
        return {-1, -1};
    }
private:
     int getRightBorder(vector<int>& nums, int target) {
        int left = 0;
        int right = nums.size() - 1;
        int rightBorder = -2; // 记录一下rightBorder没有被赋值的情况
        while (left <= right) {
            int middle = left + ((right - left) / 2);
            if (nums[middle] > target) {
                right = middle - 1;
            } else { // 寻找右边界，nums[middle] == target的时候更新left
                left = middle + 1;
                rightBorder = left;
            }
        }
        return rightBorder;
    }
    int getLeftBorder(vector<int>& nums, int target) {
        int left = 0;
        int right = nums.size() - 1;
        int leftBorder = -2; // 记录一下leftBorder没有被赋值的情况
        while (left <= right) {
            int middle = left + ((right - left) / 2);
            if (nums[middle] >= target) { // 寻找左边界，nums[middle] == target的时候更新right
                right = middle - 1;
                leftBorder = right;
            } else {
                left = middle + 1;
            }
        }
        return leftBorder;
    }
};
```

这份代码在简洁性很有大的优化空间，例如把寻找左右区间函数合并一起。

但拆开更清晰一些，而且把三种情况以及对应的处理逻辑完整的展现出来了。

下面是Java版

```java
class Solution {
    int[] searchRange(int[] nums, int target) {
        int leftBorder = getLeftBorder(nums, target);
        int rightBorder = getRightBorder(nums, target);
        // 情况一
        if (leftBorder == -2 || rightBorder == -2) return new int[]{-1, -1};
        // 情况三
        if (rightBorder - leftBorder > 1) return new int[]{leftBorder + 1, rightBorder - 1};
        // 情况二
        return new int[]{-1, -1};
    }

    int getRightBorder(int[] nums, int target) {
        int left = 0;
        int right = nums.length - 1;
        int rightBorder = -2; // 记录一下rightBorder没有被赋值的情况
        while (left <= right) {
            int middle = left + ((right - left) / 2);
            if (nums[middle] > target) {
                right = middle - 1;
            } else { // 寻找右边界，nums[middle] == target的时候更新left
                left = middle + 1;
                rightBorder = left;
            }
        }
        return rightBorder;
    }

    int getLeftBorder(int[] nums, int target) {
        int left = 0;
        int right = nums.length - 1;
        int leftBorder = -2; // 记录一下leftBorder没有被赋值的情况
        while (left <= right) {
            int middle = left + ((right - left) / 2);
            if (nums[middle] >= target) { // 寻找左边界，nums[middle] == target的时候更新right
                right = middle - 1;
                leftBorder = right;
            } else {
                left = middle + 1;
            }
        }
        return leftBorder;
    }
}
// 解法2
// 1、首先，在 nums 数组中二分查找 target；
// 2、如果二分查找失败，则 binarySearch 返回 -1，表明 nums 中没有 target。此时，searchRange 直接返回 {-1, -1}；
// 3、如果二分查找成功，则 binarySearch 返回 nums 中值为 target 的一个下标。然后，通过左右滑动指针，来找到符合题意的区间

class Solution {
	public int[] searchRange(int[] nums, int target) {
		int index = binarySearch(nums, target); // 二分查找
		
		if (index == -1) { // nums 中不存在 target，直接返回 {-1, -1}
			return new int[] {-1, -1}; // 匿名数组 
		}
		// nums 中存在 targe，则左右滑动指针，来找到符合题意的区间
		int left = index;
		int right = index;
        // 向左滑动，找左边界
		while (left - 1 >= 0 && nums[left - 1] == nums[index]) { // 防止数组越界。逻辑短路，两个条件顺序不能换
			left--;
		}
        // 向右滑动，找右边界
		while (right + 1 < nums.length && nums[right + 1] == nums[index]) { // 防止数组越界。
			right++;
		}
		return new int[] {left, right};
    }
	
	/**
	 * 二分查找
	 * @param nums
	 * @param target
	 */
	public int binarySearch(int[] nums, int target) {
		int left = 0;
		int right = nums.length - 1; // 不变量：左闭右闭区间
		
		while (left <= right) { // 不变量：左闭右闭区间
			int mid = left + (right - left) / 2;
			if (nums[mid] == target) {
				return mid;
			} else if (nums[mid] < target) {
				left = mid + 1;
			} else {
				right = mid - 1; // 不变量：左闭右闭区间
			}
		}
		return -1; // 不存在
	}
}
// 解法三
class Solution {
    public int[] searchRange(int[] nums, int target) {
        int left = searchLeft(nums,target);
        int right = searchRight(nums,target);
        return new int[]{left,right};
    }
    public int searchLeft(int[] nums,int target){
        // 寻找元素第一次出现的地方
        int left = 0;
        int right = nums.length-1;
        while(left<=right){
            int mid = left+(right-left)/2;
            // >= 的都要缩小 因为要找第一个元素
            if(nums[mid]>=target){
                right = mid - 1;
            }else{
                left = mid + 1;
            }
        }
        // right = left - 1
        // 如果存在答案 right是首选
        if(right>=0&&right<nums.length&&nums[right]==target){
            return right;
        }
        if(left>=0&&left<nums.length&&nums[left]==target){
            return left;
        }
        return -1;
    }

    public int searchRight(int[] nums,int target){
        // 找最后一次出现
        int left = 0;
        int right = nums.length-1;
        while(left<=right){
            int mid = left + (right-left)/2;
            // <= 的都要更新 因为我们要找最后一个元素
            if(nums[mid]<=target){
                left = mid + 1;
            }else{
                right = mid - 1;
            }
        }
        // left = right + 1
        // 要找最后一次出现 如果有答案 优先找left
        if(left>=0&&left<nums.length&&nums[left]==target){
            return left;
        }
        if(right>=0&&right<=nums.length&&nums[right]==target){
            return right;
        }
        return -1;
    }
}
```

**总结**

二分查找的过程中，如果不对等于数进行专门的处理，则会继续循环，一直到left > right为止

如果将等于时的处理，加入到其中一个边界的处理中（比如左边界），则查找的最终的mid就是另一个（最右边）的那个等于target的值（因为右边界不会等于target，必定是大于target），反之亦然

简单来说，如果不对等于数进行专门的处理，则最终查找会循环到处理等于的边界的另一端（因为处理了等于情况的边界，依旧会继续压缩，但不处理等于的边界是不会压缩等于的情况的）。

至于最终ans的赋值，一般在处理等于的边界判断里，因为这是遇到的最后一个等于target的值，之后就是left > right了。



#### 69.x的平方根

这个注意一下二分法和牛顿迭代法怎么使用的就行。

#### 367.有效的完全平方数

还是找时间学一下牛顿迭代法

注意，二分法的时候，求平方时要用long，不然可能超出范围。







## 27.移除元素

[代码随想录 (programmercarl.com)](https://www.programmercarl.com/0027.移除元素.html#算法公开课)



### 双指针法

双指针法（快慢指针法）： **通过一个快指针和慢指针在一个for循环下完成两个for循环的工作。**

定义快慢指针

- 快指针：寻找新数组的元素 ，新数组就是不含有目标元素的数组
- 慢指针：指向更新 新数组下标的位置

```java
class Solution {
    public int removeElement(int[] nums, int val) {
        int fastIndex = 0;
        int slowIndex = 0;
        for(fastIndex = 0; fastIndex < nums.length; fastIndex++){
            if(val != nums[fastIndex]){
                nums[slowIndex++] = nums[fastIndex];
            }
        }
        return slowIndex;
    }
}
```

注意这些实现方法并没有改变元素的相对位置！

- 时间复杂度：O(n)
- 空间复杂度：O(1)

**指针法（快慢指针法）在数组和链表的操作中是非常常见的，很多考察数组、链表、字符串等操作的面试题，都使用双指针法。**



本质上，双指针法本身就是模拟了寻找过程：

（1）当一直没找到时，俩指针都是指向同一个地址，进行赋值时就是自己赋值给自己

（2）当发现一个符合的情况时，快指针则会指向下一个需要被移动的元素，而慢指针则是指向需要被删除/覆盖的元素。

（3）当有多个符合情况的值，快指针永远指向下一个还未被判断是否符合的元素，而慢指针则指向第一个还没被i替代的元素。只有当快指针指向一个非相同值时，才会覆盖慢指针指向的元素，并前移一格

（4）两个指针的距离差就是符合元素的个数，快指针遍历完整个数组后，慢指针指向最终的位置



当然，最初始的双指针法还不是最少移动元素数量的



### 相向双指针法

两个指针从数组两端出发，这个方法有效的前提是能改变原始元素的相对位置。

**避免了需要保留的元素的重复赋值操作**。

```java
//相向双指针方法，基于元素顺序可以改变的题目描述改变了元素相对位置，确保了移动最少元素
//时间复杂度：O(n)
//空间复杂度：O(1)
class Solution {
    public int removeElement(int[] nums, int val) {
        int left = 0;
        int right = nums.length - 1;
        while(right >= 0 && nums[right] == val) right--; //将right移到从右数第一个值不为val的位置
        while(left <= right) {
            if(nums[left] == val) { //left位置的元素需要移除
                //将right位置的元素移到left（覆盖），right位置移除
                nums[left] = nums[right];
                right--;
            }
            left++;
            while(right >= 0 && nums[right] == val) right--;
        }
        return left;
    }
}
```



### 相关题目

#### 26.删除有序数组中的重复项

首先注意数组是有序的，那么重复的元素一定会相邻。

要求删除重复元素，实际上就是将不重复的元素移到数组的左侧。

考虑用 2 个指针，一个在前记作 p，一个在后记作 q，算法流程如下：

1.比较 p 和 q 位置的元素是否相等。

如果相等，q 后移 1 位
如果不相等，将 q 位置的元素复制到 p+1 位置上，p 后移一位，q 后移 1 位
重复上述过程，直到 q 等于数组长度。

返回 p + 1，即为新数组长度。



```
//自己的版本
class Solution {
    public int removeDuplicates(int[] nums) {
        int fastIndex = 1;
        int slowIndex = 0;
        if(nums.length < 2){
            return nums.length;
        }
        for(fastIndex = 1; fastIndex < nums.length; fastIndex++){
            if((fastIndex - slowIndex == 1) && (nums[fastIndex] != nums[slowIndex])){
                slowIndex++;
            }else if((fastIndex - slowIndex > 1) && (nums[fastIndex] != nums[slowIndex])){
                slowIndex++;
                nums[slowIndex] = nums[fastIndex];
            }
        }
        return slowIndex + 1;
    }
}

//推荐答案的版本
 public int removeDuplicates(int[] nums) {
    if(nums == null || nums.length == 0) return 0;
    int p = 0;
    int q = 1;
    while(q < nums.length){
        if(nums[p] != nums[q]){
            nums[p + 1] = nums[q];
            p++;
        }
        q++;
    }
    return p + 1;
}
```

思路是一样的，只是自己想的判断条件复杂点，但是也是对的。因为如果p + 1 =q时，对nums[p + 1] = nums[q]还是自己赋值给自己

这里只是一个偏移。

还有另一个答案：

数组中没有重复元素，按照上面的方法，每次比较时 `nums[p]` 都不等于 `nums[q]`，因此就会将 `q` 指向的元素原地复制一遍，这个操作其实是不必要的。

因此我们可以添加一个小判断，当 `q - p > 1` 时，才进行复制。

```
public int removeDuplicates(int[] nums) {
    if(nums == null || nums.length == 0) return 0;
    int p = 0;
    int q = 1;
    while(q < nums.length){
        if(nums[p] != nums[q]){
            if(q - p > 1){
                nums[p + 1] = nums[q];
            }
            p++;
        }
        q++;
    }
    return p + 1;
}
```



#### 283.移动零

```
//自己的解法
class Solution {
    public void moveZeroes(int[] nums) {
        int first = 0;
        int last = 0;
        int len = nums.length;
        while(first < len){
            if(nums[first] == 0){
            }else if(nums[first] != 0 && first != last){
                nums[last] = nums[first];
                last++;
            }else if(nums[first] != 0 && first == last){
                last++;
            }
            first++;
        }
        while(last < first){
            nums[last++] = 0;
        }
    }
}
```



方法一：双指针法

思路及解法

使用双指针，左指针指向当前已经处理好的序列的尾部，右指针指向待处理序列的头部。

右指针不断向右移动，每次右指针指向非零数，则将左右指针对应的数交换，同时左指针右移。

注意到以下性质：

1.左指针左边均为非零数；

2.右指针左边直到左指针处均为零。

因此每次交换，都是将左指针的零与右指针的非零数交换，且非零数的相对顺序并未改变。

```
//官方解法1：双指针法
class Solution {
    public void moveZeroes(int[] nums) {
        int n = nums.length, left = 0, right = 0;
        while (right < n) {
            if (nums[right] != 0) {
                swap(nums, left, right);
                left++;
            }
            right++;
        }
    }
	//自己实现的交换函数
    public void swap(int[] nums, int left, int right) {
        int temp = nums[left];
        nums[left] = nums[right];
        nums[right] = temp;
    }
}
```

复杂度分析

时间复杂度：O(n)，其中 n 为序列长度。每个位置至多被遍历两次。

空间复杂度：O(1)。只需要常数的空间存放若干变量。

作者：力扣官方题解
链接：https://leetcode.cn/problems/move-zeroes/solutions/489622/yi-dong-ling-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。



#### 844.比较含退格的字符串

[844. 比较含退格的字符串](https://leetcode.cn/problems/backspace-string-compare/)

```
//暴力解法
class Solution {
    public boolean backspaceCompare(String s, String t) {
        String a = clearBackspace(s);
        String b = clearBackspace(t);
        if(a.equals(b)){
            return true;
        }else{
            return false;
        }
    }
    public String clearBackspace(String s){
        StringBuilder sb = new StringBuilder("");
        for(int i = 0; i < s.length(); i++){
            char c = s.charAt(i);
            if(c != '#'){
                sb.append(c);
            }else{
                sb.deleteCharAt(sb.length() - 1);
            }
        }
        return sb.toString();
    }
}
```

其中charAt(i)，用于遍历String

append(c)，用于向StringBuilder和StringBuffer添加字符

deleteCharAt(x)，用于删除指定位置的字符

toString()，转换成String

length()获得长度



官方解法

最容易想到的方法是将给定的字符串中的退格符和应当被删除的字符都去除，还原给定字符串的一般形式。然后直接比较两字符串是否相等即可。

具体地，我们用栈处理遍历过程，每次我们遍历到一个字符：

（1）如果它是退格符，那么我们将栈顶弹出；

（2）如果它是普通字符，那么我们将其压入栈中。

```
class Solution {
    public boolean backspaceCompare(String S, String T) {
        return build(S).equals(build(T));
    }

    public String build(String str) {
        StringBuffer ret = new StringBuffer();
        int length = str.length();
        for (int i = 0; i < length; ++i) {
            char ch = str.charAt(i);
            if (ch != '#') {
                ret.append(ch);
            } else {
                if (ret.length() > 0) {
                    ret.deleteCharAt(ret.length() - 1);
                }
            }
        }
        return ret.toString();
    }
}
```

复杂度分析

（1）时间复杂度：O(N+M)，其中 N 和 M 分别为字符串 S 和 T 的长度。我们需要遍历两字符串各一次。

（2）空间复杂度：O(N+M)，其中 N 和 M 分别为字符串 S 和 T 的长度。主要为还原出的字符串的开销。



双指针法

思路及算法

一个字符是否会被删掉，只取决于该字符后面的退格符，而与该字符前面的退格符无关。因此当我们逆序地遍历字符串，就可以立即确定当前字符是否会被删掉。

具体地，我们定义 skip 表示当前待删除的字符的数量。每次我们遍历到一个字符：

（1）若该字符为退格符，则我们需要多删除一个普通字符，我们让 skip加 1；

（2）若该字符为普通字符：

A.若 skip为 0，则说明当前字符不需要删去；

B.若 skip 不为 0，则说明当前字符需要删去，我们让 skip 减 1。

这样，我们定义两个指针，分别指向两字符串的末尾。每次我们让两指针逆序地遍历两字符串，直到两字符串能够各自确定一个字符，然后将这两个字符进行比较。重复这一过程直到找到的两个字符不相等，或遍历完字符串为止。

```
class Solution {
    public boolean backspaceCompare(String S, String T) {
        int i = S.length() - 1, j = T.length() - 1;
        int skipS = 0, skipT = 0;

        while (i >= 0 || j >= 0) {
            while (i >= 0) {
                if (S.charAt(i) == '#') {
                    skipS++;
                    i--;
                } else if (skipS > 0) {
                    skipS--;
                    i--;
                } else {
                    break;
                }
            }
            while (j >= 0) {
                if (T.charAt(j) == '#') {
                    skipT++;
                    j--;
                } else if (skipT > 0) {
                    skipT--;
                    j--;
                } else {
                    break;
                }
            }
            if (i >= 0 && j >= 0) {
                if (S.charAt(i) != T.charAt(j)) {
                    return false;
                }
            } else {
                if (i >= 0 || j >= 0) {
                    return false;
                }
            }
            i--;
            j--;
        }
        return true;
    }
}

```



