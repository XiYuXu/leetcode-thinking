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

##### 知识点




#### [面试题 01.09. 字符串轮转](https://leetcode.cn/problems/string-rotation-lcci/)

> 今天碰到的一个比较搞的题，因为是旋转字符串嘛，自然想到根据取模来逐个字符判断是不是一样的。于是有了下面这个算法，通过不断地寻找s1字符串第一个字符出现的位置，然后通过取模运算不断地对比相应位置是否相等，若比到第length个字符当然就返回true了，否则一直找到没有s1[0]开头了就返回false了。

```C
bool isFlipedString(char* s1, char* s2){
    int length = strlen(s1);
    if(length!=strlen(s2)){
        return false;
    }else if(length==0){
        return true;
    }
    int index = 0;
    char *p = NULL;
    char *temp = s2;
    p = strchr(temp,s1[0]);
    int i;
    while(p!=NULL){
        for(i=1;i<length;i++){
            //printf("si=%c index=%d s2[*]=%c\n",s1[i],(int)(p-s2+i),s2[((p-s2+i)%length)]);
            if(s1[i]  != s2[((p-s2+i)%length)]){
                p = strchr(p+1,s1[0]);
                i=0;
                break;
            }
            continue;
        }
        if(i==length){
            return true;
        }
    }
    return false;
}
```

> 但是显然有更好的办法，比如如果两个字符串是旋转来的，那么s2+s2肯定是在中间构造出了一个新的s1
>
> 比如 s2是s1在第i个位置翻转来的，s1 = {ch0,ch1 .....chi...chn},那么s2={chi...chn,ch0,...chi-1},所以s2+s2就构造了（chi...chn,**ch0...chi-1..chi...chn**,ch0,...chi-1）,中间这一段就是s1啦。
>
> 所以最后的代码就转为（下面是说s1+s1包含s2，也是一样的）

```C
bool isFlipedString(char* s1, char* s2){

    if (strlen(s1) != strlen(s2)) {
        return false;
    }
    int len = strlen(s1);
    int i = 0;
    char* arr = (char*)malloc(sizeof(char) * len * 2 + 1);
    for (; i < 2 * len; i++) {
        arr[i] = s1[i % len];
        //printf("%c ", arr[i]);
    }
    arr[i] =  '\0';
    if (strstr(arr, s2) != NULL){
        return true;
    }

    return false;
}
```
* boolean初始化语句可以直接初始化

  ```java
  
  class Solution {
  	/*******
  	*	思路：
  	*	1.要保证每个输入数据都有效，则每一行、每一列、每个排列的3x3范围的数据最多出现一次
  	*   2.由于值范围为0~9，因此将每一行、每一列、每个area，定义作为单个长度为9的数组，
  	*   3.对九宫格进行遍历，用nums[i]==true表示值为i+1的数存在。
  	*	4.每次遍历到的值先判断是否在该对应行、列、块的数组出现过，不断更新三个数组
      */
      public boolean isValidSudoku(char[][] board) {
          boolean [][]row = new boolean[9][9];    //9个行数组
          boolean [][]column = new boolean[9][9]; //9个列数组
          boolean [][]area = new boolean[9][9];   //9个块数组
          //遍历每一个值
          for(int i=0;i<board.length;i++){
              for(int j=0;j<board[i].length;j++){
                  if(board[i][j]=='.')
                      continue;
                  //获取该值在0-8范围的下标
                  int temp = board[i][j]-'1';
                  //块序号
                  int index = i/3*3+j/3;
                  //若是存在对应行列块数组存在该值下标的真值，则返回false
                  if(row[i][temp] || column[j][temp] || area[index][temp]){
                      return false;
                  }else{
                      row[i][temp] = true;
                      column[j][temp] = true;
                      area[index][temp] = true;
                  }
              }
          }
          return true;
      }
  }
  ```

  ### 66.加一
  
  ```java
  /************
  *   思路：数组加一，意味着每一位如果不为9，则无进位
  *        也即若加1不为10，则没有进位，也就可以结束循环
  */  影响数组长度只有一种情况，9，99，999这些情况的证明为最后一位仍有进位
      class Solution {
          public int[] plusOne(int[] digits) {
              for(int i=digits.length-1;i>=0;i--){
  				digits[i]++;
                  digits[i] = (digits[i])%10;
                  if(digits[i]!=0){
                      return digits;  //若没返回，说明不是999之类的情况
                  }
              }
              //执行到这里说明原数格式为999的模式
              digits = new int[digits.length+1];  //产生类似1000
              digits[0] = 1;						//把第一位置为0即可描述999+1
             	return digits;     
          }
  }
  ```
  



#### [921. 使括号有效的最少添加](https://leetcode.cn/problems/minimum-add-to-make-parentheses-valid/)

> 要保证组成有效的括号，这个题其实还蛮简单的，需要注意的是测试用例有“())”,还有“)(”这样子的。原来想着好像把左括号看成1，右括号为-1，最后算一下求和绝对值。
>
> 但是只能面对前面这样子的字符串。所以为了防止右边这样的用例（必须往左填充左括号，就是把前面累加完为0后，接着直接碰到右括号了），我们就要统计这样子的情况。
>
> 总而言之：这两个用例反映出的心法就是 正值可以被负值抵消，但是负值是不可被正值抵消的
>
> 于是有

```C
int minAddToMakeValid(char * s){
    int tempSum = 0;       //保存临时计算和
    int result = 0;        //用来保存最终结果，为（直接碰到的右括号数）以及累加的绝对值
    for(int i=0;i<strlen(s);i++){
        if(s[i]=='('){
            tempSum++;
        }else{
            tempSum--;
        }
        //前面累加完为0后，直接碰到右括号了就把tempSum归0，并从该右括号后一个数开始遍历
        if(tempSum<0){     
            result++;
            tempSum=0;
        }
    }
    return result+tempSum;
}
```