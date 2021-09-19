### 1.两数之和

> 在java泛型定义中不可包含基本类型，而要用封装类型

### 11.接最多的水

> 为什么选择两者中小边向中心移动呢？
>
> 原因是area=（length）*min（height），大边若是移动了，则length-1但是min（height）也最多取到原来的短边高度，必然减小，而移动短边并未磨灭大边向中心移动的可能性。

### 14.三数之和

```c
for(int i=0;i<length-2;i++){
    //使用双指针的做法进行滑动运算，首先固定i的位置
	if(i!=0 && nums[i]==nums[i-1])
		continue;
    //指定双指针的起止位置，之后进行指针滑动
    int j=i+1;
    int j=length-1;
    while(j<k){	//确认必须条件
    	sum = nums[i]+nums[j]+nums[k]; 
        if(sum == 0){//满足条件
            /****添加到list并返回的代码******/
            //先略去前一次添加到list中的nums[j]及nums[k]值
            while(j<k && nums[j]==nums[j+1]){
                j++;
            }
            while(j<k && nums[k]==nums[k-1]){
                k--;
            }
            //使得j、k分别指向新的值，并重新判断
            j++;k--;
            continue;
        }
        //直接进行窗口滑动以满足条件
        while(j<k && nums[i]+nums[j]+nums[k]<0)
           	j++;
        while(j<k && nums[i]+nums[j]+nums[k]<0)
            k--;
    }
}
```

### 31.下一个排列

```java
class Solution {
    /*****
        1.要找到下一个排列，其实是要找到最右边的一个顺序，如[1,2,4,3]，最有边开始找的顺序数为2,3或2，4
        2.接着要做的是调换开始发生顺序的值与其值右边最小的可发生顺序的值，如在2位置开始顺序，2<4&&2<3
        然而3<4，因此要把2和3进行调换，生成[1,3,4,2]
        3.最后，从开始发生顺序的位置对其右侧数组元素进行排序即可
        4.若是整个数组未检索到顺序，直接返回整个数组的顺序排序
     */
    public void nextPermutation(int[] nums) {
        int length = nums.length;
        int min = 10000;
        int index = -1; //用于存储最小顺序的下标
        int i=0;
        boolean isFlag = false;
        for(i=length-2;i>=0;i--){//从倒数第二个位置开始找顺序，满足尽可能往右找
            for(int j=i+1;j<length;j++){//从i+1开始
                //找到逆序后判断与min的大小，找到最小的顺序
                if(nums[i]<nums[j]){
                    min = min>nums[j]?nums[j]:min;
                    index = j;
                    isFlag = true;
                }
            }
            //找到最小顺序后互换
            if(isFlag){
                int temp = nums[i];
                nums[i] = min;
                nums[index] = temp;
                break;
            }   
        }
        if(isFlag){
            Arrays.sort(nums,i+1,length);
        }else{
            Arrays.sort(nums);
        }
    }
}
```

复杂度为O(n)，但若是通过

```java
i=length-2；
while(nums[i]>nums[i+1])  
	i--;
```

则可快速定位到发生顺序的下标



### 33.搜索旋转排序数组

```java
class Solution {
    /*********
        解题思路：数组必然有一半是顺序的，找到给定顺序的数组，
        则可用二分查找利用O(log n)的复杂度查出，
        否则查找另一半，同样为旋转数组，
        重复查找过程
     */
    public int search(int[] nums, int target) {
        int low = 0;
        int high = nums.length-1;
        int mid = 0;
        while(low<=high){
            mid = (low+high)/2;
            //low-mid为顺序部分的判别方法
            if(nums[low]<=nums[mid]){
                if(target>=nums[low] && target<=nums[mid]){
                    return binarySearch(nums,low,mid,target);
                }else{
                    low = mid+1;
                }
            }else{//mid+1~high-1顺序部分的判别方法
                if(target>=nums[mid+1] && target<=nums[high]){
                    return binarySearch(nums,mid+1,high,target);
                }else{
                    high = mid;
                }
            }
        }
        return -1;
    }
    public int binarySearch(int []nums,int low,int high,int target){
        int mid = -1;
        while(low<=high){
            mid = (low + high)/2;
            if(nums[mid]==target){
                return mid;
            }else if(nums[mid]>target){
                high = mid - 1;
            }else{
                low = mid + 1;
            }
        }
        return -1;
    }
}
```

### 35.搜索插入位置

```java
class Solution {
    /******
	    解题思路:若是在范围中，则只是执行了一次二分查找，
	    而若不存在于数组中，则在最后一次二分查找时（low==high==mid）,
	    	若是target>nums[low],则应返回当前的low+1,
	    					反之，返回当前的low，
	    	这一判断由最后一次执行中low与high的调整抵消
    */
    public int searchInsert(int[] nums, int target) {
        int low = 0;
        int high = nums.length-1;
        int mid = -1;
        while(low<=high){
            mid = (low + high)/2;
            if(nums[mid]==target){
                return mid;
            }else if(nums[mid]>target){
                high = mid - 1;
            }else{
                low = mid + 1;
            }
        }
        return low;
    }
}
```

### 34.在排序数组中查找第一个元素和最后一个等于target的元素

```java
class Solution {
	/******
		解题思路：1.顺序遍历找到第一个等于target的元素，找到后记录此下标
				2.要找到结束下标，就是在找到第一个元素之后，往后找第一个不为target的元素，
				3.由于是升序序列，所以后面找到的元素比target大，最后的下标为该下标减一
	*/
    public int[] searchRange(int[] nums, int target) {
        int start = -1;
        int end = -1;
        for(int i=0;i<nums.length;i++){
            if(nums[i]==target){
                start = i;
                break;
            }
        }
        if(start == -1){
            return new int[]{start,end};
        }
        for(int i=start;i<nums.length;i++){
            if(nums[i]>target){
                end = i-1;
                return new int[]{start,end};
            }
        }
        if(nums[nums.length-1] == target){
            end = nums.length-1;
        }
        
        return new int[]{start,end};
    }
}
```

### 36.判断当前九宫格中填入数据是否有效

##### 预备知识

* 