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

31

