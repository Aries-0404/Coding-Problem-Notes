# ACWing算法基础课



# 一、基础算法

## 1、快速排序

### 是什么：

在数组中找寻一个基准值key，然后将数组元素分为两类：一类是大于基准值的放在它的右边，一类是小于基准值的放在它的左边（要想实现这个过程最直接的就是利用插入元素的方式，但是插入元素的工作量太大，得移动好多元素来为插入的元素腾出位置来，所以最好的还是利用两个元素交换位置的方式因为这样只涉及到两个位置的元素进行操作），这样最后可以找到基准值在数组中的位置，接下来就是对于基准值左边和右边的子数组按照同样的方式继续进行上述过程。

这个过程的平均时间复杂度为O(nlogn)（每次选取的基准值都能刚好将数组元素对半分），最差时间复杂度为O(n^2)（每次选取的基准值都是在最边上的）

基本思想就是如上所示，但是具体实现方式有如下几种：



### notes：快速排序算法的时间复杂度（平均时间复杂度为O(nlogn)，这是当每次的基准选的比较好的时候每次都能将数组平均分成两组）

![1681630520085](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681630520085.png)





### notes：快速选择算法的时间复杂度（平均时间复杂度为O(n)）

![1681631775686](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681631775686.png)





### 怎么办：

#### 方式一：双指针法

是什么：

选定数组最左边(或右边)的元素作为基准元素key（**为什么要选取最左边(或右边)的元素作为基准元素呢？因为这里的思想就是以基准元素作为标准然后将剩余元素分为两类，一类是大于基准的一类是小于基准的，所以最好的办法就是先将基准元素放到一边这样不影响剩余元素分类，否则的话如果以中间某个元素作为基准然后后续遍历的时候又碰到了该基准元素就很麻烦**），然后设立一前一后两个指针left,right，其中left从前往后遍历寻找比基准值key大的元素，right从后往前遍历寻找比基准值key小的元素，然后交换它们二者的位置，重复上述过程直到最后左指针left越过了右指针right（为什么是越过了右指针而不是刚好二者碰头？因为目的是要遍历数组中的所有元素，当碰头后的那个元素也是需要遍历的即循环条件是：while(left<=right)而不是while(left<right) ）

![20210515134431451](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/20210515134431451.gif)



这里还得注意一个重要的点那就是最后基准元素到底是和左右指针的哪一个进行位置交换呢？这里只取决于一个因素：当左右指针碰头后的元素值

![1680525831863](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1680525831863.png)

当左右指针碰头后：此时左指针i的左边都是小于等于基准值的元素，右指针j的右边都是大于等于基准值的元素；如果此时的元素值比基准值大则执行右指针j的左移，则i指针越过了j指针结束循环，此时的i指针指向的元素值大于基准值（i指针就在原地没有动），此时的j指针指向的元素值小于等于基准值，所以应该将基准值与j指针所在位置的元素进行交换；如果左右指针碰头后的元素值比基准值小则执行左指针的右移，则i指针越过了j指针结束循环，此时的i指针指向的元素值大于基准值，此时的j指针指向的元素值小于基准值（j指针就在原地没有动），所以应该将基准值与j指针所在位置的元素进行交换

综上：不管是先移动左指针还是先移动右指针，也不管最后当结束循环时是哪个指针移动导致的，最后基准的位置都是i-1位置也就是j位置（可以这样更方便的理解，**i指针的目的是为了从前往后寻找比基准大的元素，j指针的目的是为了从后往前寻找比基准小的元素，即使最后当结束循环以后i指针越过了j指针，i指针指向的还是大于基准的元素j指针指向的还是小于基准的元素**，不管当碰头以后是i指针主动移动导致的结束循环还是j指针主动移动导致的结束循环都是如此）

#### notes：对快速排序算法的改进（随机选取一个元素作为基准值）

由上述过程我们可以知道每次都取数组的第一个元素作为基准值这种方式的确可行但是不一定好，比如说现在数组是逆序的，然后我们每次取数组第一个元素作为基准值这样每次划分的时候都会将剩余数组元素全部分到一边，这样的话时间复杂度为O(n^2)，所以这里我们随机取数组中的一个元素作为基准值然后执行上述过程，**但是不对啊，前面我们说过了基准值得放到一边这样不耽误别的元素分类但是现在随机选取的基准值就不是在数组的最边上了，那也没问题只要我们选定了随机的元素作为基准以后接着将该元素与数组最边上的元素交换位置不就行了嘛**

这里会用到一个产生随机整数的方法Random.nextInt(k)：会产生一个[0，k)的随机整数，不包括k（其中nextInt()为动态方法，所以首先还得创建一个Random类的对象）

```Java
//双指针，先移动左指针的方式
class Solution{
    public void quickSort(int[] nums,int left,int right){
        if(left<right){
            //这里的index为基准值最后在数组中的位置，由此将数组分为两部分
         	int index=partition(nums,left,right);
            
        	quickSort(nums,left,index-1);
        	quickSort(nums,index+1,right);   
        }
        return;
    }
    
    //定义一个用于找寻基准值最后在数组中的位置的方法
    public int partition(int[] nums,int left,int right){
        int key=left;//首先确定取数组最左边的元素作为基准值
        int i=left,j=right;//设立一前一后的两个指针
        while(i<=j){//注意这里的循环结束条件
            while(i<=j && nums[i]<=nums[key]){//先移动左指针
                i++;
            }
            while(i<=j && nums[j]>=nums[key]){//后移动右指针
                j--;
            }
            if(i<j){
                //这里为什么要加这个条件？因为当结束上述两个while循环以后此时i指针越过了j指针，
                //这时候是不应该交换两个元素位置的
                swap(nums,i,j);
            }
        }
        swap(nums,key,i-1);//这里是将i-1位置作为最后基准元素的位置
        //swap(nums,key,j);二者是等效的，因为此时i-1位置就是j位置
    }
    //定义交换数组中两个元素位置的方法
    public void swap(int[] nums,int i,int j){
        int temp=nums[i];
        nums[i]=nums[j];
        nums[j]=temp;
    }
}




//双指针，先移动右指针的方式
class Solution{
    public void quickSort(int[] nums,int left,int right){
        if(left<right){
            //这里的index为基准值最后在数组中的位置，由此将数组分为两部分
         	int index=partition(nums,left,right);
            
        	quickSort(nums,left,index-1);
        	quickSort(nums,index+1,right);   
        }
        return;
    }
    
    //定义一个用于找寻基准值最后在数组中的位置的方法
    public int partition(int[] nums,int left,int right){
        int key=left;//首先确定取数组最左边的元素作为基准值
        int i=left,j=right;//设立一前一后的两个指针
        while(i<=j){//注意这里的循环结束条件
            while(i<=j && nums[j]>=nums[key]){//先移动右指针
                j--;
            }
            while(i<=j && nums[i]<=nums[key]){//后移动左指针
                i++;
            } 
            if(i<j){
                swap(nums,i,j);
            }
        }
        swap(nums,key,j);//这里是将j位置作为最后基准元素的位置
    }
    //定义交换数组中两个元素位置的方法
    public void swap(int[] nums,int i,int j){
        int temp=nums[i];
        nums[i]=nums[j];
        nums[j]=temp;
    }
}

//双指针，随机取基准值
class Solution{
    public void quickSort(int[] nums,int left,int right){
        if(left<right){
            //这里的index为基准值最后在数组中的位置，由此将数组分为两部分
         	int index=partition(nums,left,right);
            
        	quickSort(nums,left,index-1);
        	quickSort(nums,index+1,right);   
        }
        return;
    }
    
    //定义一个用于找寻基准值最后在数组中的位置的方法
    public int partition(int[] nums,int left,int right){
        Random rand=new Random();
        int key=rand.nextInt(right-left+1)+left;//随机取一个元素作为基准值
        swap(nums,left,key);//将随机取的基准值放到数组的最边上，这样不耽误剩余元素分类
        int i=left,j=right;//设立一前一后的两个指针
        while(i<=j){//注意这里的循环结束条件
            while(i<=j && nums[i]<=nums[key]){//先移动左指针
                i++;
            }
            while(i<=j && nums[j]>=nums[key]){//后移动右指针
                j--;
            }
            if(i<j){
                swap(nums,i,j);
            }
        }
        swap(nums,key,i-1);//这里是将i-1位置作为最后基准元素的位置
        //swap(nums,key,j);二者是等效的，因为此时i-1位置就是j位置
    }
    //定义交换数组中两个元素位置的方法
    public void swap(int[] nums,int i,int j){
        int temp=nums[i];
        nums[i]=nums[j];
        nums[j]=temp;
    }
}
```



#### 方式二：单指针法

是什么：

和上述双指针法的过程差不多还是选取数组最左边的元素作为基准，就是这里仅仅只用一个指针从前往后遍历，当遍历到数组最后一个元素的时候代表遍历完成，那么仅仅只用一个指针那如何实现交换位置呢又和谁交换位置呢？这里我们用那个单指针去寻找比基准小的元素，然后用一个指针index去指向最左边的基准元素后面一个位置，当单指针找到了比基准小的元素就将它与index指针指向的元素交换位置，然后index指针后移一位，重复上述过程，这样当遍历完数组以后比基准小的元素都集中在了数组的前半部分，接着将基准元素与index指针前一个位置的元素进行交换位置即可（为什么是index指针前一个位置？因为当单指针找到比基准值小的元素以后就会和index指针的元素交换位置然后index指针接着就会后移一位，此时index指针就会指向比基准值大的元素）

**单指针法相对双指针法的好处就是更清楚数组遍历的过程什么时候结束，以及最后当结束遍历的时候基准到底是和谁交换位置**

```Java
public class Solution{
    public void quickSort(int[] nums,int left,int right){
        if(left<right){
            //这里的index作为基准元素的最后在数组中的位置下标
        	int index=partition(nums,left,right);
        	quickSort(nums,left,index-1);
        	quickSort(nums,index+1,right); 
        }
        return;
    }
    public int partition(int[] nums,int left,int right){
        int key=left;//选取数组最左边的元素作为基准元素
        int i=left;
        int index=left+1;//用于接收比基准值小的数组元素
        while(i<=right){
            while(nums[i]<=nums[key]){
                i++;
            }
            swap(nums,index,i);
            index++;
        }
        return index-1;
    }
    public void swap(int[] nums,int i,int j){
        int temp=nums[i];
        nums[i]=nums[j];
        nums[j]=temp;
    }
}
```



### （785）快速排序

![1680879687829](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1680879687829.png)

解题思路：

利用快速排序算法将数组进行重排，但是一般的利用partition()函数找到基准元素最后在数组中的位置的方法会造成超时，所以这里利用了一种全新的方法，但是这个方法不是很好理解

```java
//利用partition()函数找到基准元素最后在数组中的位置
import java.util.Scanner;
import java.util.Random;
public class Main{
    private static final int N=100010;
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        int n;
        n=sc.nextInt();
        int[] nums=new int[N];
        for(int i=1;i<=n;i++){
            nums[i]=sc.nextInt();
        }
        quickSort(nums,1,n);
        for(int i=1;i<=n;i++){
            System.out.print(nums[i]+" ");
        }
    }
    public static void quickSort(int[] nums,int left,int right){
        if(left<right){
            int index=partition(nums,left,right);
            quickSort(nums,left,index-1);
            quickSort(nums,index+1,right);
        }
        return;
    }
    public static int partition(int[] nums,int left,int right){
        // Random rand=new Random();
        // //随机选取一个基准元素
        // int key=rand.nextInt(right-left+1)+left;
        
        int key=(left+right)/2;//选取数组中间一个元素作为基准
        //将基准元素换到数组最左边，这样不妨碍其他元素
        swap(nums,left,key);
        key=left;//将基准元素换到最左边以后基准元素对应的下标也得更换
        int i=left,j=right;
        while(i<=j){
            while(i<=j && nums[i]<=nums[key]){
                i++;
            }
            while(i<=j && nums[j]>=nums[key]){
                j--;
            }
            if(i<j){
                swap(nums,i,j);
            }
        }
        swap(nums,key,j);
        return j;
    }
    public static void swap(int[] nums,int i,int j){
        int temp=nums[i];
        nums[i]=nums[j];
        nums[j]=temp;
    }
}


//视频中介绍的一种全新的方法，但是不是很好理解
import java.util.Scanner;
import java.util.Random;
public class Main{
    private static final int N=100010;
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        int n;
        n=sc.nextInt();
        int[] nums=new int[N];
        for(int i=1;i<=n;i++){
            nums[i]=sc.nextInt();
        }
        quickSort(nums,1,n);
        for(int i=1;i<=n;i++){
            System.out.print(nums[i]+" ");
        }
    }
    public static void quickSort(int[] nums,int left,int right){
        if(left<right){
            //选取数组中间的元素作为基准
            int mid=nums[(left+right)/2];
            int i=left-1,j=right+1;
            while(i<j){
            /*
            为什么这里的循环条件是i<j,而不是i<=j?
            */
                do{i++;}while(nums[i]<mid);
                do{j--;}while(nums[j]>mid);
                if(i<j){
                    swap(nums,i,j);
                }
            }
            quickSort(nums,left,j);
            quickSort(nums,j+1,right);
        }
        return;
    }
    public static void swap(int[] nums,int i,int j){
        int temp=nums[i];
        nums[i]=nums[j];
        nums[j]=temp;
    }
}
```



### （786）第k个数（快速选择算法）

![1680879820336](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1680879820336.png)

解题思路：

快速选择算法是基于快速排序算法的，它的平均时间复杂度就为O(n)（具体证明见上图）。具体来说它是这样做的，首先还是和快排一样找到基准元素，然后找到它在数组中的最后的位置，假如最后基准元素在数组中的位置对应的就是我们要找的数组中的第k个最大的数那么我们直接返回该元素值就好，如果最后基准元素在数组中的位置比我们要找的第k个最大的数要小那么我们直接去该基准元素右半边数组中再去进行递归寻找第k个最大元素否则就去该基准元素左半边数组中去寻找第k个最大元素

```Java
import java.util.Scanner;
public class Main{
    private static final int N=100010;
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        int n,k;
        n=sc.nextInt();
        k=sc.nextInt();
        int[] nums=new int[N];
        for(int i=1;i<=n;i++){
            nums[i]=sc.nextInt();
        }
        System.out.println(quickSelect(nums,1,n,k));
    }
    public static int quickSelect(int[] nums,int left,int right,int k){
        if(left<=right){
            int index=partition(nums,left,right);
            if(index==k){
                return nums[index];
            }else if(index<k){
                return quickSelect(nums,index+1,right,k);
            }else{
                return quickSelect(nums,left,index-1,k);
            }
        }
        return 0;
    }
    public static int partition(int[] nums,int left,int right){
        //选取数组中间元素作为基准
        int key=(left+right)/2;
        //将基准换到数组最左边
        swap(nums,left,key);
        key=left;
        
        int i=left,j=right;
        while(i<=j){
            while(i<=j && nums[i]<=nums[key]){
                i++;
            }
            while(i<=j && nums[j]>=nums[key]){
                j--;
            }
            if(i<j){
                swap(nums,i,j);
            }
        }
        swap(nums,key,j);
        return j;
    }
    public static void swap(int[] nums,int i,int j){
        int temp=nums[i];
        nums[i]=nums[j];
        nums[j]=temp;
    }
}
```





## 2、归并排序

### 是什么：

归并排序是一种基于分治思想的高效排序算法，其核心思想是将待排序的序列拆分成两个子序列，对每个子序列再进行拆分直至不可拆分然后对相邻的子序列进行排序合并，然后再将排好序的子序列合并成一个有序的整体序列。归并排序的时间复杂度为O(nlogn)，是一种稳定的排序算法。 

![1681628832224](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681628832224.png)



### 怎么办：

先取中间位置的元素将数组一分为二，然后对两个子数组继续取它们各自中间位置的元素将子数组继续一分为二，重复上述步骤，直至子数组中只含有一个元素，然后就可以合并相邻的子数组了，先设置一个临时数组暂时存放合并后的相邻的子数组，然后将临时数组的元素值赋值给原数组，重复上述步骤直到整个数组有序



### （787）归并排序

![1681628250079](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681628250079.png)

解题思路：

本题就是利用归并排序实现对数组元素排序的典型题目

```Java
import java.util.*;
public class Main{
    private static int N=100010;
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        int n;
        n=sc.nextInt();
        int[] nums=new int[N];
        for(int i=1;i<=n;i++){
            nums[i]=sc.nextInt();
        }
        int l=1,r=n;
        merge_sort(nums,l,r);
        for(int i=1;i<=n;i++){
            System.out.print(nums[i]+" ");
        }
    }
    public static void merge_sort(int[] nums,int l,int r){
        if(l>=r){
            return;
        }
        int mid=l+r>>1;
        merge_sort(nums,l,mid);
        merge_sort(nums,mid+1,r);
        int i=l,j=mid+1;//用两个指针i,j分别指向两个有序子数组的最低位
        int[] temp=new int[r-l+1];//创建临时数组用于存放将两个有序子数组合并后的结果，然后还是得将这个数组的值重新赋给原数组nums
        int k=0;
        while(i<=mid && j<=r){
            if(nums[i]<nums[j]){
                temp[k++]=nums[i++];
            }else{
                temp[k++]=nums[j++];
            }
        }
        while(i<=mid){
            temp[k++]=nums[i++];
        }
        while(j<=r){
            temp[k++]=nums[j++];
        }
        for(i=l,j=0;i<=r;i++,j++){//此时的temp已经是将两个有序数组合并后的有序数组了，将它的值重新赋给原数组nums
            nums[i]=temp[j];
        }
    }
}
```



### （788）逆序对的数量

![1681652345121](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681652345121.png)

解题思路：

在将有序的左右子数组合并成一个新的有序子数组的过程中，我们是利用双指针的不断移动实现元素数值比较的，要想实现逆序对，则要找出在右子数组中的数值比左子数组中的数值小的数值对（比如在右子数组中选定数值2以后，可以在左子数组中选定数值3、5、7、9构成（3，2）、（5，2）、（7，2）、（9，2）四个逆序对，那么子数组本身的逆序对不考虑了吗？当然不考虑，因为子数组已经排过序了啊，所以子数组本身不存在逆序对，而且在子数组的构成过程中也是由子数组的子数组构成的，在子数组的子数组的合并过程中已经计算过了逆序对啊），综上只要我们在合并两个有序子数组的过程中注意统计右数组的j指针指向的元素比左数组的i指针指向的元素小的情况就能够统计出来总共有多少个逆序对了

还有一个注意点就是，这里总共有n个数（n最大为10^5），这n个数最多可以构成多少个逆序对呢？当它们逆序排列的时候构成的逆序对数目最多，为（n-1）+（n-2）+（n-3）+...+1=n*（n-1）/2，为O(n^2)数量级，而这里的n=10^5，所以n^2=10^10，所以最多有10^10个逆序对，已经超过了int型的表示范围，所以如果你想用int型数据来记录总共有多少个逆序对的话是行不通的，这里得用long型数据

![1681654014019](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681654014019.png)

```Java
import java.util.*;
public class Main{
    public static int N=100010;
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        int n;
        n=sc.nextInt();
        int[] nums=new int[N];
        for(int i=1;i<=n;i++){
            nums[i]=sc.nextInt();
        }
        // int count=merge_sort(nums,1,n);
        //这里为什么不用int型数据记录总共有多少个逆序对？
        //因为数据超出int型的表示范围
        long count=merge_sort(nums,1,n);
        System.out.println(count);
        return;
    }
    public static long merge_sort(int[] nums,int l,int r){
        if(l>=r){
            return 0;
        }
        int mid=l+r>>1;
        // int count=merge_sort(nums,l,mid)+merge_sort(nums,mid+1,r);
        long count=merge_sort(nums,l,mid)+merge_sort(nums,mid+1,r);
        int[] temp=new int[r-l+1];
        int i=l,j=mid+1;
        int k=0;
        while(i<=mid && j<=r){
            if(nums[i]<=nums[j]){
                temp[k++]=nums[i++];
            }else{
                /*
                当右数组指针j指向的元素比左数组指针i指向的元素小的时候，那么在右数组中取出
                指针j指向的元素可以构成多少个逆序对呢？
                答案就是mid-i+1
                */
                temp[k++]=nums[j++];
                count+=mid-i+1;
            }
        }
        while(i<=mid){
            temp[k++]=nums[i++];
        }
        while(j<=r){
            temp[k++]=nums[j++];
        }
        for(i=l,j=0;i<=r;i++,j++){
            nums[i]=temp[j];
        }
        return count;
    }
}
```





## 3、二分

### 是什么：

二分是一种常用的算法思想，也称为二分查找。在有序数组中二分通过每次查找数组中间位置的元素并比较该元素与待查找元素之间的大小关系每次都可以将搜索范围缩小一半，将O(n)的查找时间复杂度降低到O(log n)级别，具有高效、简单、易实现等特点。

二分查找算法的核心思想是：每次查找时将待查找区间一分为二，如果中间元素大于要查找的值(target)，则在左区间继续查找；如果中间元素小于要查找的值，则在右区间继续查找。不断重复以上操作，直到在某一次循环中找到要查找的值，或者待查找区间为空（即要查找的值不存在于数组中）。

 二分查找算法的前提条件是：数组必须是有序的，否则无法通过比较查找值与数组中的元素大小来确定查找方向。 

上述描述的都是在数组中查找一个元素值的情况，这样的情况很清楚，要么就是在循环中找到了并返回该元素值的下标，要么就是最后循环结束后没找到元素值最后返回-1

```Java
//二分查找有序数组的元素问题
public int binarySearch(int[] nums,int target){
    int left=0,right=nums.length-1;
    while(l<=r){
        int mid=left+right>>1;
        if(nums[mid]==target){
            return mid;
        }else if(nums[mid]<target){
            left=mid+1;
        }else{
            right=mid-1;
        }
    }
    return -1;
}
```





### notes：二分查找数组区间问题（注意边界以及下标越界问题）

二分查找数组区间问题是什么？

意思就是说一个有序数组，里面的元素可能是重复的，现在让你来求元素值target在数组中第一次出现以及最后一次出现时的下标（注意这里的target也有可能不在数组中，即最后找不到的情况），首先这里需要明确的一点是假如数组中存在目标元素target的话则无论是左边界还是右边界一定都是可以求出来的；假如数组中不存在目标元素的话则求不出来，但是不论是求出来还是没求出来最后当结束循环时都是指针left>right，根本就没法通过此来判断是否找到了边界，所以当结束循环的时候我们还得判断一下是否找到了左边界或者右边界（找到了左边界就不用再判断右边界是否存在了，因为一定存在，我们判断左边界是否存在的原因是为了判断该目标元素是否在数组中，如果在的话则返回它的左边界下标，不能当循环结束后直接返回下标，你都不存在还返回个毛线）

![1681721392553](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681721392553.png)

```java 
//二分查找数组区间问题

//（1）二分查找区间左边界
public int leftBinarySearch(int[] nums,int target){
    int left=0,right=nums.length-1;
    while(left<=right){
        int mid=left+right>>1;
        if(nums[mid]<target){
            left=mid+1;
        }else if(nums[mid]>target){
            right=mid-1;
        }else{//当nums[mid]=target时，因为我们想找区间的左边界，所以还想继续往左边扩展
            right=mid-1;
        }
    }
    /*
    当上述循环结束时的标志统一都是left>right,不管有没有找到区间左边界最后都是left>right,即使找到了
    区间左边界又是哪一个指针指向它我们现在都是不知道的，需要分析：最后当结束循环前left指针和right指针
    一定是指向同一个元素的，可关键就是这个元素到底是不是要找的target呢？见上图总共可分为三种情况：
    （1）nums[mid]=target
    （2）nums[mid]<target
    （3）nums[mid]>target
    结合上图的三种情况我们得知假如数组中存在目标元素target的话当循环结束时一定是left指针指向target元素
    所以我们可以由此进行判断，假如循环结束时nums[left]==target则left指针即我们要寻找的区间左边界，假如
    循环结束时nums[left]!=target则该数组中不存在target元素也就不存在返回左边界了。
    寻找区间右边界的情况也是同理，假如数组中存在右边界的话最后当循环结束后一定是right指针指向target元素
    我们可以由此进行判断，假如循环结束时nums[right]==target则right指针即我们要寻找的区间右边界，假如
    循环结束时nums[right]!=target则该数组中不存在target元素也就不存在返回右边界了。
    
    不过假如在同一道题中当我们已经判断过数组中是否存在左边界以后就不用再判断数组中是否存在右边界了，因为假		如有左边界的话一定存在右边界。
    */
    if(nums[left]==target){
        return left;
        /*
        这里还有一个问题，那就是这里我们直接用的nums[left]而没有考虑left指针越界的问题，那么left指针
        会发生越界吗？
        当然会发生越界啊，当数组元素全部小于目标值target时left指针最后就会发生越界，所以我们有
        两种解决办法：
        （1）当循环结束后检查一下下标是否越界
        （2）在当初创建数组的时候就把数组范围开大一点防止下标越界
        一般采取第二种方案更简单
        
        
        这里还有一个问题，那就是不仅left指针会发生越界，right指针也会发生越界的啊，当数组元素
        全部大于目标值target时right指针最后就会发生越界，那为什么我们在这里不用额外再考虑这个情况呢？
        因为当数组元素全部大于目标值target时，那就说明根本找不到目标值target，也就是left指针根本
        没法指向目标值target，所以根本不用额外考虑
        */
    }
    return -1;
}

//（2）二分查找区间右边界
public int rightBinarySearch(int[] nums,int target){
    int left=0,right=nums.length-1;
    while(left<=right){
        int mid=left+right>>1;
        if(nums[mid]<target){
            left=mid+1;
        }else if(nums[mid]>target){
            right=mid-1;
        }else{//当nums[mid]=target时，因为我们想找区间的右边界，所以还想继续往右边扩展
            left=mid+1;
        }
    }
    /*
    寻找区间右边界的情况也是同理，假如数组中存在右边界的话最后当循环结束后一定是right指针指向target元素
    我们可以由此进行判断，假如循环结束时nums[right]==target则right指针即我们要寻找的区间右边界，假如
    循环结束时nums[right]!=target则该数组中不存在target元素也就不存在返回右边界了。
    */
    if(nums[right]==target){
        return right;
    }
    return -1;
}



/*
不过还有一种对于上述算法的改进方法，上述方法的问题就在于最后结束循环的时候并不知道到底有没有左边界以及假如
有左边界的话到底哪个指针指向左边界，基于这个问题其实只要我们额外再用一个指针指向左边界并且给这个指针初始化为-1并且只有在找到左边界的时候才给这个指针重新赋值，最后我们只要返回这个指针就可以知道到底有没有左边界以及
左边界是多少了
*/
//改进后的寻找数组区间的二分法
public int leftBinarySearch(int[] nums,int target){
    int left=0,right=nums.length-1;
    int lindex=-1;//专门用来记录区间的左边界的变量
    while(left<=right){
        int mid=left+right>>1;
        if(nums[mid]<target){
            left=mid+1;
        }else if(nums[mid]>target){
            right=mid-1;
        }else{//当nums[mid]=target时，因为我们想找区间的左边界，所以还想继续往左边扩展
            right=mid-1;
            lindex=mid;
        }
    }
    return lindex;
}
/*
通过上述方法直接就越过了判断到底有没有边界的问题。更加便于理解，但是一般都是使用前面的方法而不是改进的方法，反正知道这么个事就行了。知道当用二分法寻找左边界的时候是指针left指向目标元素，当寻找右边界的时候是指针right指向目标元素
*/
```



### 总结：当用二分法寻找区间问题时，在寻找左边界时假如数组中存在目标元素当结束循环时一定是左指针left指向目标元素，在寻找右边界时假如数组中存在目标元素当结束循环时一定是右指针right指向目标元素







### 怎么办：

### （789）数的范围

![1681652426892](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681652426892.png)

解题思路：

这题与普通的二分查找的区别就在于数组中的元素是会重复的，假如让你找一个重复元素的话不仅仅是将它的一个下标找出来而是将它的下标范围找出来，所以得找到该元素在数组中的坐标下界还有坐标上界。这里得十分注意数组下标问题

```Java
import java.util.*;
public class Main{
    public static int N=100010;
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        int n,q;
        n=sc.nextInt();
        q=sc.nextInt();
        int[] nums=new int[N];
        for(int i=0;i<n;i++){
            nums[i]=sc.nextInt();
        }
        while(q>0){
            q--;
            int k;
            k=sc.nextInt();//表示要在有序数组中查找的元素
            int l=0,r=n-1;
            while(l<=r){//先找数组中数值等于k的区间左边界
                int mid=l+r>>1;
                if(nums[mid]>=k){
                    /*
                    为什么这里的if()条件是nums[mid]>=k，而不是nums[mid]>k?
                    因为我们单独将nums[mid]==k拉出来单独考虑，现在要找到数组中数值等于k的
                    左边界，然后我们已经知道了nums[mid]==k，所以很自然地我们会想到能不能
                    继续向mid左边扩展看看是否还有数组元素等于k呢？所以我们就令r=mid-1继续
                    向mid左边扩展
                    */
                    r=mid-1;
                }else{
                    l=mid+1;
                }
            }
            /*
            这里还有一个很重要的问题，那就是当结束上述循环的时候l>r，那么这时候到底是l指针指向
            目标值k呢？还是r指针指向目标值k呢？还是都不是指向目标值k的呢？
            先说结论有可能两个指针都不是指向目标值k的（假如数组中本身就没有目标值k那你怎么找都不会
            找到目标元素k的），假如数组中有目标值k那么最后结束循环的时候又是哪一个指针指向k呢？
            当数组中有目标值k的时候，最后当结束循环前l指针和r指针一定碰头了并且指向的元素就是k，此时
            是r指针的左移结束了上述循环，所以最后一定是l这个下标的元素是k
            */
            if(nums[l]!=k){
                System.out.println("-1 -1");
            }else{
                System.out.print(l+" ");
                l=0;
                r=n-1;
                while(l<=r){//再找数组中数值等于k的右边界
                    int mid=l+r>>1;
                    if(nums[mid]<=k){
                        /*
                        上面我们已经解释过为什么if()条件里面是nums[mid]>=k，而不是nums[mid]>k?
                        为什么到这里if()条件就变成了nums[mid]<=k呢？
                        同样我们得弄清楚这里要研究的是什么问题？
                        这里我们得寻找数组中数值等于k的右边界，当nums[mid]==k时我们会很自然地想
                        能不能继续往mid右边扩展看看是否还有元素等于k呢？所以我们就令l=mid+1继续
                        向mid右边扩展
                        */
                        l=mid+1;
                    }else{
                        r=mid-1;
                    }
                }
                System.out.println(r);
            }
            
        }
    }
}
```



### （790）数的三次方根

![1681652560031](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681652560031.png)

解题思路：

同样还是一个二分的思路，给定上界和下界，当中间值的三次方大于目标值则选取左边子区间继续操作，否则选取右边子区间继续操作

一些注意点：

（1）题目要求保留6位小数，根据经验我们一般选取左右边界的间距为8位小数就好

（2）最后输出的时候要求保留6位小数，我们可以利用printf("%.6f",l)方法：其中%为固定格式，6表示小数点后6位，f表示字符类型为浮点数，l为最后要输出的值；这里其实如果不控制精度的话原本的表示应该是这样的：printf("%f",l)，将浮点型数据l给打印出来，现在要求打印出来的数据保留小数点后6位，所以才变成了现在的样子：printf("%.6f",l)

notes：注意一点，printf("%d",x);是将整型数据x打印出来，这里的 "%d" 代表的是整型数据，不是double型数据

```Java
import java.util.*;
public class Main{
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        double n=sc.nextDouble();
        
        double l=-10000,r=10000;
        while(r-l>1e-8){
            // double mid=l+r>>1;只有整数类型除以2才能这样通过右移一位实现
            double mid=(l+r)/2;
            if(mid*mid*mid>n){
                r=mid;
            }else{
                l=mid;
            }
        }
        System.out.printf("%.6f",l);
        /*
        可以使用Java的System.out.printf()函数来通过输出数据的格式来达到保留浮点型数据指定位数的目的。
        其中，“%.6f”表示输出参数的浮点型数据并保留6位小数。需要注意的是，在java中，printf()函数的
        格式化字符串中，使用“%”来表示格式化占位符，后面跟着一个整数表示精度、一个字符表示类型（如f表示浮		  点数）。
        */
    }
}
```







## 4、高精度计算

### 是什么：

高精度计算也被称作大整数计算，当数据的范围（或者是数据运算的结果）超过了int型表示范围的时候也就没法表示了，但是我们又有这种运算的需求所以就创建了高精度计算



### 怎么办：

首先需要解决的就是大整数如何输入的问题，因为大整数已经超过了int型表示范围所以我们是没法创建一个int型数据然后输入数值的，所以这里我们利用的是输入一个字符串的方式（该字符串就是大整数的数值）；还有就是解决大整数如何存储的问题，一个整数但是它已经超过了int型数据的表示范围那么我们该如何存储呢？这里想到就是用int型数组来挨个存储大整数的每一位，但是还有一个问题那就是用数组存储的话首先得确定数组的长度，而数组的长度得取决于实际的大整数，这就很不方便，所以我们换个办法改用集合来存储大整数的每一位，这里指定集合的泛型为Integer类型；这里还得注意一个问题，因为这里涉及高精度的计算加减乘除等，很多运算会产生进位而如果我们利用集合顺序的存储大整数的每一位（即先存储百位、十位、个位）这样后续计算结果产生了进位我们没法在集合的首元素位置给它添加一个数，所以这里我们利用集合逆序的存储大整数的每一位，这样即使最后的运算结果产生了进位我们也只要在集合的末尾添加元素就好



### 常见的高精度计算类型：（A、B：代表高精度数（大整数）；a、b：代表单精度数（普通int型整数））

#### 一、高精度加法：A+B

#### （791）高精度加法

![1680962493145](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1680962493145.png)

解题思路：

这里首先利用字符串读入两个高精度数，然后分别再用两个集合存储这两个高精度数，最后让这两个集合的对应位分别相加即可，要注意的是两个集合的低位数字相加可能会产生进位，还得注意两个集合可能不一样长，最后还有一点那就是假如两个集合一样长的话最后的高位相加也可能产生进位别忘了这一点

```java
import java.util.Scanner;
import java.util.List;
import java.util.ArrayList;
public class Main{
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        //利用字符串的方式输入两个高精度数
        String A=sc.next();
        String B=sc.next();
        //利用集合的方式存储高精度数，这里注意要逆序存储，方便后续计算
        List<Integer> list1=new ArrayList<>();
        for(int i=A.length()-1;i>=0;i--){
            list1.add(A.charAt(i)-'0');
        }
        List<Integer> list2=new ArrayList<>();
        for(int i=B.length()-1;i>=0;i--){
            list2.add(B.charAt(i)-'0');
        }
        
        //用于存储两个高精度数相加的结果的集合
        List<Integer> list3=add(list1,list2);
        for(int i=list3.size()-1;i>=0;i--){
            System.out.print(list3.get(i));
        }
        return;
    }
    //定义两个高精度数的加法方法
    public static List<Integer> add(List<Integer> list1,List<Integer> list2){
        List<Integer> list3=new ArrayList<>();
        int t=0;//用于表示两个数组低位相加产生的进位，初始化为0
        for(int i=0; i<list1.size() || i<list2.size(); i++){
            //这里本来是想用t来表示两个数组低位相加的结果，即t=t+list1.get(i)+list2.get(i)，
            //但是两个集合可能不一样长那么对于短的集合来说当i大于它的集合长度时就会产生下标越界异常，
            //所以这里得分类讨论
            if(i<list1.size()){
                t+=list1.get(i);
            }
            if(i<list2.size()){
                t+=list2.get(i);
            }
            list3.add(t%10);
            t/=10;//别忘了数组低位相加可能会产生进位
        }
        //别忘了这里，在两个数组的最高位相加也可能产生进位，这时候它的长度就已经超过了集合的长度了，
        //所以也就不会再进入上述的循环，但是我们还得将进位的结果假如到结果集合中
        if(t>0){
            list3.add(t);
        }
        return list3;
    }
}
```



#### 二、高精度减法：A-B

#### （792）高精度减法

![1680963777115](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1680963777115.png)

解题思路：

大部分和高精度加法类似，但是也有一些特殊点，比如这里就有一个顺序关系：大的数减去小的数可以直接减，但是小的数如何减去大的数呢？如果是int型数据的话我们可以直接减它会自动帮我们计算，但是这里的是大整数，超过了int型表示范围所以需要我们自己定义如何做减法，**回顾一下实际中我们是怎么做的？小的数减去大的数我们是不是还是把转换成大的数减去小的数，然后最后的结果再补上一个负号"-"，这里也是如此，将小的数减去大的数转化成大的数减去小的数，所以我们得首先判断两个大整数的大小（这里没法直接比较两个大整数谁比较大，因为都已经超出int型范围了，所以需要我们从集合中挨个选取元素进行比较）**；第二个特殊点就是加法我们只需要关注低位是否会产生进位就好，但是减法我们得关注当前位置的低位是否够减是否需要借位以及当前位置借了一位以后自己是否够减是否需要像高位借位（**但是总的来说这里其实我们只需要关注当前位置是否给低位借位即可，如果当前位置不够减的话那么最后当前位置相减的结果就是负数，这时候再向高位借1位也是不迟的**）；**最后还有一个问题，那就是两数相减可能会产生前导0**

（比如123-121=002，这里的结果应该是2，它前面的0就是前导0）我们得删去前导0

```Java
import java.util.Scanner;
import java.util.List;
import java.util.ArrayList;
public class Main{
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        String A=sc.next();
        String B=sc.next();
        //创建两个集合用来存储大整数
        List<Integer> list1=new ArrayList<>();
        for(int i=A.length()-1;i>=0;i--){
            list1.add(A.charAt(i)-'0');
        }
        List<Integer> list2=new ArrayList<>();
        for(int i=B.length()-1;i>=0;i--){
            list2.add(B.charAt(i)-'0');
        }
        
        //存储好了两个大整数A,B接下来就是计算它们的差，这里可能为负数
        //如果A<B的话我们没法计算还是得把转换为B-A然后再结果前面添上负号，所以始终是大的数减去小的数
        //所以我们在做减法之前得判断两个大整数的大小关系
        List<Integer> ans=sub(list1,list2);
        for(int i=ans.size()-1;i>=0;i--){
            System.out.print(ans.get(i));
        }
    }
    //定义比较两个大整数的方法
    public static boolean cmp(List<Integer> list1,List<Integer> list2){
        if(list1.size()>list2.size()){
            return true;
        }else if(list1.size()<list2.size()){
            return false;
        }else{
            for(int i=list1.size()-1;i>=0;i--){
                if(list1.get(i)>list2.get(i)){
                    return true;
                }else if(list1.get(i)<list2.get(i)){
                    return false;
                }
            }
            return true;
        }
    }
    //定义两个大整数的减法
    public static List<Integer> sub(List<Integer> list1, List<Integer> list2){
        if(!cmp(list1,list2)){
            //如果大整数A<B的话，则我们反过来计算B-A同时在最后结果前输出一个负号"-"
            System.out.print("-");
            return sub(list2,list1);
        }
        List<Integer> list3=new ArrayList<>();
        /*
        这里两数相减很麻烦，因为你不仅得判断当前位置是否需要给低位借1，还得判断给低位借了1以后当前
        位置是否够大是否还需要向高位再借1（但是总的来说我们只要关注当前位置是否给低位借了1位即可，
        因为即使当前位置不够减的话最后当前位置相减的结果为负数这时候我们再向高位借1位也是不迟的）
        */
        int t=0;//这里的t表示被减数当前位置是否给低位借了1
        for(int i=0;i<list1.size();i++){
            //这里本来应该是用t来表示两个数组当前位置相减的结果，
            //即t=list1.get(i)-t-list2.get(i)，但是因为假如list2很短则可能越界，
            //所以得判断一下
            t=list1.get(i)-t;
            if(i<list2.size()){
                t-=list2.get(i);//这里就表示两数当前位置上相减的结果了，如果不够的话则这里相减
                                //的结果就是负数，这时候再向高位借1位也不迟（向高位借1反映在
                				//低位就是加10）
            }
            list3.add((t+10)%10);
            //如果两个数相减为正数则加10后再余10就是本身，如果两个数相减后为负数则加10后余10就是
            //向高位借1以后计算的结果
            if(t<0){
                t=1;//如果当前位置的数相减小于0则代表需要向高位借1
            }else{
                t=0;
            }
        }
        //去除结果中的前导0
        for(int i=list3.size()-1;i>0;i--){
            if(list3.get(i)==0){
                list3.remove(list3.size()-1);
            }else{
                break;
            }
        }
        return list3;
    }
}
```



#### 三、高精度乘法：A*b

#### （793）高精度乘法

![1680965459063](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1680965459063.png)

解题思路：

注意这里的高精度乘法指的是一个大整数A和一个小整数b的相乘（如果是两个大整数A*B，暂时还不知道如何处理）

```c++
import java.util.Scanner;
import java.util.List;
import java.util.ArrayList;
public class Main{
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        String A=sc.next();
        List<Integer> list1=new ArrayList<>();
        for(int i=A.length()-1;i>=0;i--){
            list1.add(A.charAt(i)-'0');
        }
        int b=sc.nextInt();
        List<Integer> ans=mul(list1,b);
        for(int i=ans.size()-1;i>=0;i--){
            System.out.print(ans.get(i));
        }
    }
    //定义大整数的乘法
    public static List<Integer> mul(List<Integer> list1,int b){
        List<Integer> list2=new ArrayList<>();//用来存储大整数相乘后的结果
        int t=0;//表示两个低位数相乘的进位
        for(int i=0; i<list1.size() || t!=0; i++){
            //这里也许当最高位相乘会产生进位，这时候就超过list1.size()-1了
            if(i<list1.size()){               
                t+=list1.get(i)*b;//这里用t表示低位相乘的结果
            }
            list2.add(t%10);
            t/=10;
        }
        //去除结果中前导0的操作
        while(list2.size()>1 && list2.get(list2.size()-1)==0){
            list2.remove(list2.size()-1);//这里的b可能为0所以可能会产生前导0
        }
        return list2;
    }
}

//高精度除法
#include<iostream>
#include<vector>
using namespace std;

vector<int> div(vector<int>&A,int b,int &r){
    vector<int> C;
    r=0;//表示上一位相除的余数
    for(int i=A.size()-1;i>=0;i--){
        C.push_back(r*10+A[i])/b;
        r=(r*10+A[i])%b;
    }
    reverse(C.begin(),C.end());//因为这里的除法数组低位存储的是高位数据
    while(C.size()>1 && C.back()==0){
        C.pop_back();//去除前导0
    }
    return C;
}
int main(){
    string a;
    int b;
    cin>>a>>b;
    vector<int> A;
    for(int i=a.size()-1;i>=0;i--){
        A.push_back(a[i]-'0');
    }
    vector<int> C=div(A,b,r);
    for(int i=C.size()-1;i>=0;i--){
        printf("%d",C[i]);
    }
    return 0;
}
```



#### 四、高精度除法：A/b

#### （794）高精度除法

![1680965829758](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1680965829758.png)

解题思路：

大部分和高精度乘法差不多，不过也有一些除法特有的问题：首先不论是加法、减法还是乘法统统是先从低位开始运算的，但是到了除法就不同了，除法是先从高位开始运算的；还有就是除法运算的结果我们用一个集合去存储这个结果，该结果是正序的（因为是从高位开始算起的，所以首先得到的是高位的数据），但是为了和前面的加法、减法、乘法运算匹配，这里我们需要将集合中的元素翻转一下（可以利用工具类Collections类中的reverse()方法）；最后还得注意一个点需要将余数加入到集合中，所以集合中的最后一个元素是余数，那么当要输出商的时候是从集合中的倒数第二个元素开始输出的

```Java
import java.util.Scanner;
import java.util.List;
import java.util.ArrayList;
import java.util.Collections;
public class Main{
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        String A=sc.next();
        List<Integer> list1=new ArrayList<>();
        for(int i=A.length()-1;i>=0;i--){
            list1.add(A.charAt(i)-'0');
        }
        int b=sc.nextInt();
        List<Integer> ans=div(list1,b);
        for(int i=ans.size()-2;i>=0;i--){
            //这里为什么是从集合中的倒数第二个元素开始输出？
            //因为集合中的最后一个元素是余数，当要输出商的时候是从倒数第二位开始输出的
            System.out.print(ans.get(i));
        }
        System.out.println();
        System.out.println(ans.get(ans.size()-1));
    }
    //定义大整数的除法（除法和前面的运算不同，因为它是从高位开始算起的）
    public static List<Integer> div(List<Integer> list1,int b){
        List<Integer> list2=new ArrayList<>();//用来存储大整数除法的结果
        int r=0;//用来存储高位相除的余数
        for(int i=list1.size()-1;i>=0;i--){
            r=r*10+list1.get(i);
            list2.add(r/b);//可能会产生前导0
            r%=b;
        }
        //直到这里List2集合中存储的元素都是正序的，得将它翻转过来，因为前面的加法、减法、乘法运算中
        //集合中的结果都是逆序的
        Collections.reverse(list2);//将集合list2翻转
        //去除前导0的操作
        while(list2.size()>1 && list2.get(list2.size()-1)==0){
            list2.remove(list2.size()-1);
        }
        list2.add(r);//将余数加入到集合中
        return list2;
    }
}
```





## 5、前缀和与差分

### 是什么：

（1）前缀和指的是数组某下标之前的所有元素的和（包括该元素本身），类似于数组的前n项和，比如说现在有一个数组a[n]，则对应的前缀和数组为s[n]，其中s[i]=a[0]+a[1]+a[2]+...+a[i]

（2）类似于求导与积分，差分与前缀和也是一组逆运算，比如说现在有一个数组a[n]，则它对应的差分数组为b[n]，其中a[i]=b[0]+b[1]+b[2]+...+b[i]，这里我们称b[n]数组是a[n]数组对应的差分数组，同时也可以称a[n]数组是b[n]数组的前缀和数组



### 为什么：

（1）为什么要专门创建前缀和这个概念呢？因为在实际应用中我们会遇到让我们求数组i下标到j下标的所有元素之和，这时候我们当然可以利用循环来求解，但是如果遇到让我们求解多组i下标到j下标的元素之和那我们就得利用多次循环了时间复杂度太高了，有没有什么更简单的办法呢？所以就提出了前缀和的概念，现在我们有前缀和数组s[n]（其中s[i]=a[0]+a[1]+a[2]+...+a[i]），所以要求数组i下标到j下标的所有元素之和只要求s[j]-s[i-1]即可，时间复杂度降低到了O(1)

（2）为什么要专门创建差分这个概念呢？因为在实际应用中我们会遇到数组的某一个子区间内的数值都加上c1，然后另一个子区间的数值都加上c2，，，等这些情况，然后问你最后数组的数值都是多少，这种情况我们当然可以利用循环一个个求解，但是同样的时间复杂度太高了，利用差分数组的概念我们可以快速解决这类问题，

比如有这么一个问题：

给定区间[l，r]，让我们把a数组中的[ l, r]区间中的每一个数都加上c,即 a[l] + c , a[l+1] + c , a[l+2] + c ,,,,,, a[r] + c;

暴力做法是for循环l到r区间，时间复杂度O(n)，如果我们需要对原数组执行m次这样的操作，时间复杂度就会变成O(n*m)。 考虑差分做法，(差分数组派上用场了)。

始终要记得，a数组是b数组的前缀和数组，比如对b数组的b[i]的修改，会影响到a数组中从a[i]及往后的每一个数。

首先让差分b数组中的 b[l] + c ,则a数组中位置l包括后面所有位置的元素都会受到影响 即：变成 a[l] + c , a[l+1] + c ,,,+a[r]+c, a[r+1]+c,,,,, a[n] + c;

但是我们只需要在[l，r]这个区间的数值加上c啊，后面[r，n]区间的数值不用给它加上c啊，没关系我们接着在差分数组b[n]中给b[r+1]减去c 即：b[r+1]-c，这样a[n]数组中r+1位置包括后面位置的数都会受到影响数值会减去c，这样两者结合起来自然就只有[l，r]区间的数值被加上了c，这样原先需要在原数组中利用循环来完成的操作在这里我们只需要在差分数组中修改一个数值就可以了时间复杂度降低到了O(1)，最后还有一个问题，那就是这里我们最后得到的依然是差分数组那么该如何得到最后的原数组呢？你忘了啊，原数组就是差分数组的前缀和数组啊，所以我们只要对着差分数组求前缀和自然就能得到原数组中的每一位



### 怎么办：

### 如何创建前缀和数组呢？

经过上面的介绍我们知道由前缀和数组来求数组子区间的求和十分方便，那么该如何求解前缀和数组呢？对于一维数组直接进行前n项求和就好；对于二维数组前缀和首先我们得了解它代表什么，这里现在有一个普通的二维数组a[i] [j]，则从数组左上角（1，1）位置到数组右下角（i，j）位置的所有元素求和就作为前缀和数组s[i] [j]的数值，我们想要直接利用循环来求解这个前缀和数组显然不可能（时间复杂度太高了），所以得找到它的递推关系，如下图所示：这里我们要求红色部分所有元素求和的结果s[i] [j]，它等于黄色部分所有元素求和的结果s[i-1] [j]，加上蓝色部分所有元素求和的结果s[i] [j-1]，发现紫色部分被算了两次所以我们还得再减去一次s[i-1] [j-1]，最后再加上原数组(i，j)位置处的数值a[i] [j]就得到红色部分所有元素求和的结果，

即：s[i] [j]=s[i-1] [j]+s[i] [j-1]-s[i-1] [j-1]+a[i] [j]

下图代表普通的二维数组a[i] [j]（从左上角(1,1)到右下角(i,j)的所有元素求和即为前缀和数组s[i] [j]中(i，j)位置处的元素值）

![1681024177936](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681024177936.png)



### 如何创建差分数组呢？

（1）创建一维差分数组

我们知道给定一个普通的数组a[n]，则它对应的差分数组b[n]的前缀和就是数组a[n]中的项即a[i]=b[0]+b[1]+...+b[i]，这样的差分数组是肯定存在的，但是该如何构建呢？这里我们首先假定原数组的元素都是0则对应的差分数组是多少？也全是0，这点很容易看出来，**现在原数组第i个位置添加了元素c，则因为原数组是差分数组的前缀和数组有此概念我们得出差分数组的第i个位置得加上c并且差分数组的第i+1个位置得减去c**（差分数组第i个位置加c会影响到原数组第i个位置包括后面位置的元素都加上c，然后又给差分数组第i+1个位置减去c会影响到原数组第i+1个位置包括后面位置的元素都减去c，这样二者一综合就得到原数组第i个位置加上c）

![1681029487160](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681029487160.png)

![1681029544721](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681029544721.png)

![1681029587476](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681029587476.png)





（2）创建二维差分数组

这里要想创建二维差分数组首先我们得知道二维差分数组代表的意义，因为差分数组的前缀和就代表原数组，所以对于原数组中的a[i] [j]这个元素它对应的就是差分数组中从左上角(1，1)位置一直到(i，j)位置处的范围内的所有元素的和

![1681030389828](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681030389828.png)

我们知道对于差分数组中修改一个位置的元素数值会影响到原数组中该位置后面的所有元素包括该位置处的元素，对于二维差分数组也是同理，当我们修改b[i] [j]的数值后会影响到所有需要用到b[i] [j]这个元素的位置，即图中的蓝色区域（因为这个区域的元素进行前缀和计算的时候会不可避免的用到b[i] [j]这个元素），所以我们得出当修改二维差分数组某一个位置的元素的时候它会影响到所有以它作为左上角的矩形区域内的元素的前缀和计算

![1681030828764](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681030828764.png)

那么该如何求二维差分数组呢？

假设现在原数组是个全为0的二维数组，那么它对应的差分数组也全是0这点很好理解；然后现在给原数组的

（i，j）位置添上c即a[i] [j]+c反映在差分数组中是什么样的？我们给差分数组b[i] [j]+c则对应红色部分的所有元素都会加上c，所以我们还得给所有黄色部分的元素减去c，所有蓝色部分也得减去c，发现紫色部分被减了两次，所以我们还得加回来一次，即：

b[i] [j]=b[i] [j]+c;

b[i+1] [j]=b[i+1] [j]-c;

b[i] [j+1]=b[i] [j+1]-c;

b[i+1] [j+1]=b[i+1] [j+1]+c;

经过上述操作可以实现对原数组的（i，j）位置的元素加c的操作。

就这样依次对于每个元素进行上述操作就可以得到差分数组。

![1681032972897](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681032972897.png)





### （795）前缀和

![1680966480733](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1680966480733.png)

解题思路：

就是简单的创建一个前缀和数组，后续就可以将数组某一个子区间的求和的时间复杂度降低到O(1)

```Java
import java.util.Scanner;
public class Main{
    private static final int N=100010;//创建这么大的数组
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        //第一行输入两个整数n,m
        int n=sc.nextInt();
        int m=sc.nextInt();
        //第二行包含 n个整数，表示整数数列
        int[] nums=new int[N];
        for(int i=1;i<=n;i++){
            nums[i]=sc.nextInt();//中间用空格隔开
        }
        //创建一个数组用来表示前缀和
        int[] sum=new int[N];
        for(int i=1;i<=n;i++){
            sum[i]=sum[i-1]+nums[i];
        }
        //输入接下来 m行，每行包含两个整数 l和 r，表示一个询问的区间范围(1<=l<=r<=n，
        //这里的l和r代表是数组中的第几个数而不是数组下标)
        //输出共 m行，每行输出一个询问的结果
        for(int i=0;i<m;i++){
            int l=sc.nextInt();
            int r=sc.nextInt();
            System.out.println(sum[r]-sum[l-1]);
            //这里可能会产生数组下标越界异常（为什么这里会出现这些异常呢？
            //究其原因还是这里的i,j表示的是数组中的数字是第几位而不是数组下标
        }
        return;
    }
}
```





### （796）子矩阵的和（二维数组前缀和）

![1680966559436](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1680966559436.png)

解题思路：

这里就是要利用二维前缀和数组来求子矩阵的和了，不过有一点需要注意的是，求出二维前缀和数组以后，当给定子矩阵的左上角和右下角以后，我们该如何利用二维前缀和数组来求出子矩阵的值呢？如下图所示：我们想要求红色部分求和的结果，等于黄色部分求和的结果s[x2] [y2]，减去蓝色部分求和的结果s[x2] [y1-1]，再减去紫色部分求和的结果s[x1-1] [y2]，发现绿色部分被减了两次所以还得加回来一次s[x1-1] [y1-1]，

即：s[x2] [y2]-s[x2] [y1-1]-s[x1-1] [y2]+s[x1-1] [y1-1]

下图表示的是普通的二维数组a[i] [j]（从左上角(1,1)到右下角(i,j)的所有元素求和即为前缀和数组s[i] [j]中(i，j)位置处的元素值）

![1681025983245](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681025983245.png)

```Java
import java.util.Scanner;
public class Main{
    private static final int N=1010;
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        int n,m,q;
        n=sc.nextInt();
        m=sc.nextInt();
        q=sc.nextInt();
        //输入二维数组
        int[][] arr=new int[N][N];
        for(int i=1;i<=n;i++){
            for(int j=1;j<=m;j++){
                arr[i][j]=sc.nextInt();
            }
        }
        
        //创建二维前缀和数组
        int[][] sum=new int[N][N];
        for(int i=1;i<=n;i++){
            //这里为什么要从数组下标1开始赋值?因为这样下面的递推式就不会越界了
            for(int j=1;j<=m;j++){
                sum[i][j]=sum[i-1][j]+sum[i][j-1]-sum[i-1][j-1]+arr[i][j];
            }
        }
        while(q>0){
            int x1,y1,x2,y2;
            x1=sc.nextInt();
            y1=sc.nextInt();
            x2=sc.nextInt();
            y2=sc.nextInt();
            System.out.println(sum[x2][y2]-sum[x2][y1-1]-sum[x1-1][y2]+sum[x1-1][y1-1]);
            q--;
        }
        return;
    }
}
```



### （797）差分

![1680966624520](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1680966624520.png)

解题思路：

先通过将原数组全部置0，然后依次插入元素的方式来构建一维差分数组

```Java
import java.util.Scanner;
public class Main{
    private static final int N=100010;
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        int n,m;
        n=sc.nextInt();
        m=sc.nextInt();
        int[] nums=new int[N];
        for(int i=1;i<=n;i++){
            nums[i]=sc.nextInt();
        }
        
        //构造差分数组（先将原数组看成全为0的数组然后依次给原数组插入元素以此来构建差分数组）
        int[] b=new int[N];
        for(int i=1;i<=n;i++){
            insert(b,i,i,nums[i]);
        }
        
        //对原数组的子区间内的所有元素进行修改反映在差分数组中是什么样的
        while(m>0){
            int l=sc.nextInt();
            int r=sc.nextInt();
            int c=sc.nextInt();
            b[l]+=c;
            b[r+1]-=c;
            m--;
        }
        
        //对差分数组进行前缀和的求解得到的就是原数组
        for(int i=1;i<=n;i++){
            b[i]+=b[i-1];
            System.out.print(b[i]+" ");
        }
        /*
        这是求原数组的另一种方式：
        for(int i=1;i<=n;i++){
            nums[i]=nums[i-1]+b[i];
            System.out.print(nums[i]+" ");
        }
        */
        return;
    }
    
    //给原数组nums[n]的第l个位置到第r个位置的元素都加上数值c反映在差分数组b[n]中是什么样的
    public static void insert(int[] b,int l,int r,int c){
        b[l]+=c;
        b[r+1]-=c;
    }
}
```





### （798）差分矩阵（二维差分矩阵）

![1680966689824](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1680966689824.png)

解题思路：

由上述我们已经可以得到二维差分数组了，现在是给定子区间的范围，然后让这个子区间内所有元素的数值加上c该如何操作呢？关键就是找到如何构建这个子区间的问题。

现在我们选定子区间的范围，（x1，y1）作为子区间的左上角、（x2，y2）作为子区间的右下角，将这个范围内的所有元素都加上c那么该如何操作？由前面我们已经知道修改差分数组（i，j）位置处的元素值会影响到以它作为左上角的所有元素的求和情况（即会影响到所有求前缀和的情况），所以这里我们在差分数组的（i，j）位置加上c会影响到所有黄色区域的数组数值都加上c，然后我们再得给蓝色区域的数值都减去c，再将紫色区域的数值都减去c，发现绿色区域被减了两次，所以我们还得再加一次回来，即：

b[x1] [y1]=b[x1] [y1]+c; //所有黄色区域内的元素加上c

b[x2+1] [y1]=b[x2+1] [y1]-c; //所有蓝色区域内的元素减去c

b[x1] [y2+1]=b[x1] [y2+1]-c; //所有紫色区域内的元素减去c

b[x2+1] [y2+1]=b[x2+1] [y2+1]+c; //所有绿色区域内的元素再加上c

这四个操作总和下来就是将所有红色区域内的元素加上了c。

另外还需要注意的是这里得到的是二维差分数组还不是原数组，如果想要得到原数组的话还需要进行差分数组的前缀和计算

![1681031473321](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681031473321.png)



```Java
import java.util.Scanner;
public class Main{
    private static final int N=1010;
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        int n,m,q;
        n=sc.nextInt();
        m=sc.nextInt();
        q=sc.nextInt();
        
        //输入原矩阵
        int[][] nums=new int[N][N];
        for(int i=1;i<=n;i++){
            for(int j=1;j<=m;j++){
                nums[i][j]=sc.nextInt();
            }
        }
    
        //先通过将原矩阵全部置0然后依次插入元素的方式来创建它对应的差分矩阵
        int[][] b=new int[N][N];
        for(int i=1;i<=n;i++){
            for(int j=1;j<=m;j++){
                insert(b,i,j,i,j,nums[i][j]);
            }
        }
        
        //对原数组的子区间内的数值进行加c操作反映在差分矩阵上的修改
        while(q>0){
            int x1,y1,x2,y2,c;
            x1=sc.nextInt();
            y1=sc.nextInt();
            x2=sc.nextInt();
            y2=sc.nextInt();
            c=sc.nextInt();
            insert(b,x1,y1,x2,y2,c);
            q--;
        }
        
        //输出经过多次修改后的新的原矩阵（即差分矩阵进行前缀和计算）
        for(int i=1;i<=n;i++){
            for(int j=1;j<=m;j++){
                //这里的原数组nums[i][j]就相当于差分数组b[i][j]的前缀和数组
                nums[i][j]= nums[i][j-1]+nums[i-1][j]-nums[i-1][j-1]+b[i][j]; 
                System.out.print(nums[i][j]+" ");
            }
            System.out.println();
        }
    }
    
    //对原数组的给定子区间内的所有数值进行加c的操作反映在差分数组中是什么样
    public static void insert(int[][] arr,int x1,int y1,int x2,int y2,int c){
        arr[x1][y1]+=c;
        arr[x1][y2+1]-=c;
        arr[x2+1][y1]-=c;
        arr[x2+1][y2+1]+=c;
    }
}
```





## 6、双指针算法

### 是什么：

双指针算法是一种常见的算法，核心理念是使用两个指针，分别指向不同的元素，通过对比指向的元素来协同完成任务。 双指针算法最核心的用途就是优化时间复杂度。

双指针技巧主要分为两类：左右指针和快慢指针

其中左右指针又可根据移动方向分为左右指针同向和左右指针反向

其中**快慢指针可以用来解决链表环路问题，和原地修改数组问题**

**左右同向指针且不会相交被称为滑动窗口，用于解决字符串匹配问题**

**左右反向指针可以用来搜索有序数组（数组需要提前排好序）**





### 为什么：

双指针算法常用来降低算法的时间复杂度，因为使用两个指针可以避免多层循环。它可以大大降低时间复杂度，可用在数组，单链表等数据结构中。这样，我们就可以更快地解决问题。 



### 怎么办：

### 一、快慢指针（用来解决链表环路问题，和原地修改数组问题）



### 二、左右同向指针（且不会相交被称为滑动窗口，用于解决字符串匹配问题）



### 总结：双指针算法的解题思路，一般都是先想暴力算法该怎么做，然后再去看这其中有那些隐藏的规律可以被利用然后进行优化



### （799）最长连续不重复子序列

![1681200398028](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681200398028.png)

解题思路：

本题采用滑动窗口的思路，因为给定数组nums[]中每个元素nums[i]的范围都在0~10^5范围内，所以我们可以另外用一个10^5+5大小的数组s[]来记录这些元素出现了多少次，其中数组s[]下标就是数组nums[]中的元素值，数组s[]的元素值就代表了该下标所代表的元素在数组nums[]中出现了多少次（**这个功能完全可以用HashMap结构来代替，用HashMap的话最后当排除集合中的元素的时候可以用remove(key)方法删除集合中指定key值对应的键值对**）；我们首先将两个指针都置0，保持慢指针不动，然后让快指针先走，同时记录它当前遍历的元素出现的次数，如果当遍历到一个元素发现它重复了，那么肯定造成重复的就是当前这个元素，这时候我们要做的就是不断移动慢指针直到将                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            那个造成重复的元素从当前子序列中排除出去，接着继续保持慢指针不动移动快指针；重复上述步骤直到快指针到达数组末尾。如下图所示：

首先一图中快指针遍历过的元素没有重复的，接着到二图中当遍历到后一个元素的时候发生了重复，造成重复的就是当前快指针指向的元素，这就意味着当前的子序列中前面肯定有一个元素值和当前元素相同，我们要做的就是像三图中那样不断移动慢指针将子序列中那个重复的元素排除出去，接着继续移动快指针向后延伸重复上述过程（别忘了在上述过程中要不断更新不重复的子序列的长度，找到最大值）

![1681546286372](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681546286372.png)

```Java
//前面已经试过了暴力做法（就是用两层for循环），现在来想想双指针优化算法
import java.util.Scanner;
public class Main{
    public static final int N=100010;
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        int n;
        n=sc.nextInt();
        
        //手动输入n个数的数组
        int[] nums=new int[N];
        for(int i=1;i<=n;i++){
            nums[i]=sc.nextInt();
        }
        
        int[] s=new int[N];
        /*
        用来记录当前遍历的子数组中每个元素出现的次数，那么是如何记录的呢？这里我们以nums[]数组中的元素		    值作为s[]数组中的下标值，如果在遍历的子数组中nums[i]这个元素出现了两次，则它在s[]数组中对应的值		  s[nums[i]]就等于2即s[nums[i]]=2（其实这里可以用HashMap结构来实现此功能而且更加方便，那时候就		   是数组nums[]中的数值对应HashMap中的key,该元素出现的次数对应的就是value。不过和这里的s[]数组的		  功能都是一样的反正都是记录子数组中每个元素出现的次数）
        */
        
        //用双指针的算法来优化求解过程
        int maxLen=0;//记录不重复的子数组的最大长度
        for(int i=1,j=1;j<=n;j++){//注意这里的i指针是慢的那个指针，j指针是相对较快的那个指针
            s[nums[j]]++;
            while(i<j && s[nums[j]]>1){
                s[nums[i]]--;
                i++;
            }
            maxLen=Math.max(maxLen,(j-i+1));
        }
        System.out.println(maxLen);
        return;
    }
}
```





### （2816）判断子序列

![1681200567904](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681200567904.png)

解题思路：

用到左右同向指针，一开始慢指针指向a数组的首位，快指针指向b数组的首位，接着移动快指针让它找慢指针指向的a数组中的元素在b数组中能不能找到，若是找不到则继续后移快指针继续找，若是找到了则慢指针后移一位，接着继续移动快指针，重复上述过程

![1681560394720](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681560394720.png)

```Java
import java.util.Scanner;
public class Main{
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        int n,m;
        n=sc.nextInt();
        m=sc.nextInt();
        int[] a=new int[n];
        for(int i=0;i<n;i++){
            a[i]=sc.nextInt();
        }
        int[] b=new int[m];
        for(int i=0;i<m;i++){
            b[i]=sc.nextInt();
        }
        
        //双指针做法：答案的方法更精炼，但是思想都是一样的，左右同向指针
        // int i=0,j=0;
        // while(i<n && j<m){
        //     if(a[i]==b[j]){
        //         i++;
        //     }
        //     j++;
        // }
        // if(i==n){
        //     System.out.println("Yes");
        // }else{
        //     System.out.println("No");
        // }
        int i=0,j=0;
        while(j<m){
            if(b[j]==a[i]){
                j++;
                i++;
                if(i==a.length){
                    break;
                }
            }else{
                j++;
            }
        }
        if(i==a.length){
            System.out.println("Yes");
        }else{
            System.out.println("No");
        }
    }
}
```





### 三、左右反向指针（用来搜索有序数组（数组需要提前排好序））

### （800）数组元素的目标和

![1681200495958](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681200495958.png)

解题思路：

这题暗示的已经很明显了，两个都是升序数组，所以我们想到左右反向指针，首先左指针指向A数组中的最小值，右指针指向B数组中的最大值，然后计算它们二者的和，如果和比目标值大则我们往前移动右指针缩小和值，如果和比目标值小则我们往后移动左指针增大和值，这样就将原本需要两次循环的操作改成了只要遍历一次两个数组就行了

```Java
import java.util.Scanner;
public class Main{
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        int n,m,x;
        n=sc.nextInt();
        m=sc.nextInt();
        x=sc.nextInt();
        int[] A=new int[n];
        for(int i=0;i<n;i++){
            A[i]=sc.nextInt();
        }
        int[] B=new int[m];
        for(int i=0;i<m;i++){
            B[i]=sc.nextInt();
        }
        /*
        暴力做法会导致超时，所以这里我们利用双指针来进行优化
        //暴力做法
        for(int i=0;i<n;i++){
            for(int j=0;j<m;j++){
                if(A[i]+B[j]==x){
                    System.out.print(i+" "+j);
                    return;
                }
            }
        }
        */
        
        for(int i=0,j=B.length-1; i<n; i++){
            while(A[i]+B[j]>x){
                j--;
            }
            if(A[i]+B[j]==x){
                System.out.print(i+" "+j);
                return;
            }
        }
    }
}
```





## 7、位运算

### 是什么：

所有的数字在在计算机内存中都是以二进制的形式储存的，位运算就是直接对整数在内存中的二进制位进行运算；

主要有以下几种：这里的a、b都是int型数据

（1）按位与（&）：a&b

（2）按位或（|）：a|b

（3）按位异或（^）：a^b



### 怎么办：

这里介绍一下lowbit(int x)方法，它的作用是找到并返回整数x的二进制表示中的最后一个1连同它后面的0一起返回，举个例子10的二进制表示为1010，调用该方法后返回的即是10（返回的是右边的那个1连同它后面的0，注意这里返回的是二进制的一零，而不是十进制下的十）。那么该方法是如何做到的呢？该方法的方法体就是：x & -x，二者进行相与操作最后得到的就是蓝色的部分，即最后一个1连同它后面的0都被返回了

![1681562466244](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681562466244.png)

### （801）二进制中1的个数

![1681200640268](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681200640268.png)

解题思路：

由上述介绍的lowbit(int x)方法我们可以得到一个整数x它的二进制表示中的最后的一个1连同他后面的0，所以每次我们调用一次lowbit(x)方法，然后让x减去这个值就相当于将x中最后的一个1给减去了，然后看看减多少次它会变成0就意味着它的二进制表示中有多少个1

举例：

15的二进制表示为1111，lowbit(15)=1（二进制），15-lowbit(15)=1110=14；

14的二进制表示为1110，lowbit(14)=10（二进制），14-lowbit(14)=1100=12；

12的二进制表示为1100，lowbit(12)=100（二进制），12-lowbit(12)=1000=8;

8的二进制表示为1000，lowbit(8)=1000（二进制），8-lowbit(8)=0000=0.

```Java
import java.util.Scanner;
public class Main{
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        int n;
        n=sc.nextInt();
        int[] nums=new int[n];
        for(int i=0;i<n;i++){
            nums[i]=sc.nextInt();
        }
        for(int i=0;i<n;i++){
            int count=0;
            while(nums[i]!=0){
                nums[i]=nums[i]-lowbit(nums[i]);
                count++;
            }
            System.out.print(count+" ");
        }
    }
    
    //lowbit()方法可以返回二进制数字中的最后一个1包括后面的数位0
    public static int lowbit(int x){
        return x & -x;
    }
}
```





## 8、离散化

### 是什么：

离散化，把无限空间中有限的个体映射到有限的空间中去，以此提高算法的时空效率。

通俗的说，离散化是在不改变数据相对大小的条件下，对数据进行相应的缩小。例如：

原数据：1,999,100000,15；处理后：1,3,4,2；

原数据：{100,200}，{20,50000}，{1,400}；

处理后：{3,4}，{2,6}，{1,5}；

这些处理都不改变数据之间的相对大小



### 为什么：

就拿下面的这个区间和的例子来说，我们规定的数轴的长度是（-10^9，10^9），然后本来这么长的数轴上是没有一个数的，现在在其中挑选10^5个位置然后在这些位置上插入数据（注意在挑选这10^5个位置的时候也可能会两次选到同一个位置），然后给定你区间让你求这个区间内的数据的和。我们肯定想到前缀和，但是如果直接建立前缀和数组的话，那么该数组的下标范围就是（-10^9，10^9），真的需要这么大的数组吗？其实没必要，因为10^9个位置，其中只有10^5个位置上有数别的位置都为0，也就是说平均10^4个位置上才有一个数，那么我们建立那么多的数组上面的位置都被浪费了啊，这里其实我们只要知道当给定一个子区间的时候我们只要知道这个子区间内有多少个位置上有数以及这些数都是多少就好了，没必要将原数组每个位置都了解清楚；所以综上我们只需要知道那10^5个有数的位置它们的相对位置，以及后续需要查询的10^5个子区间对应的左右下标和上述的10^5个有数的位置它们之间的相对位置就好了。举例来说：

现在有一个数组如下图所示，它只有10号下标、80号下标、100号下标有数，别的位置都是0，然后现在给了你一个子区间的下标8，90，问你这个子区间内的区间元素和是多少，如果建立前缀和数组的话非得建立和原数组一样大内存空间的前缀和数组吗？当然不是，这里我们只需要知道这三个有数的位置下标以及后面要查询的子区间的两个下标总共五个下标的相对关系就行了，可以将它们转换成下图所示的相对位置关系，然后本来让求在原数组中位置下标8到位置下标90这个区间内的元素和，就相当于求后续数组中位置下标1到位置下标4这个区间内的元素和了，后续数组的空间大小那可就小多了啊

![1681565468512](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681565468512.png)



### 怎么办：

### （802）区间和

![1681200725862](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681200725862.png)

解题思路：

利用离散化将原先广阔数组中的元素映射到小数组中去，要记住保持数组下标的相对顺序不变，并且保持元素下标以及元素值之间的一一对应关系不变

```Java
/*
这题就用之前学过的前缀和来做，但是有一个问题，那就是这里的区间范围太大了为（-10^9,10^9），但是总共只有10^5个位置上有数，别的那么多位置上都是空的平均每10^4个位置上才有一个数，所以如果我们创建前缀和数组的话也是很浪费数组空间的，其实这里我们并不需要那么多的位置啊，好好想想我们是不是只需要知道各个数值之间的相对位置就好了，没必要知道他们的绝对位置，我们只要知道哪个数在哪个数左边还是右边以及最后的要查询的子区间的两个端点和这些下标的相对位置所以综上我们只要创建3*10^5个位置的数组就可以了，以防不够创建3*10^5+10个位置的数组（总共有10万个位置上面有数，然后又要询问10万个子区间，所以还得加上这10万个子区间的两个边界，所以总共是30万个位置）
*/
import java.util.Scanner;
import java.util.List;
import java.util.ArrayList;
import java.util.Collections;
public class Main{
    public static final int N=300010;
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        int n,m;
        n=sc.nextInt();
        m=sc.nextInt();
        
        List<Pair> add=new ArrayList<>();//用来存放x-c数据对的集合
        List<Pair> query=new ArrayList<>();//用来存放子区间l-r数据对的集合
        List<Integer> all=new ArrayList<>();//用来存放所有用到的下标的集合（包括x,l,r）
        
        for(int i=0;i<n;i++){
            int x,c;
            x=sc.nextInt();
            c=sc.nextInt();
            add.add(new Pair(x,c));
            all.add(x);//all集合将所有用到的x下标加入到集合中
        }
        for(int i=0;i<m;i++){
            int l,r;
            l=sc.nextInt();
            r=sc.nextInt();
            query.add(new Pair(l,r));
            all.add(l);//all集合将所有用到的l下标和r下标加入到集合中
            all.add(r);
        }
        
        //对all集合进行排序、去重
        Collections.sort(all);
        /*这种方式确实可以去重，但是没有将集合中重复的元素进行删除
        int k=0;
        for(int i=0;i<all.size();i++){
            if(i==0 || all.get(i)!=all.get(i-1)){
            //当数据是第一个或者是和前面的数据不同时意味着它是第一次出现
                all.set(k++,all.get(i));
                //这里仅仅是将不重复的元素放到了数组最前面，但是后面的元素并没有删除
            }
        }
        */
        int end=unique(all);
        all=all.subList(0,end);
        //将去重后的all集合中位置[0,end)上的子集合重新取出来赋给集合all（end位置的元素取不到）
        
        int[] a=new int[N];//用来存放元素相对位置上的数值的数组，这里的下标就已经改成了相对位置
        for(int i=0;i<add.size();i++){
            int x=add.get(i).first;
            int index=find(x, all);//找到插入的x-c中x对应的相对位置
            a[index]+=add.get(i).second;//这里对于同一个x可能会进行多次相加操作
        }
        
        int[] s=new int[N];//创建前缀和数组
        for(int i=1;i<=all.size();i++){
            s[i]=s[i-1]+a[i];
        }
        
        for(int i=0;i<query.size();i++){
            int l=query.get(i).first;
            int r=query.get(i).second;
            int index1=find(l,all);
            int index2=find(r,all);
            System.out.println(s[index2]-s[index1-1]);
        }
    }
    
    //定义集合去重的方法，该方法将排序后的集合中所有不重复的元素都放到集合前面的位置，然后返回集合中
    //最后一个不重复的元素的后一个位置
    public static int unique(List<Integer> list){
        int k=0;
        for(int i=0;i<list.size();i++){
            if(i==0 || list.get(i)!=list.get(i-1)){
                //当排序后的集合中元素是第一个位置或者与前面位置的元素不同说明它是第一次出现，
                //就将它放入集合中前面的位置
                list.set(k++, list.get(i));
            }
        }
        return k;//返回的是集合中最后一个不重复的元素的后一个位置
    }
    
    
    //找到值key在集合all中第一次出现时对应的位置，不是下标，而是相对位置第几个（从1开始而不是从0开始）
    public static int find(int key,List<Integer> all){
        int left=0,right=all.size()-1;
        while(left<=right){
            int mid=(left+right)/2;
            if(all.get(mid)==key){
                return mid+1;
                //这里要返回的并不是下标而是相对位置第几个（从1开始的，所以要给下标加上1）
            }else if(all.get(mid)>key){
                right=mid-1;
            }else{
                left=mid+1;
            }
        }
        return -1;
    }
    
    
    /*
    这里之前出了问题，但是不是二分查找的问题而是对于同一个下标x可能会进行多次加c操作而我之前仅仅
    就是利用赋值来进行的，这样后面的数值会将前面的数值覆盖掉
    public static int find(int x ,List<Integer> list){
        int l  = 0;
        int r = list.size() - 1;
        while(l < r){
            int mid = ( l + r )/ 2;
            if(list.get(mid) >= x) r = mid;
            else l = mid + 1;
        }
        return l + 1;
    }
    */
    
}

/*
因为后续要输入一对一对的数据，比如x-c和l-r，所以我们得定义这个结构（这里还不能用HashMap，因为假如以l作为key的话后续可能会遇到查询两个子区间的时候，两个子区间的左边界都是l，那么这时候就没法再用HashMap了），所以必须得定义这样的结构
*/
class Pair{
    public int first;
    public int second;
    
    public Pair(int first, int second){
        this.first=first;
        this.second=second;
    }
}
```





## 9、区间合并

### 是什么：

区间合并是指将一组区间如果它们产生了交集就将它们进行合并。通常情况下，区间合并问题的输入是一个由多个区间组成的集合，问题的输出是将这些区间进行合并后得到的新区间集合。

例如，假设输入为 [(1, 3), (2, 6), (8, 10), (15, 18)]，这里每个元素是一个区间，用 (a, b) 表示左端点为 a，右端点为 b。按照区间合并的规则，可以将区间 (1, 3) 和 (2, 6) 合并为 (1, 6)，(8, 10) 无法与前面的区间进行合并，因此我们将它单独作为一个区间，最后将 (15, 18) 也单独作为一个区间。因此，最终的输出是 [(1, 6), (8, 10), (15, 18)]，它们都是不重叠的区间。



### 怎么办：

### （803）区间合并

![1681200796894](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681200796894.png)

解题思路：

这里的思路就是将一个个子区间放入集合中，然后再根据子区间的左下标将集合排序，依次查看上一个子区间是否会和当前的子区间产生交集，如果不产生交集就看当前遍历的子区间是否会和它的下一个元素子区间产生交集；如果产生交集那么就看产生交集以后的区间右边界到底是多少

这里需要同时输入多对区间的左右下标，而且这里的区间的左右下标是成对出现不能分散的，所以我们得定义一个类Pair来专门存储这里的一对下标，然后将该Pair类的对象存入集合中，最后再根据集合中的元素即Pair的左下标排序，所以我们在定义类Pair的时候就得实现comparable接口在里面重写compareTo()方法实现按照区间的左下标排序的功能

```Java
import java.util.Scanner;
import java.util.List;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
public class Main{
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        int n;
        n=sc.nextInt();
        /*
        这里必须得一次性将n行l-r一起输入进来，然后进行相交的判断，不能一次输入一行l-r这样没法判断
        又因为l-r是成对出现的，所以必须得定义一个数据结构同时包含这两个数据（这里不能使用HashMap结构，
        因为有可能两个l-r对中的l是相同的，这时候如果以l作为key值就会矛盾）
        */
        List<Pair> list=new ArrayList<>();
        for(int i=0;i<n;i++){
            int l,r;
            l=sc.nextInt();
            r=sc.nextInt();
            list.add(new Pair(l,r));
        }
        //将集合按照所有元素的l进行排序（这里在定义类Pair的时候就得让它
        //实现comparable接口并重写compareTo()方法定义比较规则）
        Collections.sort(list);
        
        //已经在集合中按照所有元素的l排过序了，接下来就是判断集合中的各个元素是否相交
        int count=1;
        int r=list.get(0).r;
        for(int i=0;i<list.size()-1;i++){
            int l=list.get(i+1).l;
            if(r<l){
                count++;
            }
            r=Math.max(r, list.get(i+1).r);
        }
        System.out.println(count);
    }
}

//定义类Pair的比较规则是按照存储子区间的左边界进行比较
class Pair implements Comparable<Pair>{
    public int l;
    public int r;
    
    public Pair(int l,int r){
        this.l=l;
        this.r=r;
    }
    
    public int compareTo(Pair p){
        return Integer.compare(this.l,  p.l);
    }
}
```

```Java
//注意上述的方法并没有将最后的区间完成合并，只是一直在计算有多少个区间，
//所以这里我们来完成一下区间的合并工作
import java.util.Scanner;
import java.util.List;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
public class Main{
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        int n;
        n=sc.nextInt();
        /*
        这里必须得一次性将n行l-r一起输入进来，然后进行相交的判断，不能一次输入一行l-r这样没法判断
        又因为l-r是成对出现的，所以必须得定义一个数据结构同时包含这两个数据（这里不能使用HashMap结构，
        因为有可能两个l-r对中的l是相同的，这时候如果以l作为key值就会矛盾）
        */
        List<Pair> list=new ArrayList<>();
        for(int i=0;i<n;i++){
            int l,r;
            l=sc.nextInt();
            r=sc.nextInt();
            list.add(new Pair(l,r));
        }
        //将集合按照所有元素的l进行排序
        Collections.sort(list);
        
        //注意之前的方法并没有将有交集的区间合并起来，只是一直在算到底有多少个区间，这里我们将区间合并起来
        List<Pair> ans=new ArrayList<>();
        int l=(int)-2e9,r=(int)-2e9;
        //先设定左右边界的值最小，这里因为所给的边界值的范围为（-10^9，10^9）所以我们一开始将边界值
        //设定成-2e9一定比实际的边界值小
        for(int i=0;i<list.size();i++){
            if(r<list.get(i).l){
                if(r!=-2e9){
                    ans.add(new Pair(l,r));
                    //这里当我们更新到下一个子区间的时候才将上一个子区间加入到最后的集合中，
                    //所以当面对最后一个子区间的时候我们是没法将它加入到集合中的，所以最后等出了
                    //循环还得加上将最后的子区间加入集合的操作
                }
                l=list.get(i).l;
                r=list.get(i).r;
            }else{
                r=Math.max(r,list.get(i).r);
            }
        }
        if(r!=-2e9){//出了上述循环以后还得将最后的子区间加入集合
            ans.add(new Pair(l,r));
        }
        System.out.println(ans.size());
    }
}

//定义类Pair的比较规则是按照存储子区间的左边界进行比较
class Pair implements Comparable<Pair>{
    public int l;
    public int r;
    
    public Pair(int l,int r){
        this.l=l;
        this.r=r;
    }
    
    public int compareTo(Pair p){
        return Integer.compare(this.l, p.l);
    }
}
```









# 二、数据结构

## 1、单链表

### 是什么：

单链表（Singly Linked List）是一种常见的数据结构，由一组节点（Node）组成，每个节点包含一个数据元素和一个指向下一个节点的指针（或引用）。单链表中的每个节点只保存下一个节点的地址，因此只能向一个方向遍历。

单链表常用于实现队列、栈以及其他一些数据结构，也是很多算法中的基础数据结构之一。与数组不同，单链表的插入和删除操作效率较高，但是访问任意位置的元素需要从头节点开始遍历，效率较低。



### 怎么办：

#### 如何实现单链表？

在Java中我们当然可以定义一个节点类Node，然后里面定义属性包括元素值和指向下一个节点的指针，然后每次使用的时候都重新创建一个节点类的对象，但是这样的效率实在是太低了，所以一般情况下我们改用数组来实现单链表因为效率高

```Java
public class Node{
    public int val;//定义节点的元素值
    public Node next;//定义节点的next指针
    
    public Node(val){
        this.val=val;
    }
}
```

#### 如何用数组实现单链表？（用数组实现单链表又叫静态链表）

这里明明链表节点里面存在两个结构，一个是节点的元素值，另一个则是节点的next指针，所以单纯只用一个数组是没法表示的，所以得用两个数组才行：int[] e用于存储节点的元素值，int[] next用于存储节点的next指针；具体使用见下图：

现在有一个假设有一个链表，链表的头节点位于数组的下标1处，里面存放的元素值为2，

（1）先进行第一次操作：现在想在链表元素2后面插入一个元素4，我们可以选择在数组下标3处插入元素4（**为什么不是在紧跟着下标1后面的下标2插入元素4呢，这里不是要表示链表吗？实际上这里我们可以在数组的任意位置处插入元素4，这里的e数组所起到的作用仅仅是作为存储数值的容器，那么如何表示各个节点之间的前后关系呢？用另外一个next数组表示啊**），当在下标3处插入元素4以后我们就要修改next数组中下标1处的数值将next[1]改为3了（代表数组下标1对应的链表节点后面的一个链表节点对应的数组下标是3）；

（2）现在进行第二次操作：在链表元素2和4之间插入一个元素7，我们选择在数组下标5处插入元素7（这里可以在任意位置处插入元素7，理由前面介绍过了），然后就要修改next数组里面的元素值了，修改next[1]=5，next[5]=3，next[3]=-1（代表的意思是数组下标1对应的链表节点后面一个链表节点对应的数组下标是5，而数组下标5对应的链表节点后面一个链表节点对应的数组下标是3，-1代表数组下标为3对应的链表节点暂时没有下一个元素）

#### notes：单链表中需要一个头指针head指向链表的头节点，还需要一个idx指针指向链表的当前节点

上面我们说过了我们可以在任意位置插入新的元素，实际上也是对的，但是任意毕竟不利于我们操作，所以一般情况下我们都会设置一个idx代表指向链表当前节点的指针（而这个指针在数组中也是一个位置一个位置移动的）

![1681979707065](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681979707065.png)



### （826）单链表

![1681907441543](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681907441543.png)

解题思路：

就是基本的用数组实现单链表的结构

```Java
//用数组来实现一个单链表
import java.util.*;
public class Main{
    static int N=100010;
    static int[] e=new int[N];//用来存储链表节点的数值的数组
    static int[] next=new int[N];//用来存储链表节点的next指针的数组
    static int idx,head;
    /*
    idx表示指向链表当前节点的指针，head表示指向链表头节点的指针（注意这里head不表示头节点），
    还有这里虽然说的都是指针其实指的都是数组的下标
    */
    //定义初始化链表的方法
    public static void init(){
        head=-1;//初始化链表时还没有创建头节点，所以head指针指向-1
        idx=0;//创建链表时当前的节点就是数组的头号节点即0号节点
    }
    
    
    //向链表头插入一个数x
    public static void add_head(int x){
        e[idx]=x;
        next[idx]=head;
        head=idx;
        idx++;
    }
    
    
    
    /*
    定义在下标是k的节点的后面插入数x（注意：这里和题目中要求定义的在第k个插入的数后面
    插入数x二者是有区别的，二者相差了一个1具体分析见下）
    */
    /*
    这里需要注意的一点就是什么叫第k个插入的数？是当前链表中的第k个数吗？不是的，而是第k个插入链表中的数，
    后续这个第k个插入的数可能会被从当前链表中删除，那么此时链表中第k个数就不是第k个插入的数了，而且再想在	当前链表中找到这第k个插入的数就找不到，不过题目中说了所有操作保证合法，这就保证了不会说把第k个插入的数	删除了以后再让你在第k个插入的数后面插入一个数。
    
    知道了第k个插入的数以后关键是我们该如何寻找这第k个插入的数呢？
    前面我们已经介绍过了，用数组模拟链表的话那么当想在链表中插入一个数的时候我们是可以选择数组的任意一个
    位置进行插入的，那么这时候我们该如何找到这第k个插入元素对应的数组下标的呢？答案是找不到
    注意后面我们又补充了一句，为了便于处理我们弄了一个指向链表当前节点的指针idx，它是从0开始递增的，所以
    当插入第1个数时它插入的位置就是数组下标0，当插入第2个数时它插入的位置就是数组下标1，，，
    当插入第k个数时它插入的位置就是数组下标k-1。
    所以当我们要在第k个插入的数后面插入数x时，第k个插入的数对应的下标为k-1，而我们这里定义的是在下标是
    k的节点的后面插入数x，所以可以看到二者的下标刚好差了1
    */
    public static void add(int k,int x){
        e[idx]=x;
        next[idx]=next[k];
        next[k]=idx;//这里的k表示数组下标的意思（是从0开始的）
        idx++;
    }
    
    //将下标是k的点后面的一个点删掉
    public static void remove(int k){
        next[k]=next[next[k]];//这里的k表示数组下标的意思（从0开始的）
    }
    
    
    
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        int M;
        M=sc.nextInt();
        init();
        while(M>0){
            M--;
            String s=sc.next();
            char ch=s.charAt(0);
            if(ch=='H'){
                int x=sc.nextInt();
                add_head(x);
            }else if(ch=='D'){
                int k=sc.nextInt();
                if(k==0){
                    head=next[head];
                }else{
                    remove(k-1);
                    /*
                    为什么是k-1?
                    因为这里的k表示的不是下标了，而是数组中第k个插入的数，因为是用数组模拟链表，而且
                    用于表示当前节点的指针idx是一个一个位置递增的所以新加入的节点都是按顺序往后添加的，
                    初始化链表后第一个插入的节点它的下标为0，第二个插入的节点它的下标为1...，
                    第k个插入的节点它所对应的数组下标就是k-1
                    */
                }
            }else{
                int k=sc.nextInt();
                int x=sc.nextInt();
                add(k-1,x);//为什么是k-1?和上述同样的道理
            }
        }
        for(int i=head;i!=-1;i=next[i]){
            System.out.print(e[i]+" ");
        }
    }
}
```







## 2、双链表

### 是什么：

双链表（Doubly Linked List）是一种常见的数据结构，与单链表类似，由一组节点（Node）组成，每个节点包含一个数据元素，以及指向前一个节点和后一个节点的指针（或引用）。双链表中的每个节点保存了前一个节点和后一个节点的地址，因此可以向前或向后遍历。

双链表相比于单链表，多了一个指向前一个节点的指针，可以支持双向遍历，但也带来了额外的内存开销。双链表在某些场景下比单链表更加高效，如需要频繁在链表中间进行插入或删除操作，并且需要后向遍历链表的情况。



### 怎么办：

#### 如何实现双链表?

在Java中我们当然可以定义一个双链表节点类Node，然后里面定义属性包括元素值和指向前一个节点的指针和指向下一个节点的指针，然后每次使用的时候都重新创建一个节点类的对象，但是这样的效率实在是太低了，所以一般情况下我们改用数组来实现双链表因为效率高

```Java
public class Node{
    public int val;//定义节点元素值
    public Node left;//定义节点的左指针
    public Node right;//定义节点的右指针
    
    public Node(int val){
        this.val=val;
    }
}
```

#### 如何用数组实现双链表？

和前面的单链表类似，这里我们需要用到三个数组：int[] e用来存储链表节点的元素值，int[] Left用来存储链表节点的左指针，int[] Right用来存储链表节点的右指针

#### notes：双链表中我们不仅需要链表的头指针head指向链表的头节点，而且需要链表的尾指针tail指向链表的尾节点，还需要一个指向链表当前节点的指针idx

当在链表的头部不断插入元素的过程中我们当然可以不断的变化头指针使它始终指向头节点，当在链表的尾部不断插入元素的过程中我们同样可以不断变化尾指针使它始终指向链表的尾节点，但是还可以我们设置头指针固定指向某个位置（虚拟头节点）、尾指针就固定指向某个位置（虚拟尾节点），然后当插入一个新的头节点、尾节点时，我们再令新的头节点的左指针指向虚拟头节点而且还要修改虚拟头节点的右指针指向新添加的头节点（当插入新的尾节点时操作过程也是类似），当最后输出的时候我们要注意避开虚拟节点



### （827）双链表

![1681907342561](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681907342561.png)

解题思路：

这里我们只需要定义在某一个下标右边插入节点的方法就可以了，不用再额外定义再某一个下标的左侧插入节点的方法，因为在某一个下标的左侧插入节点就可以转换为在该下标左侧的那个节点的右侧插入节点即可以将左侧插入节点转换为右侧插入节点

```Java
//用数组模拟双链表
import java.util.*;
public class Main{
    static int N=100010;
    static int[] e=new int[N];//用来存储链表节点元素值
    static int[] L=new int[N];//用来存储链表节点的左指针
    static int[] R=new int[N];//用来存储链表节点的右指针
    static int l;//链表的头指针（相当于上面说的head）
    static int r;//链表的尾指针（相当于上面说的tail）
    static int idx;//指向链表当前节点的指针
    
    //初始化链表的方法
    public static void init(){
        l=0;//指向链表的虚拟头节点
        r=1;//指向链表的虚拟尾节点
        idx=2;
        
        //上面我只是指明了链表的虚拟头节点和尾节点，但还没有指明它们之间的前后关系呢
        R[0]=1;
        L[1]=0;
    }
    
    //在下标为k的点右边插入一个数x（不需要再定义在左边插入的方法了，
    //因为在下标为k的点左边插入可以转换为在下标为k-1的点右边插入）
    public static void insert(int k,int x){
        e[idx]=x;
        R[idx]=R[k];
        L[idx]=k;
        L[R[k]]=idx;
        R[k]=idx;
        idx++;
    }
    
    //删除下标为k的点
    public static void remove(int k){
        R[L[k]]=R[k];
        L[R[k]]=L[k];
    }
    
    
    public static void main(String[] args){
        init();//别忘了初始化链表
        Scanner sc=new Scanner(System.in);
        int M;
        M=sc.nextInt();
        while(M>0){
            M--;
            String s=sc.next();
            char ch=s.charAt(0);
            if(ch=='L'){
                int x=sc.nextInt();
                insert(0,x);
            }else if(ch=='R'){
                int x=sc.nextInt();
                insert(L[1],x);
            }else if(ch=='D'){
                int k=sc.nextInt();//表示链表中第k个插入的数
                remove(k+1);
                /*
                因为数组中的下标0处代表虚拟头节点，下标1处代表虚拟尾节点都已经被占用了，所以后续插入
                节点的话是从数组中的下标2处开始的，即第1个插入的节点插在下标为2处，第2个插入的节点
                插在下标为3处，第k个插入的节点插在下标为k+1处
                */
            }else if(s.equals("IL")){
                int k,x;
                k=sc.nextInt();
                x=sc.nextInt();
                insert(L[k+1],x);
                //在第k个节点的左边插入一个节点，首先找到第k个节点，它对应的下标是k+1,然后我们找到它
                //前面的节点为L[k+1],在它的右边插入就相当于在第k个节点的左边插入节点了
            }else{
                int k,x;
                k=sc.nextInt();
                x=sc.nextInt();
                insert(k+1,x);
            }
            
        }
        
        for(int i=R[l]; i!=r; i=R[i]){
            System.out.print(e[i]+" ");
        }
        
    }
}
```







## 3、栈

### 是什么：

 栈（Stack）是一种线性数据结构，具有“后进先出”（Last-In-First-Out，LIFO）的特点，即最后入栈的元素最先出栈。栈可以理解为一摞盘子，只能从顶部添加或移除盘子，而不能从底部操作。在栈的基础上，可以实现许多常见的算法和数据处理操作，比如计算机程序中的函数调用过程、表达式求值以及回溯搜索等。栈中元素的访问限制为只能访问栈顶元素，即只能取出和插入栈顶元素。因此，栈是一种高效的数据结构，常用于处理可以被划分为许多具有相同结构和处理方式的子问题的场合。 



### 怎么办：

#### 如何实现栈？

在Java中我们可以利用内置类java.util.Stack或者是 java.util.ArrayDeque 类来创建一个栈类的对象，里面定义了入栈、出栈、取栈顶元素、判断栈是否为空等各种操作，但是同样的会面临效率低的问题，所以我们想到用数组来实现栈

#### 如何用数组实现栈？

栈和普通数组的区别就在于栈没法做到随机存取，只能在栈顶进行存取，所以我们只需要在普通数组的基础上定义一个栈顶指针指向栈顶元素就可以了，然后每次存取的时候都是只能取栈顶指针指向的元素就行了，具体如下图所示：

![1682062480103](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1682062480103.png)



### （828）模拟栈

![1681907266579](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681907266579.png)

解题思路：

就是一个普通的用数组模拟栈的过程

```Java
//用数组模拟栈
import java.util.*;
public class Main{
    static int N=100010;
    static int[] stk=new int[N];
    static int head;//用于指向栈顶的指针
    
    public static void init(){
        head=-1;//这里我们用head指向当前栈顶元素，初始化时栈为空，所以栈顶指针指向-1
    } 
    public static void push(int x){
        stk[++head]=x;
    }
    public static void pop(){
        head--;
    }
    public static void empty(){
        if(head==-1){
            System.out.println("YES");
        }else{
            System.out.println("NO");
        }
    }
    public static void query(){
        System.out.println(stk[head]);
    }
    
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        init();
        int M;
        M=sc.nextInt();
        while(M>0){
            M--;
            String s=sc.next();
            if(s.equals("push")){
                int x=sc.nextInt();
                push(x);
            }else if(s.equals("pop")){
                pop();
            }else if(s.equals("empty")){
                empty();
            }else{
                query();
            }
        }
    }
}
```





### （3302）表达式求值

![1683429755839](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1683429755839.png)

解题思路：

##### 是什么：

题目中给定的是一个字符串，字符串的内容是一个数学表达式（其中涉及到除了各个数字字符以外还涉及到加减乘除、括号等运算符），我们要做的就是输出该数学表达式的结果，**注意这里的数字字符读取的时候是一个个分散开来的而不是作为一个整体的（比如数字12作为一个字符串输入，那么它的读取是字符1和字符2而不是将整个12作为一个整体读取的）**，我们最后运算的时候要注意这一点，**还有如何判断一个字符 'ch' 是否是数字字符呢？也有现成的方法Character.isDigit(char ch)**。



##### 怎么办：

本题我们可以利用两个栈，其中一个用来存取数字，另一个用来存取运算符，但是要注意不是一昧的存取直到表达式结束而是在表达式中途特定的时候得将栈中的元素取出来进行计算，具体参见下面的图示：

![1683447582755](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1683447582755.png)



![1683447659408](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1683447659408.png)



这里我们直接利用Java中现成的栈Stack，就省的再去定义各种方法了。

注意下述的方法中我们并没有单独定义括号的优先级，当然也没有必要定义，我们只要知道当遇到左括号以及右括号的时候该进行何种处理就好了。

##### notes：java中的栈Stack中的peek()方法的作用是查看栈顶的对象但是不移除它，pop()方法的作用是返回并移除栈顶的对象

```Java
import java.util.*;
public class Main{
    static Stack<Integer> num=new Stack<>();//用来存储字符串中数值的栈
    static Stack<Character> op=new Stack<>();//用来存储字符串中操作符的栈
    static void eval(Stack<Integer> num, Stack<Character> op){
        int b=num.pop();
        int a=num.pop();
        /*
        这里为什么要将a,b顺序反过来？
        对于加法和乘法当然无所谓，但是对于减法和除法一定得明确到底哪个在前哪个在后，
        例如a-b这个式子我们会先扫描到a然后让a入栈，接着会扫描到b然后让b入栈，那么后续
        出栈的时候也是b先出栈
        */
        char ch=op.pop();
        if(ch=='+'){
            num.push(a+b); 
        }else if(ch=='-'){
            num.push(a-b);
        }else if(ch=='*'){
            num.push(a*b);
        }else{
            num.push(a/b);
        }
    }
    
    
    public static void main(String[] args){
        //用哈希表来存储各个操作符对应的优先级
        HashMap<Character,Integer> map=new HashMap<>();
        map.put('+',1);
        map.put('-',1);
        map.put('*',2);
        map.put('/',2);
        
        Scanner sc=new Scanner(System.in);
        String s=sc.next();
        for(int i=0;i<s.length();i++){
            char c=s.charAt(i);
            if(Character.isDigit(c)){
                /*
                如果字符串的第i位是数字的话（数字可能是多位数，
                比如12这种整数不是以一个整体12代入的而是作为字符1和字符2）
                */
               int j=i;
               int x=0;
               while(j<s.length() &&Character.isDigit(s.charAt(j))){
                   //注意这里将数字字符转换成对应的数字是如何操作的
                   x=x*10+s.charAt(j)-'0';
                   j++;
               }
               num.push(x);
               i=j-1;
               /*
               这里为什么要令i=j-1?
               因为当执行完上述while循环以后此时的j指向的字符已经不是数字字符了而且我们
               并没有遍历到该字符，那么理所应当将i指向j啊，而不是将i指向j-1，因为j-1对应的
               字符我们已经遍历过一遍了，如果再遍历一遍的话那不是重复了吗？
               对，没错，但是我们要注意到这里的i=j-1操作是处于if分支里面的，在它的外面还有
               一层for循环会执行i++操作，也就是说当这里执行完i=j-1操作以后到了外面它又会
               执行一次i++操作，刚好指向原来的j指针的位置
               */
            }else if(c=='('){//扫描到左括号我们直接将它入栈就可以了
                op.push(c);
            }else if(c==')'){
                //扫描到右括号我们一直要计算到符号栈中将左括号取出来为止，这也很符合预期：遇到括号
                //先计算括号里面的
                while(op.peek()!='('){
                    eval(num,op);
                }
                op.pop();
            }else{
                while(!op.empty() && op.peek()!='(' && map.get(op.peek())>=map.get(c)){
                    eval(num,op);
                }
                op.push(c);
            }
        }
        while(!op.empty()){
            eval(num,op);
        }
        System.out.println(num.pop());
    }
}
```





## 4、队列

### 是什么：

队列（Queue）是一种线性数据结构，具有“先进先出”（First-In-First-Out，FIFO）的特点，即最先入队的元素最先出队。队列可以理解为排队，只能从队尾插入元素，从队头取出元素，而新增元素时只能插入在队尾，取出元素时只能取出队头元素。 



### 怎么办：

#### 如何实现队列？

在Java中可以使用内置类java.util.Queue或者是java.util.LinkedList类来创建一个队列类的对象，里面定义了队列的各种操作：从队尾插入元素、从队首取出元素、返回队首元素但不删除、判断队列是否为空等方法，但是同样效率低下，所以我们用数组来实现队列

#### 如何用数组实现队列？

队列相比一般的数组的区别就在于队列只能在队尾插入元素，只能在队首删除元素，所以我们需要在一般数组的基础上加上两个指针分别指向队列首尾，当要插入或删除队列元素时只能在这两个指针处进行操作，具体实现见下图：

![1682064376325](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1682064376325.png)



### （829）模拟队列

![1681907187783](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681907187783.png)

解题思路：

就是一般的用数组实现队列的方式

```Java
//用数组模拟队列
import java.util.*;
public class Main{
    static int N=100010;
    static int[] queue=new int[N];
    static int head,tail;//用于指向队列队首元素和队尾元素的指针
    
    public static void init(){
        head=0;
        tail=-1;
    }
    public static void push(int x){
        queue[++tail]=x;
    }
    public static void pop(){
        head++;
    }
    public static void empty(){
        if(head>tail){
            System.out.println("YES");
        }else{
            System.out.println("NO");
        }
    }
    public static void query(){
        System.out.println(queue[head]);
    }
    
    public static void main(String[] args){
        init();//别忘了一开始要初始化队列
        Scanner sc=new Scanner(System.in);
        int M;
        M=sc.nextInt();
        while(M>0){
            M--;
            String s=sc.next();
            if(s.equals("push")){
                int x=sc.nextInt();
                push(x);
            }else if(s.equals("pop")){
                pop();
            }else if(s.equals("empty")){
                empty();
            }else{
                query();
            }
        }
    }
}
```



## 5、单调栈

### 是什么：

单调栈（Monotonic Stack）是一种栈的应用，用于解决一些需要维护单调性的问题。该栈维护的是单调递增或单调递减的序列，可以用来求解一些问题，如找到数组中每个元素左右两侧第一个大于或小于它的数的位置等。在单调栈中，每次添加元素时都对栈进行调整，保证栈内的元素始终保持单调性。 



### 为什么：

给定一个大小为n的数组，现在我们给定一个下标k然后让你来求下标k左边离它最近的并且比它小的数暴力法要怎么求？

首先我们从下标k-1开始从右往左遍历数组元素找到第一个比它小的数然后输出就可以了。该算法的时间复杂度为O(k)，然后如果要找到所有元素它的左边离它最近的且比它小的数该算法的时间复杂度就是O(n^2)

上述算法用数据结构如何实现呢？仔细想想是不是和栈类似啊，当确定数组下标k之后我们先从左到右将数组下标0~(k-1)的元素入栈，然后依次从栈顶取出元素拿它和下标k的数组元素对比，找到第一个比下标为k的元素小的元素就行了，是不是和栈一样遵循先进后出的原则啊

#### 下图为普通栈求解数组下标k左边离它最近的且比它小的数的图示：

当下标k指向数组元素5的时候我们将数组元素5前面的所有元素都入栈，得到栈中的元素为[1，3，6，2]，然后依次从栈顶取出元素去找第一个小于目标元素5的值；然后当下标k指向数组元素1的时候我们将数组元素1前面的所有的元素入栈得到栈中的元素为[1，3，6，2，5]，然后依次从栈顶取出元素去找第一个小于目标元素1的值

仔细观察上述过程看看有没有可以改进的地方，我们注意观察栈中的6和2这一对元素发现了什么？发现了6在2的左边且比2大；这意味着什么？这意味着当我们遍历到数组元素2右边的某个元素时当我们要求该元素的左边离它最近的且比它小的数的时候，只要有2在那么6就永无出头之日（因为2比6小还离该元素更近。那么数组元素6前面的那个数组元素3会不会也让6永无出头之日呢？是不可能的，因为虽然3比6小，但是6在3的右边，假如遇到后面的一个元素是7那么6也是满足比该元素小的而且相比于3的话6离它还更近，所以这里的3是没法对6赶尽杀绝的，只有遇到这里的2才会对6赶尽杀绝），对于栈中的其他元素对也是如此处理，如果在栈中发现了一个逆序对，则将逆序对中前面的那个元素删除，就这样这里的普通栈变成了一个单调递增的栈

总结一下当要寻找某个元素左边离它最近的且比它小的数的时候我们形成的是一个单调递增的栈

![1682136239914](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1682136239914.png)

#### 下图为单调栈求解数组下标k左边离它最近的且比它小的数的图示：

当下标k指向数组元素5的时候如果我们用普通栈的话此时栈中存储的元素应该是[1，3，6，2]但是前面我们也分析过了要去除栈中逆序对中的前面一个元素，所以此时的单调栈中的元素为[1，2]然后我们再从单调栈中依次取出元素来寻找第一个比目标元素小的数；当下标k指向数组元素1的时候此时的单调栈中的元素为[1，2，5]我们还是从栈顶依次取出元素寻找第一个比目标元素小的数

![1682151362554](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1682151362554.png)





### 怎么办：

### （830）单调栈

![1681907078054](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681907078054.png)

解题思路：

就是一个普通的单调栈的题目。不过这里有一点需要注意的是，为什么这里我们不用事先确定好N个整数的数列然后再操作呢？下面的单调队列的题目可是事先已经输入好了数组nums然后再进行操作的，为什么这里不用事先输入好N个整数数列，而是每次输入一个数我们就可以输出在它左边离它最近的且比它小的数呢？因为其实不管单调栈还是单调队列都是动态变化的，而这里我们的单调栈维护的就是当前元素左边可能比它小且离它近的元素，所以当输入一个数以后单调栈中的元素只可能是它的左边已经出现过的元素和它右边的元素无关，所以不用等全部数组元素输入完毕再去建立单调栈。而下面的单调队列则是必须首先确定好数组的所有元素然后让滑动窗口再去移动，否则的话你的数组元素都没有输入完毕你让滑动窗口怎么移动呢？

```Java
//用数组实现单调栈
/*
这里想要我们实现的功能就是单调栈的功能，这里我们用数组来实现单调栈
*/
import java.util.*;
public class Main{
    static int N=100010;
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        int[] stk=new int[N];//创建单调栈
        int head=-1;//指向当前栈顶的下标，一开始的时候栈里面没有元素，栈顶下标为-1
        int n=sc.nextInt();
        while(n>0){
            n--;
            int x=sc.nextInt();
            while(head!=-1 && stk[head]>=x){
                head--;
            }
            if(head!=-1){
                System.out.print(stk[head]+" ");
            }else{
                System.out.print(-1+" ");
            }
            stk[++head]=x;
        }
    }
}
```





## 6、单调队列

### 是什么：

单调队列（Monotonic Queue）是一种队列的应用，用于解决一些需要维护单调性的问题。该队列维护的是单调递增或单调递减的序列，可以用来求解一些问题，如找到滑动窗口里的最大/最小值等。在单调队列中，每次添加元素时都对队列进行调整，保证队列内的元素始终保持单调性。 



### 为什么：

给定一个大小为n的数组和固定大小为k的滑动窗口，我们现在要求在滑动过程中滑动窗口内的最小值。这个问题如果是用暴力算法该如何解决呢？

很简单，对于滑动窗口每到一个位置我们就遍历里面的元素找到最小值，这样在每个滑动窗口中需要进行k次操作寻找最小值，而总共滑动窗口会经过n-k+1次位置变换，所以总共的时间复杂度为O(k*（n-k+1）)，因为n>>k所以时间复杂度为O(n * k)。

上述做法用数据结构该如何实现呢？这里的滑动窗口是不是就像一个固定长度的队列啊，每次当滑动窗口右移的时候都相当于队尾插入新的元素同时队头删除旧的元素，然后每次滑动窗口到达一个新的位置我们就还是需要遍历这个队列的所有元素，所以该算法的时间复杂度还是O(n * k)。

#### 下图为一般队列中求滑动窗口最小值的图示：

注意看因为我们要求的是滑动窗口中的最小值，所以我们注意看窗口中的6和2这一对元素，我们发现2比6后入队列而且2比6小，这意味着什么呢？意味着只要2进入了队列那么6就永无出头之日了，因为2只要在队列中那么当要找最小值的时候就一定不会去找6（暂时不看1和3），那么6就心想慢慢熬吧，等把2熬出了窗口那么6就还是有机会可以作为窗口中的最小值的但是这个想法是不可能的，因为2比6后入队列所以当然比6后出队列，也就是说真的只要2在6后面那么6就永无出头之日。所以当我们遍历到2这个元素的时候发现它前面有个6那么我们就可以把6从队列中删除了因为它没有用；对于其他元素也是类似的处理，然后我们就得到了单调队列

![1682087140563](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1682087140563.png)

#### 下图为单调队列中求滑动窗口最小值的图示：

这里有个问题为什么第一幅图下面写的此时单调队列中的元素为[1，2]呢？因为首先1作为窗口中的最小值放进去没有问题，同时由于上面的分析我们明白了3、6作为元素2前面的元素比2先入队列且比2大，所以当2入队列之后它们便再无出头之日，只要2在队列中它们便再没法输出出来（这就是比你年轻还比你能干），那为什么1没有把2给排除出队列呢？因为1在2的前面，这就意味着1会先出队列那么当1出了队列以后2还是有机会作为滑动窗口的最小值输出出来的（比你能干但是比你老，等他没了你还是有可能会出头的），然后后面对于新遍历的元素也是同样的处理，当发现逆序对以后就将队尾元素删除，最后的队列发现了它们单调递增的属性，不过这里还要注意的一点就是你的单调队列是不能无限延长的，因为滑动窗口的大小是固定的，所以当窗口后移的过程中队列里面的元素也是需要不断维护的，我们需要检查队列头的元素下标是否超出窗口下标范围

单调队列求滑动窗口最小值问题的时间复杂度分析：

当给定单调递增队列后我们再去找滑动窗口的最小值就不用遍历一遍单调队列了，而是直接找到队列的首元素就可以了，时间复杂度为O(1)，那么总共有多少个滑动窗口的位置呢？总共有n-k+1个位置，所以总的时间复杂度为O(1 * （n-k+1）)，因为n>>k所以时间复杂度为O(n)

![1682087165767](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1682087165767.png)



#### 单调队列的原理：

![1682067973411](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1682067973411.png)



#### notes：单调队列中为了维护队列的单调性队尾是可以出队的，这是与普通队列的一点区别



### 怎么办：

### （154）滑动窗口

![1681906913669](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681906913669.png)

解题思路：

单调队列的思想上面已经介绍过了，不过有一个点需要注意的是上述过程中我们说单调队列存储的是元素值，但其实不是，因为如果单调队列存储的是元素值的话，那么后续当我们要判断一个队列的队头元素下标是否超出滑动窗口范围的时候我们就无能为力了，所以其实这里的单调队列我们存储的是原数组的下标

```Java
//这里的代码是对的，思想也是如此，但就是因为数据太多了而且输入输出的速度太慢了造成超时
//用数组实现单调队列
import java.util.*;
import java.io.*;
public class Main{
    static int N=1000010;
    static int[] queue = new int[N];//用于维护单调队列
    static int[] nums = new int[N];//用于输入给定的数组
    static int n,k;//n表示数组长度，k表示滑动窗口长度
    static int head,tail;//队列的队首指针和队尾指针
    public static void main(String[] args) throws IOException{
        Scanner sc=new Scanner(System.in);
        n=sc.nextInt();
        k=sc.nextInt();
        nums=new int[N];
        for(int i=0;i<n;i++){
            nums[i]=sc.nextInt();
        }
        int[] queue=new int[N];//构建单调队列



        head=0;
        tail=-1;//用于指向单调队列的队首和队尾

        for(int i=0;i<n;i++){
            //对数组nums里面的每个滑动窗口进行最小值的判断（这里是利用单调队列来实现的）
            if(head<=tail && i-queue[head]+1>k){
                //首先判断队列是否为空，以及判断单调队列最左边的元素是否越过滑动窗口边界
                head++;
            }
            while(head<=tail && nums[queue[tail]]>=nums[i]){
                tail--;
            }
            queue[++tail]=i;

            if(i>=k-1){
                System.out.print(nums[queue[head]]+" ");
            }
        }
        System.out.println();


        head=0;
        tail=-1;//用于指向单调队列的队首和队尾

        for(int i=0;i<n;i++){//对数组nums里面的每个滑动窗口进行最大值的判断（这里是利用单调队列来实现的）
            if(head<=tail && i-queue[head]+1>k){//首先判断队列是否为空，以及判断单调队列最左边的元素是否越过滑动窗口边界
                head++;
            }
            while(head<=tail && nums[queue[tail]]<=nums[i]){
                tail--;
            }
            queue[++tail]=i;

            if(i>=k-1){
                System.out.print(nums[queue[head]]+" ");
            }
        }
    }
}



//这是改进了输出输出后的代码，思想都没变，只是变化了输入输出的方式加快了速度
//用数组实现单调队列
import java.util.*;
import java.io.*;
public class Main{
    static int N=1000010;
    static int[] queue = new int[N];
    static int[] nums = new int[N];
    static int n,k;
    static int head,tail;
    public static void main(String[] args) throws IOException{
        // Scanner sc=new Scanner(System.in);
        // int n,k;//n表示数组长度，k表示滑动窗口长度
        // n=sc.nextInt();
        // k=sc.nextInt();
        // int[] nums=new int[N];
        // for(int i=0;i<n;i++){
        //     nums[i]=sc.nextInt();
        // }
        // int[] queue=new int[N];//构建单调队列

        BufferedReader bf = new BufferedReader(new InputStreamReader(System.in));
        PrintWriter wt = new PrintWriter(new OutputStreamWriter(System.out));
        String[] st = bf.readLine().split(" ");
        n = Integer.parseInt(st[0]);
        k = Integer.parseInt(st[1]);
        String[] str = bf.readLine().split(" ");
        for(int i = 0 ; i < n ; i ++ ) nums[i] = Integer.parseInt(str[i]);



        head=0;
        tail=-1;//用于指向单调队列的队首和队尾

        for(int i=0;i<n;i++){//对数组nums里面的每个滑动窗口进行最小值的判断（这里是利用单调队列来实现的）
            if(head<=tail && i-queue[head]+1>k){//首先判断队列是否为空，以及判断单调队列最左边的元素是否越过滑动窗口边界
                head++;
            }
            while(head<=tail && nums[queue[tail]]>=nums[i]){
                tail--;
            }
            queue[++tail]=i;

            if(i>=k-1){
                System.out.print(nums[queue[head]]+" ");
            }
        }
        System.out.println();


        head=0;
        tail=-1;//用于指向单调队列的队首和队尾

        for(int i=0;i<n;i++){//对数组nums里面的每个滑动窗口进行最大值的判断（这里是利用单调队列来实现的）
            if(head<=tail && i-queue[head]+1>k){//首先判断队列是否为空，以及判断单调队列最左边的元素是否越过滑动窗口边界
                head++;
            }
            while(head<=tail && nums[queue[tail]]<=nums[i]){
                tail--;
            }
            queue[++tail]=i;

            if(i>=k-1){
                System.out.print(nums[queue[head]]+" ");
            }
        }
    }
}

```







## 7、kmp

### 是什么：

kmp是一种字符串匹配算法，用于在一个主串中寻找模板串的出现位置，它的核心思想是利用已经匹配过的字符尽可能减少不必要的比较从而提高匹配的效率，该算法的时间复杂度为O(m+n)（其中m为主串的长度，n为模板串的长度）



### 为什么：

最朴素的字符串匹配算法就是暴力算法，如下图所示，主串长度为m模板串长度为n，首先拿模板串的第一位与主串的第一位开始进行匹配，匹配了就进行下一位的匹配，直到在模板串的某个位置发生了不匹配，这时候的暴力做法是怎么处理的呢？直接将模板串后移一位，即重新拿模板串的第一位与主串的第二位开始进行匹配，如果匹配了就接着进行下一位的匹配，，，，可以看到假使遇上了模板串与主串在某个位置不匹配的情况，暴力的做法都是将模板串右移一位然后接着进行匹配，这样的算法效率太低了，那么上述算法的时间复杂度到底是多少呢？

我们来分析一下：

首先主串长度为m模板串长度为n，模板串每次与主串进行一次匹配大概会进行n次比较操作（就是检查模板串的这n个字符是否与主串的n个字符相等），然后模板串会与主串进行多少次匹配呢？因为模板串每次确定一个位置就会进行一次匹配，又因为模板串每次都是移动一个位置，所以模板串总共会确定m-n+1次位置；

综上，时间复杂度为O(n*（m-n+1）)，又因为一般情况下m>>n，所以时间复杂度为O(n * m)

![1681908456132](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681908456132.png)



现在我们对上述方法提出改进，那么该如何改进呢？详细分析见下图：

![1681909966738](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681909966738.png)





#### 什么是next数组？（注意这里的next数组我们存储的是最长前后缀匹配的长度）

next数组中的元素next[ i ]表示模板串在下标i处之前的子段的最长前后缀匹配的长度，这是整个KMP算法的精髓，正是有了这个数组才避免了暴力法一个位置一个位置移动的情况

![1681912025425](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681912025425.png)



#### 如何理解这里的next[]数组？

如下图所示，我们将模板串与主串进行匹配（绿色部分表示匹配的部分，红色部分表示不匹配的部分），首先在上面的图中我们看到模板串与主串匹配了一部分，但是在后面的部分发生了不匹配，没办法我们只能将模板串后移接着进行匹配，首先要明确我们为什么要移动模板串？目的是为了找到一个位置使得模板串与主串完全匹配。假设我们移动到下图的位置然后全部匹配了，我们注意观察这时候的模板串和主串的情况，是不是**模板串中原本用黑线标注出来的子串与主串中用黄线标注出来的子串发生了匹配，而主串中用黄线标注出来的部分原先是和模板串中用黄线标注出来的部分匹配的（在原先的匹配图中可以看出模板串中用黑线标注的部分和主串中用黑线标注的部分二者是匹配的，而原先在模板串中用黄线标注的部分和主串中用黄线标注的部分二者是匹配的），这就代表原先模板串中用黑线标注出来的部分和模板串中原先用黄线标注出来的部分二者是匹配的，这就是为什么要求模板串中各个位置处最长前后缀的原因**

模板串当它移动的时候满足前后缀匹配的话那么它才有可能完全匹配，否则根本不可能完全匹配

当模板串的一个位置发生了不匹配，但是这同时也意味着该模板串在该位置前面的子串都是匹配的，我们可以利用模板串中该位置处的最长前后缀匹配来确定模板串后移的位置

![1681911659579](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681911659579.png)

#### 如何创建next[]数组？以及如何利用next[]数组进行字符串匹配？

如下图所示，模板串在pos=5处发生了不匹配，但是在0-4处模板串都是匹配的，因此我们返回next[pos-1]=2，这意味着模板串中pos-1处的最长前后缀匹配长度为2（也就是后续模板串移动时是将模板串中下标为1的位置(下标为1的位置和模板串开头的长度就是2)移到当前的这个pos-1的位置来，从图中也可以很方便的理解），所以后续移动到现在的pos-1位置的模板串的坐标应该是next[pos-1]-1，但是这个位置是匹配的所以我们接着进行模板串中下标next[pos-1]-1后面一个位置的匹配，而模板串中后一个位置的坐标就是next[pos-1]-1+1即next[pos-1]，刚好因为next数组保存的就是长度所以保存的内容比一般的下标大1，而我们现在要进行匹配的下标刚好也是需要加1的刚好就匹配上了

![1681916735272](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681916735272.png)



#### （1）传统方法创建next[]数组

对于模板串中位置下标x处的最长前后缀匹配，当我们确定了位置下标x以后我们就看位置下标0-x这么长的子串中最长前后缀为多少（假设模板串长度为n的话，则传统方法构建next数组的时间复杂度为O(n^2)；现在已经构建好了next数组要利用KMP算法进行字符串匹配，时间复杂度为多少？假设主串的长度为m的话，利用KMP算法进行字符串匹配的时间复杂度为O(m+n)，为什么是O(m+n)呢？ 乍一看，pos值可能不停地变成next[pos-1]，代价会很高；但我们使用摊还分析，显然pos值一共顶多自增len(S)次，因此pos值减少的次数不会高于len(S)次。由此，复杂度是可以接受的，不难分析出整个匹配算法的时间复杂度：O(m+n) ）

```Java

//找到模板串中位置下标x处的最长前后缀匹配长度
int getNext(int x) {
    for (int i = x; i >= 0; i--) {
        if (p.substring(0, i).equals(p.substring(x - i + 1, x + 1))) {
            //substring(0,i)方法的形参的范围是左闭右开的[0,i)
            return i;//注意这里的i代表的是长度而不是下标
        }
    }
    return 0;
}

int[] next = new int[p.length()];
for (int x = 0; x < p.length(); x++) {
    next[x] = getNext(x);
}


//已经创建好了next数组，接下来就是利用这个next数组进行字符串匹配
void search() {
    int tar = 0;//表示主串s中的下标值
    int pos = 0;//表示模板串p中的下标值
    while (tar < s.length()) {
        if (s.charAt(tar) == p.charAt(pos)) {
            tar++;
            pos++;
        } else if (pos != 0) {
            /*
            在模板串的pos下标处发生了字符串的不匹配，且pos下标不是0，这就意味着模板串
            从0下标直到pos-1下标处都是与主串匹配的
            */
            pos = next[pos - 1];
            /*
            因为模板串在pos下标处发生了不匹配所以我们更换pos下标重新进行匹配，同时还得保证
            原先的pos-1位置前的前后缀匹配，因为这样可以减少匹配的次数。
            注意这里的next[]数组返回的是前后缀匹配的长度而不是下标
            因为原先的模板串在pos位置处发生了不匹配，而在pos-1位置处都还是匹配的，而我们返回
            的next[pos-1]则是模板串在pos-1位置处的最长前后缀匹配长度注意不是下标，所以实际上
            当将模板串后移后来到现在的现在的pos-1指针位置处的下标应该是next[pos-1]-1(因为
            next数组返回的是长度所以得到下标的话还得减1，因为pos-1位置处是匹配的，所以应该匹配
            pos位置的元素，所以我们得匹配模板串中next[pos-1]-1下标后面一个位置的元素，而它
            后面一个位置的左边就是next[pos-1]-1+1=next[pos-1]，这里因为next数组存储的是长度
            所以比一般的下标加1，而刚好我们现在要求的坐标就是原先的坐标后面一位所以也得加上1，刚好
            就匹配上了，具体分析见上图)
            */
        } else {
            tar++;
        }
        if (pos == p.length()) {//当模板串全部都匹配以后输出这时候模板串开头对应的主串中的下标
            System.out.println(tar - pos);
            pos = next[pos - 1];
        }
    }
}
```



#### （2）如何快速求next数组（利用next数组的递推关系）

![1681958316255](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681958316255.png)

```java 
//利用递推关系快速创建模板串的next数组

String P=sc.next();//模板串
int n=P.length();//n为模板串的长度
char[] p=P.toCharArray();//模板串对应的字符数组
int[] next=new int[n];
int x=1,now=0;
while(x<n){
    if(p[x]==p[now]){
        next[x]=now+1;
        x++;
        now++;
    }else if(now>0){
        now=next[now-1];
    }else{
        next[x]=0;
        x++;
    }
}


//已经求得了模板串的next数组，接下来利用next数组进行字符串匹配的方法和上述传统方法一样
/*
这里的字符型数组char[] s表示主串对应的字符数组，char[] p表示模板串对应的字符数组
int m表示主串对应的长度，int n表示模板串对应的长度
*/
void search(){
    int tar=0;//表示主串中的用来匹配的下标
    int pos=0;//表示模板串中的用来匹配的下标
    while(tar<m){
        if(s[tar]==p[pos]){
            tar++;
            pos++;
        }else if(pos>0){
            pos=next[pos-1];
        }else{
            tar++;
        }
        
        if(pos==n){
            System.out.println(tar-pos);
            pos=next[pos-1];
        }
    }
   
}
```





### 怎么办：

### （831）KMP字符串

![1681906680308](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1681906680308.png)

解题思路：

就是按照上面的KMP算法进行字符串匹配，不过这里有一点需要注意的就是因为数据太多了，主串S最多有100万个字符所以使用普通的输入输出的话速度太慢了，所以这里可以利用流进行处理

```Java
import java.util.*;
public class Main{
    static int N=100010;
    static int M=1000010;
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        int n;//表示模板串P的长度
        n=sc.nextInt();
        String P=sc.next();
        int m;//表示主串S的长度
        m=sc.nextInt();
        String S=sc.next();
        char[] s=S.toCharArray();//将主串S转换为字符数组后续便于处理
        
        //利用KMP算法进行字符串的匹配
        
        //首先创建模板串P的next数组
        char[] p=P.toCharArray();//将模板串P转换为字符数组后续便于处理
        int[] next=new int[n];
        int x=1;//x表示当前的模板串的下标
        int now=0;//表示模板串中x下标处匹配的字符前缀的最后一个下标
        while(x<n){
            if(p[x]==p[now]){
                next[x]=now+1;
                now++;
                x++;
            }else if(now>0){
                now=next[now-1];
            }else{
                next[x]=0;
                x++;
            }
        }
        
        //上面已经创建好了模板串的next数组，接下来就是利用这个数组进行字符串的匹配
        int tar=0;//用于匹配的主串的下标
        int pos=0;//用于匹配的模板串的下标
        while(tar<m){
            if(s[tar]==p[pos]){
                tar++;
                pos++;
            }else if(pos>0){
                pos=next[pos-1];
            }else{
                tar++;
            }
            
            if(pos==n){
                System.out.print(tar-pos+" ");
                pos=next[pos-1];
            }
        }
    }
}







//这里存在输入输出速度太慢的问题，因为数据太多了主串最多有100万个字符，所以用输入输出流提高速度
import java.util.*;
import java.io.*;
public class Main{
    static int N=100010;
    static int M=1000010;
    public static void main(String[] args) throws IOException{
        // Scanner sc=new Scanner(System.in);
        // int n;//表示模板串P的长度
        // n=sc.nextInt();
        // String P=sc.next();
        // int m;//表示主串S的长度
        // m=sc.nextInt();
        // String S=sc.next();
        // char[] s=S.toCharArray();//将主串S转换为字符数组后续便于处理
        
        BufferedReader bf = new BufferedReader(new InputStreamReader(System.in));
        BufferedWriter wt = new BufferedWriter(new OutputStreamWriter(System.out));
        int n;
        n=Integer.parseInt(bf.readLine());//输入模板串P的长度
        String P=bf.readLine();//输入模板串P
        char[] p=P.toCharArray();//将模板串P转换为字符数组后续便于处理
        int m;
        m=Integer.parseInt(bf.readLine());//输入主串S的长度
        String S=bf.readLine();//输入主串
        char[] s=S.toCharArray();//将主串S转换为字符数组后续便于处理
        
        
        
        //利用KMP算法进行字符串的匹配
        
        //首先创建模板串P的next数组
        int[] next=new int[n];
        int x=1;//x表示当前的模板串的下标
        int now=0;//表示模板串中x下标处匹配的字符前缀的最后一个下标
        while(x<n){
            if(p[x]==p[now]){
                next[x]=now+1;
                now++;
                x++;
            }else if(now>0){
                now=next[now-1];
            }else{
                next[x]=0;
                x++;
            }
        }
        
        //上面已经创建好了模板串的next数组，接下来就是利用这个数组进行字符串的匹配
        int tar=0;//用于匹配的主串的下标
        int pos=0;//用于匹配的模板串的下标
        while(tar<m){
            if(s[tar]==p[pos]){
                tar++;
                pos++;
            }else if(pos>0){
                pos=next[pos-1];
            }else{
                tar++;
            }
            
            if(pos==n){
                //System.out.print(tar-pos+" ");
                wt.write(tar-pos+" ");
                pos=next[pos-1];
            }
        }
        wt.flush();
        wt.close();
        bf.close();
    }
}
```





## 8、Trie树（Trie树不是二叉树，它可以有很多分支）

### 是什么：

Trie树，也称字典树或前缀树，是一种树形数据结构，用于快速地存储和检索字符串数据。每个节点代表一个字符串前缀，从根节点到叶子节点的路径表示一个完整的字符串，因此用户可以根据字符串前缀快速检索和匹配字符串。Trie树常被用于自动完成、拼写检查、单词搜索等应用领域。 



### 怎么办：

#### 如何理解Trie树？

Trie树的每个节点都代表一个字符串的前缀，从根节点开始，一直到某个节点代表的前缀正好等于需要查询的字符串为止，就可以查找到该字符串。另外，每个节点还有若干个子节点，代表该节点所代表的前缀后续可能跟随的字符，这也是Trie树插入和查询的核心实现。



#### Trie树存储字符串的示意图如下：（每次存储或者查询字符串都必须得从根节点出发）

（0）最开始的根节点为0号节点；

（1）插入第1个字符串，将字符串的每一个字符作为节点插入到Trie树中，首先插入第1个字符'a'，发现Trie树中根节点下没有'a'节点所以我们创建一个新的节点给它标记为1号节点然后将'a'放进1号节点；再沿着1号'a'节点向下插入第2个字符'b'发现1号'a'节点下也没有'b'节点所以我们创建一个新的节点给它标记为2号节点然后将'b'放进2号节点；再沿着2号'b'节点向下插入第3个字符'c'发现2号'b'节点下也没有'c'节点所以我们创建一个新的节点给它标记为3号节点然后将'c'放进3号节点；再沿着3号'c'节点向下插入第4个字符'd'发现3号'c'节点下也没有'd'节点所以我们创建一个新的节点给它标记为4号节点然后将'd'放进4号节点；再沿着4号'd'节点向下插入第5个字符'e'发现4号'd'节点下也没有'e'节点所以我们创建一个新的节点给它标记为5号节点然后将'e'放进5号节点；最后在5号'e'节点的位置处用五角星标记一下表示从根节点到此节点的路径为一个字符串（后面会说明为什么要特意标记一下）；

（2）再来插入第2个字符串，将字符串的每一个字符作为节点插入到Trie树中，首先插入第1个字符'a'，发现Trie树中根节点下已经有了'a'节点它就是1号'a'节点所以我们不用另外创建一个新的节点而是顺着1号'a'节点继续往下寻找；沿着1号'a'节点向下插入第2个字符'b'发现1号'a'节点下已经有了'b'节点它就是2号'b'节点所以我们不用另外创建一个新的节点而是顺着2号'b'节点继续往下寻找；沿着2号'b'节点向下插入第3个字符'c'发现2号'b'节点下已经有了'c'节点它就是3号'c'节点所以我们不用另外创建一个新的节点而是顺着3号'c'节点继续往下寻找；沿着3号'c'节点向下插入第4个字符'd'发现3号'c'节点下已经有了'd'节点它就是4号'd'节点所以我们不用另外创建一个新的节点而是顺着4号'd'节点继续往下寻找；沿着4号'd'节点向下插入第5个字符'f'发现4号'd'节点下并没有一个'f'节点所以我们得另外重新创建一个新的节点给它标记为6号节点然后将'f'放进6号节点中；最后在6号'f'节点的位置处用五角星标记一下表示从根节点到此节点的路径为一个字符串；

（3）插入第3个字符串，将字符串的每一个字符作为节点插入到Trie树中，首先插入第1个字符'b'，发现Trie树中根节点下没有'b'节点所以我们创建一个新的节点给它标记为7号节点然后将'b'放进7号节点；再沿着7号'b'节点（这里的7号'b'节点区别于上述的2号'b'节点，为什么要给每个节点编号？目的就是要区分不同位置的相同数值的节点）向下插入第2个字符'c'发现7号'b'节点下也没有'c'节点所以我们创建一个新的节点给它标记为8号节点然后将'c'放进8号节点；再沿着8号'c'节点向下插入第3个字符'd'发现8号'c'节点下也没有'd'节点所以我们创建一个新的节点给它标记为9号节点然后将'd'放进9号节点；再沿着9号'd'节点向下插入第4个字符'e'发现9号'd'节点下也没有'e'节点所以我们创建一个新的节点给它标记为10号节点然后将'e'放进10号节点；再沿着10号'e'节点向下插入第5个字符'f'发现'e'节点下也没有'f'节点所以我们创建一个新的节点给它标记为11号节点然后将'f'放进11号节点；最后在11号'f'节点的位置处用五角星标记一下表示从根节点到此节点的路径为一个字符串；

（4）插入第4个字符串，将字符串的每一个字符作为节点插入到Trie树中，首先插入第1个字符'b'，发现Trie树中根节点下已经有了'b'节点它就是7号'b'节点所以我们不用另外创建一个新的节点而是顺着7号'b'节点继续往下寻找；沿着7号'b'节点向下插入第2个字符'c'发现7号'b'节点下已经有了'c'节点它就是8号'c'节点所以我们不用另外创建一个新的节点而是顺着8号'c'节点继续往下寻找；沿着8号'c'节点向下插入第3个字符'd'发现8号'c'节点下已经有了'd'节点它就是9号'd'节点所以我们不用另外创建一个新的节点而是顺着9号'd'节点继续往下寻找；沿着9号'd'节点向下插入第4个字符'f'发现9号'd'节点下没有一个现成的'f'节点所以我们创建一个新的节点给它标记为12号节点然后将'f'放进12号节点；最后在12号'f'节点的位置处用五角星标记一下表示从根节点到此节点的路径为一个字符串；

（5）插入第5个字符串，将字符串的每一个字符作为节点插入到Trie树中，首先插入第1个字符'a'，发现Trie树中根节点下已经有了'a'节点它就是1号'a'节点所以我们不用另外创建一个新的节点而是顺着1号'a'节点继续往下寻找；沿着1号'a'节点向下插入第2个字符'b'发现1号'a'节点下已经有了'b'节点它就是2号'b'节点所以我们不用另外创建一个新的节点而是顺着2号'b'节点继续往下寻找；沿着2号'b'节点向下插入第3个字符'c'发现2号'b'节点下已经有了'c'节点它就是3号'c'节点，至此第5个字符串插入完毕；最后在3号'c'节点的位置处用五角星标记一下表示从根节点到此节点的路径为一个字符串；

（6）插入第6个字符串，将字符串的每一个字符作为节点插入到Trie树中，首先插入第1个字符'c'，发现Trie树中根节点下没有'c'节点所以我们创建一个新的节点给它标记为13号节点然后将'c'放进13号节点；再沿着13号'c'节点向下插入第2个字符'a'发现13号'c'节点下也没有'a'节点所以我们创建一个新的节点给它标记为14号节点然后将'a'放进14号节点；再沿着14号'a'节点向下插入第3个字符'b'发现14号'a'节点下也没有'b'节点所以我们创建一个新的节点给它标记为15号节点然后将'b'放进15号节点；最后在15号'b'节点的位置处用五角星标记一下表示从根节点到此节点的路径为一个字符串；

![1682392548374](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1682392548374.png)

#### 如何实现Trie树呢？

观察上述过程，我们发现每一个节点处它的直属后面能插入最多26个节点（比如根节点处或下标1节点处它的下面直属的能插入最多26个小写英文字母，这里的小写英文字母我们得将它们转化为数字下标，直接拿英文字母减去字符'a'即可：  -'a'），所以我们可以用一个二维数组来实现Trie树：son[N] [26]，其中第一维下标N代表Trie树中节点的下标编号（第一维下标为k代表的就是Trie树中的第k个下标的节点），第二维下标26代表Trie树中该下标位置处直属后面插入的到底是哪一个字符（比如插入'a'字符对应的第二维下标就是0、插入的是'z'字符对应的第二维下标就是25），然后该数组存储的内容是什么呢？**son[k] [i]存储的就是Trie树中下标为k的节点它的直属后面插入了第i个字符节点后（这里的i如何计算？若是插入的'a'字符对应的应该是0，若是插入的'z'字符对应的应该是25）这个新插入的第i个字符节点的下标**（就拿上面的例子来说：在0号节点下插入了一个'a'节点给该'a'节点起的下标编号为1所以son[0] [0]=1（为什么这里的数组第二个下标0代表'a'节点呢？因为我们要将所有的26个小写英文字母转化为数字，所以得减去字符'a'，而字符 'a' - 'a' =0）；在1号节点下插入了一个'b'节点后给该'b'节点起的下标编号为2所以son[1] [1]=2；在2号节点下插入了一个'c'节点后给该'c'节点起的下标编号为3所以son[2] [2]=3；在3号节点下插入了一个'd'节点后给该'd'节点起的下标编号为4所以son[3] [3]=4；在4号节点下插入了一个'e'节点后给该'e'节点起的下标编号为5所以son[4] [4]=5；在4号节点下插入了一个'f'节点后给该'f'节点起的下标编号为6所以son[4] [5]=6；在0号节点下插入了一个'b'节点后给该'b'节点起的下标编号为7所以son[0] [1]=7等等等等二维数组的编号诸如此类

**综上所述我们可以用一个二维数组来实现Trie树，那么前面我们也说过了Trie树是用来快速存储和查询字符串的，但是你这里的二维数组存储的数据是节点的下标，如何能够存储字符串呢？**

**这里我们要知道的一点是Trie树中的每一个节点下标都对应一个字符节点，比如说现在有一个字符节点它的下标为i，然后我们在它的后面插入了另一个字符节点q它对应的下标为j，所以这个Trie树中的下标为j的节点对应的就是字符节点q，也就是说每一个下标和字符节点之间是一一对应的，我们从根节点出发一路来到了叶子节点处，中间会经过好多个下标的中间节点，每个中间节点都对应一个字符，所以这一路走下来也就对应了一个字符串，而且从根节点走到此叶子节点的路只有一条意味着只有唯一的一个字符串与这条路径对应；我们只要记录字符串的结尾对应的是几号节点就能知道它对应的是哪个字符串了。**

**为什么呢？不会出现两个不同的字符串对应同一个结尾节点的吗？当然不可能，因为前面我们已经看到了两个字符串如果有公共前缀那么它们在公共前缀的部分会同路而行，后续又会分开了，走向不同的叶子节点**

Trie树的时间复杂度为O(len)，其中len为要搜索或插入字符串的长度，对于搜索操作来说，这个时间复杂度是与查询是否成功无关的，具有较好的稳定性和可靠性。但由于空间复杂度较高，每个节点要存储指向孩子节点的指针，所以一旦字符集过大，或字符串长度过长，就可能导致Trie树的空间占用过大，影响效率。



#### notes：为什么要在Trie树中每个字符串结尾的位置给它做上五角星标记？

其实不一定非要是五角星标记，别的标记也可以，做标记的主要目的是说明从根节点到该节点位置处是一个字符串的结尾，这样我们才知道该位置处是一个字符串啊，就拿下面的例子来说：先插入了字符串"abcde"，然后又插入了字符串"abc"，如果我们不在位置"c"处标记一下的话那么我们怎么知道该位置处是一个字符串的结尾而不是仅仅一个中间节点呢？



### （835）Trie字符串统计

![1682174235191](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1682174235191.png)

解题思路：

由上面的示意图我们知道了向Trie树中插入字符串是如何操作的，接下来就是如何将上述过程转换成代码；我们要做的就是两个问题：

（1）如何向Trie树中插入字符串？

（2）如何统计Trie树中每个字符串出现了多少次？

我们是用二维数组来实现Trie树的，其中第一维的下标代表节点的编号，第二维的下标代表每个节点处它可能的取值；就比如这里我们往Trie树中插入字符串（字符仅包含小写英文字母），所以这里的第一维的下标就指代总共有多少个字符，第二维的下标就代表每个节点处它所有可能的取值范围，这里的取值范围就是26个小写的英文字母。当我们向Trie树中插入一个节点的时候首先我们从根节点出发

```java
import java.util.*;
public class Main{
    static int N=100010;
    static int[][] son=new int[N][26];//这里的N代表字符串的最大长度，26代表字符串的每个位置上的数取值的可能性为26个小写英文字母
    static int idx=0;//用于指向当前节点的下标，当向Trie树中插入节点的时候我们要给每个插入的节点编号，用的就是idx下标
    static int[] count=new int[N];
    /*
    用于记录每个字符串出现了多少次，这里是如何能做到给每个字符串记录的呢？
    一般情况下我们是得用一个哈希表，其中key值为字符串value值为对应字符串出现的次数，
    这样每一个字符串和出现的次数一一对应的啊，这里仅仅就用一个一维数组如何能记录到字符串
    和它们出现次数之间的对应关系的呢？
    这里就不得不感叹设计的巧妙了，因为我们在向Trie树中插入节点的时候会给每一个节点编号，
    因此每一个不同的字符串它的结尾的节点都对应唯一的一个编号，我们以这个编号作为count数组
    的下标就可以确定字符串和对应的出现次数之间的一一对应的关系了。
    
    这里不会出现count数组长度不够用的情况吗？
    好像是会出现的吼，因为我们要给每个前缀不同的新插入的字符建立唯一的一个编号，那么这里最多
    会插入2*10^4个字符串，如果这些字符串都没有公共前缀而且每个字符串都很长那么要新建立的每个
    字符对应的编号不久特别多了嘛，此时这里的count数组的大小还够用吗？
    是够用的，因为题目中说明了所有输入的字符串的总长度不超过10^5，所以即使是给这些字符串每一
    个字符单独确定一个编号也是够用的。
    */
    
    public static void insert(char[] chars){
        int p=0;
        for(int i=0;i<chars.length;i++){
            int u=chars[i]-'a';
            if(son[p][u]==0){
                son[p][u]=++idx;
            }
            p=son[p][u];
        }
        count[p]++;
    }
    
    public static int query(char[] chars){
        int p=0;
        for(int i=0;i<chars.length;i++){
            int u=chars[i]-'a';
            if(son[p][u]==0){
                return 0;
            }else{
                p=son[p][u];
            }
        }
        return count[p];
    }
    
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        int n;
        n=sc.nextInt();
        while(n>0){
            n--;
            String s1,s2;
            s1=sc.next();
            s2=sc.next();
            char ch=s1.charAt(0);
            if(ch=='I'){
                insert(s2.toCharArray());
            }else{
                System.out.println(query(s2.toCharArray()));
            }
        }
    }
}
```



### （143）最大异或对

![1683358499987](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1683358499987.png)

解题思路：

##### 是什么：

给定n个数，其中两两进行异或操作，现在要求找出异或值最大的那个值



##### 怎么办：

##### （1）这题用暴力法该怎么做呢？

首先我们看用暴力法如何解决这道题，用两层for循环，其中外层的for循环首先固定一个数，然后内层的for循环再依次去遍历所有的数二者进行异或运算，然后用一个值记录所有的数进行异或运算后的最大值就是我们要找的结果，该暴力算法的时间复杂度为O(n^2)；

##### （2）有没有更好的办法呢？如何将本题与Trie树构建联系呢？

当然有，就是我们要提的Trie树，首先看一下上述过程是否有可以优化的地方，其中外层的for循环首先固定一个数这个显然没法再优化，关键就是内层的for循环遍历每一个数这是可以优化的，那么该如何优化呢？因为我们想找到两个数异或后最大的值，所以在给定当前值的情况下，我们当然希望从二进制高位开始的每一位都与当前值相反这样进行异或操作以后得到的值就更大（比如当前值是3假设它的二进制表示是0011，那么我们当然希望找到一个数的二进制表示是1100也就是12，这样的话它们俩进行异或运算后的结果最大是1111也就是15），这样我们就避免了盲目遍历每一个数的操作而是首先去寻找二进制位上与当前值二进制位上相反的数是否存在；上述过程图示如下：

在每一个分支的时候我们最好的情况就是找到该二进制位上的数与当前数在二进制位上相反这样进行异或运算后的值最大，但是假如集合中不存在该二进制位上的数与当前数在二进制位上相反该咋办呢？那就说明没有这种最好的情况，那我们就退而求其次呗

将下述的节点图旋转90度发现了什么，它就是我们要找的Trie树啊，如此我们就构建起了本题与Trie树的关系

![1683361310544](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1683361310544.png)



##### （3）如何将每一个数存储到Trie树中？

由上述过程我们得知要将每一个输入的数换做二进制表示然后存储到Trie树中，那么我们该如何将十进制的数转换成二进制的数呢？利用除2取余法吗？这样太麻烦了还得另外弄个集合专门来存储余数！其实还有一个更好的办法，我们可以通过移位操作来判别出每一个数它对应的二进制各个位置上的数到底是0还是1；就拿本题来说，每一个数的数据范围是0<=Ai<2^31，这就说明每一个数它的最高位就是2^30，如果我们要想直到该数对应的2^30位置上的数到底是0还是1的话只要将该数右移30位即可，图示如下：

![1683363458441](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1683363458441.png)



##### （4）在每一个二进制位上进行异或操作后的结果到底是多少，是1吗？

当然不是，比如说现在在2^2位置上进行异或操作后得到的值是1那这代表结果是1吗？不是，而是在该位置上的结果是1，最终的结果是1*（2^2）也就是4，那么由上述我们得到要想得到每一个数各个二进制位上的数值是多少我们得进行向右移位的操作，同样的如果我们得到该位置上的数进行异或运算后的结果是1的话也仅仅只能代表在该位置上的运算结果是1那么最终的结果是多少呢？还得进行向左移位的运算，并且你当初向右移位了几个位置现在就得向左移位几个位置回去



##### （5）这里用来表示Trie树的二维数组son[] []，其中的第一维下标代表什么？

与所有的Trie树结构相同，其中的二维数组中第一维下标都表示的树中节点的数量，这里最多有多少个节点呢？因为每一个数的二进制位都只有31位，然后这里最多有10^5个数，而且我们知道在往Trie树中加入新的节点的时候只要前缀有一个节点不同，那么后续的所有节点都必须重新创建，所以这里最多有31*（10^5）个节点，为了防止数组下标越界我们把数组大小开大一点即：31 *（10^5）+10=3100010

```Java
import java.util.*;
public class Main{
    static int N=100010;
    static int[] a=new int[N];//用来存储输入的n个数的数组
    static int M=3100010;
    static int[][] son=new int[M][2];
    static int idx=0;
    
    static void insert(int x){
        int p=0;
        for(int i=30;i>=0;i--){
            int u=x>>i&1;
            /*
            为什么要右移以后再和1进行相与操作？
            目的还是为了得到该位置上的数到底是0还是1
            */
            
            if(son[p][u]==0){
                son[p][u]=++idx;
            }
            p=son[p][u];
        }
    }
    //这里是给定一个数x我们直接找出与它进行异或运算后的最大的结果
    static int query(int x){
        int p=0;
        int res=0;//用来与当前的数x进行异或运算后的最大结果
        for(int i=30;i>=0;i--){
            int u=x>>i&1;
            if(son[p][1-u]!=0){
                res+=1<<i;
                /*
                这里的代码非常漂亮。二进制位上每一个位置进行异或运算后的结果为1并不代表最后的结果
                就是1而是在该位置上的结果就是1
                */
                
                p=son[p][1-u];
            }else{
                p=son[p][u];
            }
        }
        return res;
    }
    
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        int n;
        n=sc.nextInt();
        for(int i=1;i<=n;i++){
            a[i]=sc.nextInt();
            insert(a[i]);
        }
        
        int ans=0;//用来存储最后的结果
        for(int i=1;i<=n;i++){
            ans=Math.max(ans,query(a[i]));
        }
        System.out.println(ans);
    }
}
```





## 9、并查集

### 是什么：

并查集（Disjoint Set），也称为不相交集合数据结构，是一种维护集合的数据结构，支持合并集合和判断元素是否在同一个集合中。

并查集的实现主要基于数组和树形结构，其中数组实现的并查集称为并查集数组，树形结构实现的称为并查集森林。在并查集的实现中，每个集合用一个树形结构来表示，其中一个节点所在集合的树的根节点表示该集合。

一般来说，初始化时，每个元素都是一个单独的集合，对于每个集合，选择一个代表元素作为该集合的标识。在后续的操作中，可以通过合并两个集合的代表元素，将这两个集合合并为一个集合。当判断两个元素是否属于同一个集合时，只需要找到这两个元素所在的集合，然后比较这两个集合的代表元素是否相同即可。



### 怎么办：

#### 如何用数组实现并查集？

并查集主要就是两个功能：

（1）合并两个集合

（2）判断两个元素是否属于同一个集合中

这里就有一个问题，怎么样叫做合并两个集合呢？以及怎么判断两个元素是否属于同一个集合呢？如果将集合中所有元素都遍历一遍的话那么太麻烦了，我们的做法是在集合中确定一个代表元素由该代表元素来代表该集合，如果两个元素它们的代表元素相同的话那么它们就属于同一个集合中，否则就不属于同一个集合中，这个代表元素我们找的就是祖先节点；合并两个集合的过程也是类似，我们先找到其中一个集合的代表元素即祖先节点，然后让该祖先节点的祖先节点变成是另一个集合的祖先节点，这时候原先那个集合的祖先节点就相当于后来这个集合中的一个普通的元素了

![1682413012290](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1682413012290.png)



#### 路径压缩是什么？

由上述找一个节点的祖先节点的过程我们看出来很麻烦，得不断地由一个节点去找它的父亲节点，然后父亲节点再往上找它的父亲节点直到找到祖先节点为止，而且后续当要查找某个点的时候又得从头开始由它的父亲节点往上一步步找；那么我们想这样，当我们由某个节点往上找祖先节点的过程中，我们将路径上所有经过的节点都直接连接到祖先节点上，这样当我们后续来找这里的某个节点它的祖先节点的时候我们可以一步就找到它的祖先节点就不用一步步由父亲节点再往上找了。

使用路径压缩以后，找某个节点的祖先节点的时间复杂度降低到**几乎O(1)**



### （836）合并集合

![1682174311732](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1682174311732.png)

解题思路：

利用并查集来实现合并集合，注意路径压缩过程中一些易错的点，具体见下面的代码：

```Java
import java.util.*;
public class Main{
    static int N=100010;
    static int[] father=new int[N];//用于记录每个元素它的父亲节点是谁
    /*
    这是直接寻找节点x的祖先节点，没有经过路径压缩的版本
    public static int find(int x){
        while(x!=father[x]){//当一个节点不是祖先节点的时候
            x=father[x];//我们让它不断去找它的父亲节点
        }
        return x;
    }
    */
    
    /*
    这里想到了路径压缩，但是逻辑有问题，这里我们不是让路径上的所有点指向祖先节点（这样的话
    所有点都不存在了大家都变成了祖先节点）而是让路径上所有点的父亲节点指向祖先节点（这样的话
    祖先节点就变成了路径上所有点的父亲节点）
    public static int find(int x){
    	while(x!=father[x]){//当一个节点不是祖先节点的时候
    		x=find[x];//我们让该节点指向它的祖先节点（这里逻辑有问题）
    	}
    	return x;
    }
    */
    
    /*
    这里想到了路径压缩，但是这里是死循环，永远无法跳出while循环
    public static int find(int x){
    	while(x!=father[x]){
    		father[x]=find[father[x]];
    	}
    	return father[x];
    }
    */
    
    //这才是正确的路径压缩的代码
    //并查集的核心操作用于寻找x节点的祖先，并对路径上的点进行路径压缩
    public static int find(int x){ 
        if(x!=father[x]){
            father[x]=find(father[x]);
            //这里是递归的代码，所以看着只执行了一次但是实际上执行了多次了
        }
        return father[x];
    }
    
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        int n,m;
        n=sc.nextInt();
        for(int i=1;i<=n;i++){
            father[i]=i;//初始时每个节点的祖先都是本身
        }
        m=sc.nextInt();
        while(m>0){
            m--;
            String s=sc.next();
            int a,b;
            a=sc.nextInt();
            b=sc.nextInt();
            char ch=s.charAt(0);
            if(ch=='M'){//将a元素所属的集合合并到b元素所属的集合中
                father[find(a)]=find(b);
            }else{
                if(find(a)==find(b)){
                    System.out.println("Yes");
                }else{
                    System.out.println("No");
                }
            }
        }
    }
}
```



### （837）连通块中点的数量

![1683040534729](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1683040534729.png)

解题思路：

本题与一般并查集的区别就在于这里会询问，连通块中点的数量，那么该怎么办呢?

其实我们只要加上一个size数组用来记录各个点下标对应的集合中有多少个元素就好了，初始时每个节点作为一个独立的集合，每个集合中只有一个节点，后续进行合并集合的时候我们不用去维护每个节点对应的size数组下标的值，只要维护每个集合中祖先节点对应的size数组下标的值就可以了

```java 
import java.util.*;
public class Main{
    static int N=100010;
    static int[] father=new int[N];
    //记录每个节点的父亲节点的数组，初始时每个节点的父亲节点就是本身
    static int[] size=new int[N];
    /*
    记录每个集合中有多少个节点的数组，初始时每个节点作为一个独立的集合，每个集合中只有一个节点，
    后续进行合并集合的时候我们不用去维护每个节点对应的size数组下标的值，只要维护每个集合中祖先
    节点对应的size数组下标的值就可以了
    */
    //定义寻找每个节点的祖先节点的方法，并且加上了路径压缩
    static int find(int x){
        if(x!=father[x]){
            father[x]=find(father[x]);
        }
        return father[x];
    }
    //定义初始化并查集的方法
    static void init(){
        for(int i=1;i<N;i++){
            father[i]=i;
            size[i]=1;
        }
    }
    public static void main(String[] args){
        init();
        Scanner sc=new Scanner(System.in);
        int n,m;
        n=sc.nextInt();
        m=sc.nextInt();
        while(m>0){
            m--;
            String s=sc.next();
            if(s.equals("C")){
                int a=sc.nextInt();
                int b=sc.nextInt();
                if(find(a)==find(b)){
                    continue;
                }
                
                /*
                注意下面这两句的顺序不能颠倒，否则本来的意思是说将合并前两个集合中的元素个数相加，
                变成了将用作祖宗节点的那个集合的元素加了两遍
                father[find(a)]=find(b);//将集合b所在的集合作为祖宗节点的集合
                size[find(b)]+=size[find(a)];
                //此时的a节点对应的祖宗节点已经不是原来的祖宗节点，而是和b节点合并后的祖宗节点
                */
                size[find(b)]+=size[find(a)];
                father[find(a)]=find(b);
            }else if(s.equals("Q1")){
                int a=sc.nextInt();
                int b=sc.nextInt();
                if(find(a)==find(b)){
                    System.out.println("Yes");
                }else{
                    System.out.println("No");
                }
            }else{
                int a=sc.nextInt();
                System.out.println(size[find(a)]);
            }
        }
    }
}
```



### （240）食物链

![1683082808734](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1683082808734.png)

解题思路：

#### 是什么：

给定编号1-n的n个动物，每个动物都是A、B、C中的一种（这三种动物构成一个闭合回路的食物链），但是我们并不知道它到底是哪一种动物，然后给出k次关于两两动物之间关系的描述，其中有真话也有假话，我们要判断这k句话中有多少是假话。

那么什么样的话叫做假话呢？

（1）当前的话与前面某些真话冲突就是假话

（2）当前的话中动物编号x,y比给定最大编号n大就是假话

（3）当前的话中表示动物编号x吃x就是假话

那么又有一个问题，**如何判断前面的某些话是真话呢？比如它一开始说的第一句话是1号和2号动物是同类，第二句话又说1号动物吃2号动物，那么这两句话中肯定只有一个是正确的（也有可能两句话都不正确）但是我们并不知道哪一个是正确的，因为我们并不知道每个动物的具体情况而且因为是第一句话和第二句话所以前面也没有可以参考的话，所以这时候我们就认为先说的那句话就是正确的**，即在这里我们认为第一句话是正确的那么因为第二句话与第一句话矛盾所以第二句话就是假话；

总结一下，就是当出现两个编号的时候但是在这之前我们并不知道这两个编号对应的动物之间的关系，那么这时候出现的第一句涉及这两个编号的关系我们就认为它是正确的

![1683192873196](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1683192873196.png)



#### 怎么办：

##### （1）如何将本题与并查集构建联系？

如果我们知道2对动物之间的关系，则我们可以知道它们之间的所有关系，就可以将它们都加入集合中（在同一个集合中的所有动物的关系都是明确的），比如：

我们现在知道x吃y，又知道y吃z，则我们一定可以知道z吃x，为什么？因为这里只有A、B、C三种动物且构成闭合回路的食物链。所以我们可以将它们都加入一个集合中，但是如果我们要在同一个集合中维护两两动物之间的关系的话实在是太麻烦了（时间复杂度为O(n^2)），有没有更简单的方法呢？当然是有的，我们可以维护每个动物和集合中的祖先节点所代表的动物之间的关系（时间复杂度为O(n)），这样我们就可以借由两个动物和祖先节点之间的关系从而推断出这两个动物之间的相互关系。

##### （2）那么该如何表示每个动物和祖先节点代表的动物这二者之间的关系呢？

这里我们就可以借由每个动物节点和祖先节点之间的距离来表示二者之间的关系，**我们现在规定当i节点在j节点下面距离为1的时候代表i节点吃j节点**，则后续的距离情况如下图所示：

0号节点代表祖先节点，i号节点吃0号节点所以它距离0号节点的距离为1；j号节点吃i号节点所以它距离i号节点的距离为1（同时它吃i号节点也意味着它被0号节点吃，因为三种动物构成闭合的食物链）所以它距离0号节点的距离为2；k号节点吃j号节点所以它距离j号节点的距离为1（同时它吃j号节点也意味着它和0号节点是同一类动物）所以它距离0号节点的距离为3；m号节点吃k号节点所以它距离k号节点的距离为1（同时它吃k号节点也意味着它吃0号节点）所以它距离0号节点的距离为4；后续还可以继续添加节点，，，

综上，我们可以看出一个节点与根节点的距离可以反映它与根节点之间的关系：

当该节点与根节点之间的距离模3为0的时候，代表该节点与根节点是同类；

当该节点与根节点之间的距离模3为1的时候，代表该节点吃根节点；

当该节点与根节点之间的距离模3为2的时候，代表该节点被根节点吃。

![1683186162171](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1683186162171.png)

##### （3）假如涉及到的两个节点代表的动物不在同一个集合中该怎么办呢？

前面我们已经说过了，当第一次涉及到两个之前从来没有过联系的动物的时候我们就认为该句话就是正确的，我们就可以按照该句话所表述的关系来合并这两个集合并建立这两个动物之间的关系，举例如下图所示：

①当要求表示1号节点吃4号节点时，我们得合并两个集合，问题就是现在这里的距离x得是多少才能表示1号节点吃4号节点呢？由前面我们已经知道了只要这里的1号节点到4号节点的距离模3余1就可以表示1号节点吃4号节点了：即 x%3==1**（即这里的x其实可以随便取值，只要它满足这个式子就可以，你可以取x=1、x=4、x=7、x=10、、、）**

![1683189092073](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1683189092073.png)



②当要求表示2号节点吃5号节点时，我们得合并两个集合，这里我们并不是直接拿2号节点去合并，还是拿2号节点所在的集合中的祖先节点（即1号节点）去合并，问题就是现在这里的距离x得是多少才能表示2号节点吃5号节点呢？由前面我们已经知道了只要这里的2号节点到4号节点的距离模3的余数比5号节点到4号节点的距离模3的余数大一个就可以表示2号节点吃5号节点了：即 （x+1）%3==1%3+1（这里为什么是x+1呢？因为我们要求的是2号节点到4号节点的距离，而x表示的是1号节点到4号节点的距离）

![1683189397038](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1683189397038.png)



③当要求表示2号节点被5号节点吃，我们得合并两个集合，这里我们并不是直接拿2号节点去合并，还是拿2号节点所在的集合中的祖先节点（即1号节点）去合并，问题就是现在这里的距离x得是多少才能表示2号节点被5号节点吃呢？由前面我们已经知道了只要这里的2号节点到4号节点的距离模3的余数比5号节点到4号节点的距离模3的余数小一个就可以表示2号节点被5号节点吃了：即 **（x+1）%3==1%3-1**（这里为什么是x+1呢？因为我们要求的是2号节点到4号节点的距离，而x表示的是1号节点到4号节点的距离）

##### notes：由上述式子我们可以看出这里的x有可能会取负数的

![1683190504437](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1683190504437.png)



本题大体上的知识介绍就是这样，不过还有很多的细节需要注意：

（1）注意下述的find方法所起到的作用，不仅是路径压缩，还得要更新dist数组（其中dist数组原本存储的是每个节点到其父亲节点的距离，后续在路径压缩的过程中就得更新为每个节点到其祖先节点的距离），那么要如何更新dist数组的数值为每个节点到其祖先节点的距离呢？这里就得用每个节点到其父亲节点的距离加上它的父亲节点到其祖先节点的距离，注意这里一定得保证是父亲节点到其祖先节点的距离（而不是父亲节点到它的父亲节点的距离），到底是如何保证的见下面的介绍：

![1683191583482](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1683191583482.png)

（2）注意当两个节点不在同一个集合中的时候我们需要合并两个集合，此时这两个节点执行find(x)方法寻找的是各自的祖先节点，然后当合并两个集合的时候与此同时我们还得更新dist数组，但是要注意此时更新dist数组的过程中因为集合合并已经完成了，所以此时再对原先的两个节点进行find(x)操作对应的已经不是原先的祖先节点了而是合并后的祖先节点

（3）还得注意这里的d[x]即x节点到其父亲节点的距离可能会是负数（距离为什么会是负数呢？可能是在合并两个集合的时候造成的后果，具体见上面notes部分介绍），而负数取模也是负数，所以为了避免这个问题我们又是如何处理的

![1683191994534](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1683191994534.png)

```Java
import java.util.*;
public class Main{
    static int N=50010;
    static int[] father=new int[N];//记录每个节点的父亲节点
    static int[] dist=new int[N];
    /*
    记录每个节点到父亲节点的距离，初始化时每个节点的父亲节点就是本身，所以距离都为0，
    后续当进行路径压缩的时候也会更新这个dist数组的数值将其更新为每个节点到祖宗节点的距离
    */
    static int find(int x){
        if(x!=father[x]){
            /*
            father[x]=find(father[x]);
            dist[x]+=dist[father[x]];
            这样写为什么不行呢？
            因为在这里的find函数有两个作用：一是路径压缩，二是更新dist数组的数值为每个节点
            到其祖宗节点的距离。
            那么是如何更新dist数组为每个节点到祖宗节点的距离的呢？
            就是利用每个节点到其父亲节点的距离加上父亲节点到其祖先节点的距离就更新为该节点
            到其祖先节点的距离，如果我们在执行更新dist数组之前先执行father[x]=find(father[x]);
            将每个节点的父亲节点更新为祖先节点的话，那么后续父亲节点就变成了祖先节点那么自然祖先节点
            到祖先节点的距离就变成了0。
            
            综上我们得出得先更新dist数组
            */
            
            /*
            dist[x]+=dist[father[x]];
            father[x]=find(father[x]);
            这样写又为什么不行呢，不是已经将更新dist数组的操作放在了前面吗？
            因为在这里的find函数有两个作用：一是路径压缩，二是更新dist数组的数值为每个节点
            到其祖宗节点的距离。
            那么是如何更新dist数组的呢？
            就是利用每个节点到其父亲节点的距离加上父亲节点到其祖先节点的距离就更新为该节点
            到其祖先节点的距离，但是注意这里有个问题那就是要加上父亲节点到其祖先节点的距离
            而我们的dist数组中一开始存储的都是每个节点到其父亲节点的距离，也就是说假如这里
            父亲节点对应的dist数组中的值没有更新的话那么这样写的结果就不对了。
            那么该如何保证父亲节点对应的dist数组中的值一定会更新呢？
            这里就得递归调用find(father[x])方法，这样就能够利用递归更新当前节点x的父亲节点
            对应的dist数组中的值。
            
            综上我们又得出不仅得先更新dist数组，而且在保证dist数组更新正常的情况下还得先
            递归调用find方法，所以我们有以下操作：
            */
            
            int u=find(father[x]);
            dist[x]+=dist[father[x]];
            father[x]=u;
        }
        return father[x];
    }
    
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        int n,k;
        n=sc.nextInt();
        k=sc.nextInt();
        for(int i=1;i<=n;i++){
            father[i]=i;
            dist[i]=0;
        }
        int count=0;//记录假话的数量
        int num=0;
        while(k>0){
            k--;
            int D,x,y;
            D=sc.nextInt();
            x=sc.nextInt();
            y=sc.nextInt();
            if(x>n || y>n){
                count++;
            }else{
                //这里在合并两个集合的时候一直用的是find(x)为什么不行？
                // if(D==1){
                //     if(find(x)==find(y) && (dist[x]-dist[y])%3!=0){
                //         count++;
                //     }else if(find(x)!=find(y)){
                //         father[find(x)]=find(y);
                //         dist[find(x)]=dist[y]-dist[x];
                //     }
                // }else{
                //     if(find(x)==find(y) && (dist[x]-dist[y]-1)%3!=0){
                //         count++;
                //     }else if(find(x)!=find(y)){
                //         father[find(x)]=find(y);
                //         dist[find(x)]=dist[y]+1-dist[x];
                //     }
                // }
                
                int px=find(x);
                int py=find(y);
                /*
                为啥这里一定得将find(x)换成px，find(y)换成py，否则报错呢？
                因为假如一开始两个节点x和y不在同一个集合中，此时的find(x)和find(y)各自指向
                各自的祖宗节点，然后我们得将这两个节点合并起来此时的find(x)和find(y)就不是
                指向之前的那个祖宗节点的了而是合并后的祖宗节点
                */
                if(D==1){
                    if(px==py && (dist[x]-dist[y])%3!=0){
                        /*
                        这里的其实原本的意思是说如果px==py那么代表x和y处于同一个集合中，
                        并且dist[x]%3==dist[y]%3代表x节点和y节点到根节点的距离对3取模
                        后的结果是相同的则代表x和y是同一类动物；但是注意这里的dist[x]可能取
                        负数，则负数取模为负数（原本二者是属于同一类动物的但是现在就因为距离
                        为负数导致取模后的结果不相同然后被判定成了二者不是同一类动物），为了
                        避免这种负数的影响所以我们采取移项到一边然后取模是否等于0的办法判断
                        二者是不是同类：即（dist[x]-dist[y]）%3==0这样就可以消除负数的影响
                        */
                        count++;
                    }else if(px!=py){
                        father[px]=py;
                        dist[px]=dist[y]-dist[x];
                    }
                }else{
                    if(px==py && (dist[x]-dist[y]-1)%3!=0){
                        count++;
                    }else if(px!=py){
                        father[px]=py;
                        dist[px]=dist[y]+1-dist[x];
                    }
                }
            }
        }
        System.out.println(count);
    }
}
```





## 10、堆

### 是什么：

堆（heap）是一种数据结构，它是一个**可以被看做一棵二叉树（而且这个二叉树是完全二叉树）的数组对象**。堆经常被用作实现优先队列、排序算法等，具有高效地插入、删除和查找最小（或最大）元素的特点。堆被分为大根堆与小根堆，大根堆要求每个节点的值都不大于其父节点的值，而小根堆要求每个节点的值都不小于其父节点的值。 

完全二叉树是指除了最后一层，其它层的节点都是满的，并且最后一层的节点都在左边连续排列。堆中的任何一层都不能有 "洞"，因为在堆的运行中，节点需要按照一定的规则来插入或删除。所以，堆是一种满足完全二叉树性质的特殊数据结构。 

```Java
public class Main {
    public static void swap(int i,int j){//这里的交换的仅仅是形参的值不会影响实参的值
        int temp=i;
        i=j;
        j=temp;
    }
    public static void main(String[] args){
        int[] a={0,1,2,3,4,5};
        System.out.println();
        int temp=a[1];
        a[1]=a[2];
        a[2]=temp;
        for(int i=0;i<6;i++){
            System.out.print(a[i]+" ");
        }
        System.out.println();
        swap(a[1],a[2]);
        for(int i=0;i<6;i++){
            System.out.print(a[i]+" ");
        }
    }
}

public class Main {
    static int[] a={0,1,2,3,4,5};
    public static void swap(int i,int j){//这里的交换的还是仅仅是形参的值不会影响实参的值
        int temp=i;
        i=j;
        j=temp;
    }
    public static void main(String[] args){
        System.out.println();
        int temp=a[1];
        a[1]=a[2];
        a[2]=temp;
        for(int i=0;i<6;i++){
            System.out.print(a[i]+" ");
        }
        System.out.println();
        swap(a[1],a[2]);
        for(int i=0;i<6;i++){
            System.out.print(a[i]+" ");
        }
    }
}

//下面的两个方法可以实现交换3个数组元素的功能吗
//第一个方法不能实现交换3个数组元素的功能，因为这里交换的仅仅是形参的值，真正的实参即ph和hp数组里面的
//元素值还是没有改变
public static void swap(int[] heap,int i,int j){
    int temp=heap[i];
    heap[i]=heap[j];
    heap[j]=temp;

    temp=ph[hp[i]];
    ph[hp[i]]=ph[hp[j]];
    ph[hp[j]]=temp;

    temp=hp[i];//hp[i]下标为i的点是第几个插入进来的
    hp[i]=hp[j];
    hp[j]=temp; 
}

//这里是可以实现交换3个数组的元素值的功能的
public static void swap(int[] heap,int[] hp,int[] ph,int i,int j){
        int temp=heap[i];
        heap[i]=heap[j];
        heap[j]=temp;
        
        temp=ph[hp[i]];
        ph[hp[i]]=ph[hp[j]];
        ph[hp[j]]=temp;
        
        temp=hp[i];//hp[i]下标为i的点是第几个插入进来的
        hp[i]=hp[j];
        hp[j]=temp;  
    }


//这个方法能不能实现模拟堆的功能？
import java.util.*;
public class Main{
    static int N=100010;
    static int[] heap=new int[N];
    static int[] ph=new int[N];//ph[k]用来存储第k个插入点在堆中对应的下标
    static int[] hp=new int[N];//hp[k]用来存储堆中下标为k的点是第几个插入进来的
    static int size=0;//记录堆中当前元素的下标
    static int m=0;//记录第几个插入的数
    
    public static void swap(int[] heap,int[] hp,int[] ph,int i,int j){
        int temp=heap[i];
        heap[i]=heap[j];
        heap[j]=temp;
        
        temp=ph[hp[i]];
        ph[hp[i]]=ph[hp[j]];
        ph[hp[j]]=temp;
        
        temp=hp[i];//hp[i]下标为i的点是第几个插入进来的
        hp[i]=hp[j];
        hp[j]=temp; 
    }
    public static void down(int x){//将x下标的元素沉下来
        int t=x;
        if(2*x<=size && heap[2*x]<heap[t]){
            t=2*x;
        }
        if(2*x+1<=size && heap[2*x+1]<heap[t]){
            t=2*x+1;
        }
        if(t!=x){
            swap(heap,hp,ph,t,x);
            down(t);
        }
    }
    public static void up(int x){//将x下标的元素浮上去
        int t=x;
        if(x/2>=1 && heap[x/2]>heap[t]){
            t=x/2;
            swap(heap,hp,ph,t,x);
            up(t);
        }
    }
    public static void insert(int x){//这里的x表示的是数值
        heap[++size]=x;
        ++m;
        ph[m]=size;
        hp[size]=m;
        up(size);
    }
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        int n;//对集合的n次操作
        n=sc.nextInt();
        while(n>0){
            n--;
            String s=sc.next();
            if(s.equals("I")){
                int x=sc.nextInt();
                insert(x);
            }else if(s.equals("PM")){
                System.out.println(heap[1]);
            }else if(s.equals("DM")){
                swap(heap,hp,ph,1,size);
                size--;
                down(1);
            }else if(s.equals("D")){//删除第k个插入的数
                int k=sc.nextInt();
                swap(heap,hp,ph,ph[k],size);
                size--;
                down(ph[k]);//这里的down()方法和up()方法二者只会执行一个
                up(ph[k]);
            }else{//将第k个插入的数的数值修改为x
                int k,x;
                k=sc.nextInt();
                x=sc.nextInt();
                heap[ph[k]]=x;
                down(ph[k]);
                up(ph[k]);
            }
        }
    }
}

```



### 怎么办：

#### 如何实现堆呢？

一般我们都是**用一个数组来实现堆，堆是一种完全二叉树结构**，所以我们用数组来实现堆还得体现它完全二叉树的这个性质，那么该如何体现二叉树的性质呢？其实无非就是确定每个节点它的左节点、右节点是谁的事情，这里我们令根节点的下标为1，这样的话它的左儿子的下标对应的就是2，右儿子的下标就是3，与之对应的后续下标为x的节点它的左儿子的下标就是2x，它的右儿子的下标就是2x+1，如此就确定了每个节点它的左儿子、右儿子分别是谁（同时也确定了每个节点它的父亲节点是谁）也就确定了二叉树这个结构，接下来就是在二叉树的基础上实现完全二叉树这个结构，这个就更好办了，只要我们按顺序添加节点编号中间不要留空就实现了，堆的示意图如下：

![1682520748138](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1682520748138.png)



#### 如何实现小根堆呢？

现在已经有了堆，下一步就是将现成的堆构建成小根堆，小根堆的意思就是父亲节点的值小于等于它的左儿子的值、小于等于它的右儿子的值，并且每个左儿子、右儿子分别又构成小根堆；根据上述概念，我们该如何构建小根堆呢？其实无非就是将每个父亲节点和它的左右儿子进行对比，然后将三者中最小者换到父亲节点的位置，接着对每个儿子节点继续进行上述操作，直到所有的节点都和左右儿子调整过，我们将这个过程称为下沉：down(x)，意为从下标x处的节点开始往它的左右儿子处进行下沉。上述过程图示如下：



#### notes：小根堆构建过程中的注意点

（1）在小根堆构建过程中父亲节点一定是和左右儿子中的较小者进行交换，否则没法构成小根堆（就拿下述第二次交换位置为例，为什么是拿下标1的数值4和下标2的数值1交换数值而不是拿下标1的数值4和下标3的数值3进行交换呢？因为虽然3比4小，但是将3换到父亲节点的位置上此时它的左儿子是1比父亲节点的值小，这样是不算小根堆的）

（2）注意当下标为x的节点进行下沉的时候，它可能会被换到x的左儿子或者右儿子的位置上，这时候原本x节点上的那个数的下沉就结束了吗？当然不是，因为当它被换到左儿子或右儿子的位置上以后作为新的父亲节点它可能比自己的左儿子或右儿子还要大，所以还得往下沉一直到满足小根堆的条件或者它已经没有儿子节点了

（3）注意当使用下沉方法的时候下沉节点的顺序一定是得从下往上的，举例来说就是下图中的必须先将儿子节点的堆调整成小根堆以后再去调整父亲节点。为什么呢？因为如果我们先调整父亲节点的话确实可以保证父亲节点的值是父亲节点本身、它的左儿子、它的右儿子三者中的最小者，然后我们顺势再去调整它的左右儿子也确实可以保证它的左右儿子都是小根堆，但是如果这时候左右儿子的堆中将一个很小的值调整了上来这时候就没法再将这个很小的数继续往上调整了，因为父亲节点已经调整过了；图示如下图：

![1682582899394](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1682582899394.png)

![1682585455183](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1682585455183.png)



#### notes：如何删除小根堆中的一个节点？

因为堆是一个用数组实现的完全二叉树结构，所以如果删除的是堆中的任何一个中间节点的话，那么我们如果删除这个节点后还得将后续的节点往前移动来堵住这个空缺，太麻烦了！有没有更好的办法呢？当然是有的，首先我们将数组最后一个元素的值来给这个要删除的数组元素赋值，接着将数组的最后一个元素删除，此时数组中就删除了一个元素了并且本来我们要删除的那个元素的值也被数组最后一个元素值给覆盖住了，这就相当于删除了我们要删除的那个元素，并且**由于删除的是数组的最后一个元素所以实现起来很简单，我们只要将指向数组最后一个元素的指针往前移动一个位置就可以了（注意这样删除数组的最后一个元素后该最后一个元素依然存在于数组中，只是我们将指针前移了一位这样当我们下次遍历堆的时候当下标超过该指针我们就知道堆下标越界了(但是此时数组下标并没有越界哦，所以你如果非要去用数组调用那个删除的元素的话还是可以调用的)，并且这样当下次插入新的元素的时候就可以将刚刚要删除的元素值给覆盖住了）**；但要注意到这里还没完，因为是小根堆，所以当我们将堆中最后一个元素把它的元素值放到中间节点后此时是不符合小根堆的结构的，所以我们得将该节点继续进行下沉操作



#### 构建小根堆的时间复杂度

上述这个往下沉的过程也有讲究，采取不同的方法会导致时间复杂度不同，现在已知该堆中总共有n个节点，因为是完全二叉树所以树的高度是logn，所以每个节点的平均下沉高度为 (logn)/2 ，总共有n个节点，所以如果采取从末尾节点到根节点的节点顺序往下沉的方式来调整小根堆的话时间复杂度为 O(n*logn)。

**但其实还有更好的方法，时间复杂度为 O(n)**，了解这点我们首先得知道完全二叉树的节点数目情况：

![1682653707383](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1682653707383.png)

设完全二叉树总共有n个节点，第1层有1个节点、第2层有2个节点、、、最后一层第k+1层有2^k个节点，则存在：2^0 + 2^1 + 2^2 + ...+ 2^(k-1) + 2^k=n，其中最后一层的节点数目为2^k个，前k层的总的节点数目为：

2^0 + 2^1 + 2^2 + ...+ 2^(k-1) =2^k-1，所以前k层的节点总数和第k+1层的节点数目二者相差无几（这里的k对于每一个整数都成立）。所以假设这个完全二叉树总共有n个节点的话，那么最后一层的节点数目为n/2（因为前k层与第k+1层的节点数目二者相等，各占一半），倒数第二层的节点数目为n/4（因为去除最后一层后总共有n/2个节点，所以此时的最后一层即原来的倒数第二层有n/4个节点），倒数第三层的节点数目为n/8，，，，我们进行下沉方法的时候，对于最后一层的节点是不用下沉的因为它的下面已经没有节点了，所以我们对于倒数第二层的节点开始下沉一直往上对于每个节点都进行一遍下沉操作，其中倒数第二层总共有n/4个节点这些节点最多下降1层（因为他们下面只有一层了），对于倒数第三层总共有n/8个节点这些节点最多下降2层，，，，所以对于除了最后一层外的n/2个节点来说，他们所需要进行的下沉操作次数是：

S=n/4 * 1 + n/8 * 2 + n/16 * 3 +...这是一个等差乘等比数列，处理后的结果为：S=n * [1-(1/2)^k]（这里的k为二叉树的高度），所以总的时间复杂度为：O(n)



### （838）堆排序

![1682174369002](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1682174369002.png)

解题思路：

注意构建小根堆过程中的几个注意点，同时我们采取从倒数第二层的节点开始进行下沉操作，一直往上进行下沉操作

```Java
//用数组实现小根堆
import java.util.*;
public class Main{
    static int N=100010;
    static int[] heap=new int[N];
    //用数组来实现小根堆，堆中的首元素下标从1开始（这样的话才满足x号节点它的左儿子
    //下标是2*x,它的右儿子下标是2*x+1），而且堆的话需要一个指针专门指向堆中的最后一个元素
    static int size;
    
    //定义交换数组中两个下标处的值的方法
    public static void swap(int[] heap,int i,int j){
        int temp=heap[i];
        heap[i]=heap[j];
        heap[j]=temp;
    }
    //利用已有的数组来创建小根堆
    public static void init(int[] heap){
        for(int i=size/2;i>=1;i--){//从倒数第二层的节点开始进行下沉操作
            down(i);
        }
    }
    //创建down()方法
    public static void down(int x){
        int t=x;//用t来记录下标x，x的左儿子，x的右儿子中三者中的最小值的下标
        if(2*x<=size && heap[2*x]<heap[t]){
            //如果x号节点的左儿子存在而且值比x号节点的值小则t指向x的左儿子
            t=2*x;
        }
        if(2*x+1<=size && heap[2*x+1]<heap[t]){
            //如果x号节点的右儿子存在而且值比x号节点的值小则t指向x的右儿子
            t=2*x+1;
        }
        if(t!=x){//最小值发生在x的左儿子或者右儿子中
            swap(heap,x,t);
            down(t);
        }
    }
    
    
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        int n,m;
        n=sc.nextInt();
        size=n;//记录堆中总共有多少个元素
        m=sc.nextInt();//输出数组中前m小的数
        for(int i=1;i<=n;i++){
            heap[i]=sc.nextInt();//现在这里的数组heap仅仅用来存储数据还没有实现小根堆的功能
        }
        
        //利用现有的heap[]数组创建小根堆
        init(heap);
        while(m>0){
            m--;
            System.out.print(heap[1]+" ");
            heap[1]=heap[size--];
            down(1);
        }
    }
}
```



#### 如何实现模拟堆呢？

模拟堆相较于一般的小根堆的区别在于模拟堆可以删除第k个插入的数，并且可以将第k个插入的数的数值修改为x。区别就是在模拟堆中我们可以方便的找到第k个插入的数是多少，这里有个问题：第k个插入的数它的下标就是k吗？当然不是，如果是普通的数组的话当然是如此，但是这里的是小根堆，**将数据插入数组以后还得进行调整所以它的下标位置是不确定的，所以我们得专门创建一个数组用来存储第k个插入的数它最后对应的数组下标是多少：ph[k]=i（意味着第k个插入堆中的数它对应的数组下标为i）**。

这样就完了吗？当然不是，因为当我们进行上浮或下沉操作的时候，我们要交换两个数组元素的数值：

swap(int i, int j)：其中的 i 和 j 代表数组元素的下标，假如说数组下标 i 的元素是第k1个插入堆中的数、数组下标 j 的元素是第k2个插入堆中的数，当我们交换数组下标 i 和 j 对应的元素数值后此时第k1个插入堆中的数的数组下标 变为 j 、第k2个插入堆中的数的数组下标则为 i ，但是**一开始的时候我们凭什么知道数组下标 i、j 的元素是第几个插入堆中的呢？所以这就需要我们另外再创建一个数组用来存储每一个数组下标的元素是第几个插入进来的：hp[k]=i（意味着数组下标为k的元素是第i个插入堆中的）**



#### 模拟堆问题的注意点：

注意点之一就在于，这里的堆中的元素是一边插入、删除，一边进行调整的，而上题的所有的数组元素一开始全部都是给定的，所以在上题堆排序过程中我们可以只写一个下沉方法down(int x)然后实现小根堆的操作，而这里我们因为是需要不断插入元素的，同时过程中还会要求输出当前堆的最小值等操作所以我们需要在不断插入删除元素的过程中维护当前这个小根堆，这就需要我们还得写一个将当前元素上浮的方法up(int x)。

为什么非得写一个上浮方法up()呢？上题没写不也实现了小根堆的操作吗？

对，其实不写up()方法也能实现小根堆，但是如果现在在堆末尾插入一个元素的话如果不写up()方法那么为了维护当前这个小根堆那么我们得和上题一样从倒数第二行的元素开始进行下沉操作一直向上进行到根节点的位置，时间复杂度为 O(n) ，这还仅仅是插入一个元素的话；但是当我们定义了up()方法以后，那么当插入一个元素的时候为了维护当前这个小根堆，我们对该元素进行上浮操作最多执行次数为 logn ，时间复杂度为 O(logn)。



#### notes：如何删除模拟堆中的一个节点

注意这里当要删除元素的时候是如何操作的，我们不能简单的用堆中最后一个元素的数值来覆盖我们要删除的那个元素了，因为模拟堆问题中还牵扯到得去计较第k个插入的元素对应的下标是多少以及堆中每个下标对应的是第几个插入的元素，所以这里我们的操作还得去交换ph数组和hp数组中两个元素的数值

这就完了吗？当然不是，因为当我们将要删除的元素与堆末尾的元素交换数值后，此时将堆末尾元素指针前移一位表示将那个元素从堆中删除出去了，但其实那个要删除的元素依然存在于数组中，只是当我们后续要插入元素的时候会将该元素覆盖掉，但是如果在插入一个新的元素之前我们利用数组去调用这个原本应该删除的元素还是可以调用的；并且如果我们对该元素进行上浮操作或下沉操作也是可以进行的，因为那个数依然存在于数组中啊

还有一个问题，当我们将堆尾元素和我们要删除的元素交换位置后，此时估计已经不是小根堆了，那么此时到底是对该元素进行下沉操作还是上浮操作呢？不用在意这个细节，我们既写上下沉方法down()、又写上上浮方法up()，省的再去判断，而且这两个方法肯定只会执行一个，为什么？因为这两个方法的方法体内部本身自己就会进行判断。

```Java
//现在交换数组中的两个数的元素值不仅得交换两个数的元素值还得交换ph和hp数组中两个数的值
/*
int[] heap;用来存储堆的数组
int[] ph;用来存储第k个插入堆中的元素对应的下标为i
int[] hp;用来存储堆中下标的为i的元素对应的是第k个插入堆中的
*/
public static void swap(int i,int j){
   int temp=heap[i];
    heap[i]=heap[j];
    heap[j]=temp;
    
    temp=ph[hp[i]];
    ph[hp[i]]=ph[hp[j]];
    ph[hp[j]]=temp;
    
    temp=hp[i];
    hp[i]=hp[j];
    hp[j]=temp;
}

public static void main(String[] args){
    /*
    注意这里的操作是错误的：
    因为将要删除的第k个数与堆末尾的元素交换位置后此时ph[k]指向堆末尾，然后我们又执行了
    将堆末尾指针前移的操作，此时原本要删除的元素依然存在于数组中，然后我们又执行了up(ph[k])
    操作将本该删除的元素又进行了上浮操作，造成没有删掉该元素。那么该怎么做呢？见下面：
    
    Scanner sc=new Scanner(System.in);
    int k=sc.nextInt();//表示要删除的是第k个插入堆中的元素
    swap(ph[k],size);
    size--;
    up(ph[k]);这时候的ph[k]已经是交换后的数组下标了，已经越过堆的下标界限了
    down(ph[k]);
    */
    
    //正确做法：一开始就找到第k个插入的数对应的数组下标，最后对该下标进行下沉或上浮操作
    Scanner sc=new Scanner(System.in);
    int k=sc.nextInt();
    k=ph[k];
    swap(k,size);
    size--;
    up(k);//这里的k依然是交换位置之前的那个下标
    down(k);
}
```



### （839）模拟堆

![1682586891543](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1682586891543.png)

解题思路：

一些重要的点上面都说过了。

```Java
import java.util.*;
public class Main{
    static int N=100010;
    static int[] heap=new int[N];
    static int[] ph=new int[N];//ph[k]用来存储第k个插入点在堆中对应的下标
    static int[] hp=new int[N];//hp[k]用来存储堆中下标为k的点是第几个插入进来的
    static int size=0;//记录堆中当前元素的下标
    static int m=0;//记录第几个插入的数
    
    public static void swap(int[] heap,int[] hp,int[] ph,int i,int j){
        int temp=heap[i];
        heap[i]=heap[j];
        heap[j]=temp;
        
        temp=ph[hp[i]];
        ph[hp[i]]=ph[hp[j]];
        ph[hp[j]]=temp;
        
        temp=hp[i];//hp[i]下标为i的点是第几个插入进来的
        hp[i]=hp[j];
        hp[j]=temp; 
    }
    
    public static void down(int x){//将x下标的元素沉下来
        int t=x;
        if(2*x<=size && heap[2*x]<heap[t]){
            t=2*x;
        }
        if(2*x+1<=size && heap[2*x+1]<heap[t]){
            t=2*x+1;
        }
        if(t!=x){
            swap(heap,hp,ph,t,x);
            down(t);
        }
    }
    
    public static void up(int x){//将x下标的元素浮上去
        int t=x;
        if(x/2>=1 && heap[x/2]>heap[t]){
            t=x/2;
            swap(heap,hp,ph,t,x);
            up(t);
        }
    }
    
    public static void insert(int x){//这里的x表示的是数值
        heap[++size]=x;
        ++m;
        ph[m]=size;
        hp[size]=m;
        up(size);
    }
    
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        int n;//对集合的n次操作
        n=sc.nextInt();
        while(n>0){
            n--;
            String s=sc.next();
            if(s.equals("I")){
                int x=sc.nextInt();
                insert(x);
            }else if(s.equals("PM")){
                System.out.println(heap[1]);
            }else if(s.equals("DM")){
                //heap[1]=heap[size--];这里不应该是简单的赋值，而是交换数组中二者下标对应的元素值
                swap(heap,hp,ph,1,size);
                size--;
                down(1);
            }else if(s.equals("D")){//删除第k个插入的数
                int k=sc.nextInt();
                k=ph[k];
                swap(heap,hp,ph,k,size);
                size--;
                down(k);
                up(k);
            }else{//将第k个插入的数的数值修改为x
                int k,x;
                k=sc.nextInt();
                k=ph[k];
                x=sc.nextInt();
                heap[k]=x;
                up(k);
                down(k);
            }
        }
    }
}
```





## 11、哈希表

### 是什么：

哈希表（Hash Table）是一种经典的数据结构，也称为散列表。哈希表存储键-值（key-value）对，它的核心思想是将key通过哈希函数转化为一个索引，然后将value存储在该索引处。

哈希函数通常是非常快速的函数，它将键映射到一个固定的索引上，这个索引通常是一个非负整数。在哈希表中，这个索引称为哈希值或散列值（Hash Value）。当我们要查找键对应的值时，先使用哈希函数计算出该键对应的哈希值，然后直接从哈希表中取出相应的值，这样我们就可以快速地完成查找操作。

在哈希表中，冲突是一个重要的问题。当两个或多个键被计算得出的哈希值相同时，就发生了冲突。哈希表通常使用开放寻址法（蹲坑法）或者拉链法（数组+链表）来解决冲突。

哈希表的优点是可以实现快速的查找、插入和删除操作，时间复杂度通常可以达到 O(1)。



#### notes：离散化是一种特殊的哈希方式



### 怎么办：

#### 哈希函数：

将想要查询的关键字（姓名、手机号等）映射成便于查找的东西的函数，一般而言一个好的哈希函数可以帮我们将一些关键字从一个大集合映射到小集合，然后我们将这些小集合的信息存储下来称为哈希表



#### 哈希函数怎么写？

直接对数据取模，用来取模的数得比数据范围大并且得是质数（减少冲突的概率）。

当采用开放寻址法的时候，我们开辟数组空间的大小得是数据范围的2~3倍，并且得是质数；当采用拉链法的时候开辟数组空间的大小就比数据范围大一点就行也得是质数。（具体参见下面的例题）

**注意这里的取模也有讲究：**

就拿下面的例子来说，**原本的数据范围为（ -10^9，10^9），我们想将数据映射到（0，10^5），如果我们直接拿它对 10^5 取模那么最后的结果中如果数据本身是负数则取模后的结果还是负数这不是我们想要的，所以我们可以在取模后的结果上再加上一个10^5，这样原本取模后为负数的结果现在肯定变成了正数，但是如果原本取模后就为正数的话这样再加上一个10^5不会太大了嘛？对，这样确实太大了，所以我们给取模后的结果加上10^5后再取一次模这样就可以了**



### （840）模拟散列表

![1682174437428](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1682174437428.png)

解题思路：

这题为什么会想到哈希表？

仔细看题目数据范围为 -10^9 ~ 10^9，然后从这么大的范围当中选取最多 10^5 个数进行插入或者询问，是不是从一个大的集合映射到一个小的集合，所以会想到哈希表（也可以联想到前面学过的离散化，毕竟离散化是一种特殊的哈希方式）

```Java
//开放寻址法（蹲坑法）实现哈希表
import java.util.*;
public class Main{
    static int N=200003;//采用开放寻址法时数组大小得是数据范围的2~3倍并且得是质数
    static int[] h=new int[N];
    static int nulls=0x3f3f3f3f;
    //这样变量名不能用null，因为null在Java中属于特有名词；为什么要设置这个见下面解释
    public static void insert(int x){
        int index=(x%N+N)%N;//哈希函数，用来求解关键字对应的下标（上面解释了为什么要这样取模）
        while(h[index]!=nulls && h[index]!=x){
            index++;
            if(index==N){
                index=0;
            }
        }
        h[index]=x;
    }
    public static int find(int x){
        int index=(x%N+N)%N;
        while(h[index]!=nulls && h[index]!=x){
            index++;
            if(index==N){
                index=0;
            }
        }
        return index;
    }

    public static void main(String[] args){
        for(int i=0;i<N;i++){
            h[i]=nulls;
            /*
            为什么要设置这一步？
            因为后续我们要判断h数组中每个元素是否等于要查询的值x，而这里x的范围为-10^9~10^9，
            所以我们初始的时候得将数组中每个元素设置成x取值范围之外的数，而这里的0x3f3f3f3f就是
            范围以外的数
            */
        }
        Scanner sc=new Scanner(System.in);
        int n;
        n=sc.nextInt();
        while(n>0){
            n--;
            String s=sc.next();
            int x=sc.nextInt();
            if(s.equals("I")){
                insert(x);
            }else{
                int k=find(x);
                if(h[k]==x){
                    System.out.println("Yes");
                }else{
                    System.out.println("No");
                }
            }
        }
    }
}


//拉链法（数组+链表）实现哈希表
import java.util.*;
public class Main{
    static int N=100003;//哈希函数要求取模的数得是质数，100003是大于100000的最小质数
    static int[] h=new int[N];
    //用来作为哈希表的数组，这里哈希表中存储的是对应元素的下标，
    //所以一开始初始化的时候都得指向-1（表示下标为空）
    /*
    其实仔细想一想这里作为哈希表的数组h里面的元素值不用指向-1而是默认的指向0也是可以的，为什么？
    因为这里我们向数组e中存储数据的时候都是从下标1开始存储的，所以下标0本身就是空的。
    这里注意要区分一下几个下标：一个是作为哈希表的数组h其中的下标对应元素的哈希值这里的下标
    是从0开始的；另一个是作为存储元素数值的数组e以及存放next指针的数组，这里的下标都是从1开始的
    */
    static int[] e=new int[N];//用来存放数值的数组
    static int[] next=new int[N];//用来表示next指针的数组
    static int idx=0;//指示e数组中当前元素的下标

    public static void insert(int x){
        int index=(x%N+N)%N;//首先通过哈希函数找到要插入的数值对应的数组下标
        ++idx;
        e[idx]=x;
        next[idx]=h[index];
        h[index]=idx;
    }
    public static boolean find(int x){
        int index=(x%N+N)%N;
        for(int i=h[index];i!=-1;i=next[i]){
            if(e[i]==x){
                return true;
            }
        }
        return false;
    }

    public static void main(String[] args){
        for(int i=0;i<N;i++){
            h[i]=-1;
        }
        Scanner sc=new Scanner(System.in);
        int n;
        n=sc.nextInt();
        while(n>0){
            n--;
            String s=sc.next();
            int x=sc.nextInt();
            if(s.equals("I")){
                insert(x);
            }else{
                if(find(x)){
                    System.out.println("Yes");
                }else{
                    System.out.println("No");
                }
            }
        }
    }
}
```



#### 字符串哈希

##### 是什么：

将字符串转换成对应的哈希值



##### 为什么：

可以用来快速判定两个字符串是否相等的问题，就不用再挨个字符的进行比较，而是直接计算两个字符串的哈希值，当哈希函数选的足够好的话我们认为不同的字符串会对应不同的哈希值不会产生冲突



##### 怎么办：

字符串如何转换成哈希值呢？

（1）将字符串看成是一个P进制的数，每个字符的ASCII码对应数的一位

（2）ASCII码的范围是0-127，所以这里P进制的P可以取128，但是经验上取P=131或13331冲突率低

（3）字符串很长对应的数太大，通过取模2^64（这也是个经验值）将数字映射到（0，2^64-1）（在C++中有一个无符号整型unsigned long long可以表示的范围为（0，2^64-1)，所以我们在C++中可以直接用unsigned long long类型的数据可以省去我们手动对2^64取模的过程；但是Java中没有无符号整型这个数据类型，只有long类型，long类型的取值范围为（-2^63，2^63-1），其中-2^63为Long.MIN_VALUE，2^63-1为Long.MAX_VALUE），所以这里我们换成对Long.MAX_VALUE取模（**这里还得注意一个问题，当我们由int型数据与long型数据进行运算以后得到的结果是一个long型数据，如果我们在这时候直接将运算结果赋给一个int型变量会造成精度丢失**）

![1683017754301](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1683017754301.png)



#### notes：当一个字符和一个数字进行加减乘除等运算后最后的结果是什么？

当一个字符与数字进行加减乘除等运算时，是将该字符对应的ASCII码与对应的数字进行加减乘除运算，最后的结果也是一个整数。



#### notes：当一个int型数据与long型数据运算后的结果是long型数据，如果将运算结果直接赋给int型变量会造成精度丢失



### （841）字符串哈希

![1683017250025](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1683017250025.png)

解题思路：

当我们要求一个子串对应的哈希值的时候最朴素的做法是什么？

就是将该子串取出来然后看成P进制的数，每一个字符都对应P进制上的一个位数然后就这样求解出该子串对应的哈希值；有没有更好的办法呢？当然是有的，类似于前面学过的前缀和数组，这里我们可以建立一个字符前缀的哈希数组，当要求字符串中下标 L-R 这段子串对应的哈希值的时候我们只要知道 1-R这段子串的哈希值以及1-(L-1)这段子串的哈希值，然后二者之间满足一定的关系就能求解出来 L-R这段子串对应的哈希值，具体关系见下图：

![1683031722718](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1683031722718.png)

```Java
import java.util.*;
public class Main{
    static int N=100010;
    static long[] h=new long[N];
    /*
    用来存放字符串前缀哈希值的数组（为什么定义为long型数组，因为后面要与long型数据进行运算，
    防止精度丢失）
    */
    static int P=131;//将字符串看成P进制的数中P的取值
    static long[] p=new long[N];
    //用来记录P的多少次方的数组（下标为k就是k次方，直接在数组中记录好，省的后面再算）
    static long Q=Long.MAX_VALUE;//用来取模的值
    
    //得到l-r这段子串的哈希值的方法
    static long get(int l,int r){
        return h[r]-h[l-1]*p[r-l+1];//
    }
    
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        int n,m;
        n=sc.nextInt();
        m=sc.nextInt();
        String s;
        s=sc.next();
        
        p[0]=1;
        for(int i=1;i<=n;i++){
            h[i]=(h[i-1]*P+s.charAt(i-1))%Q;
            /*
            这里为什么要用s.charAt(i-1)?
            因为字符串的下标是从0开始的，我们将字符串下标0对应的前缀哈希值存放在h数组中下标1的位置，
            将字符串下标1对应的前缀哈希值存放在h数组中下标2的位置，
            将字符串下标k对应的前缀哈希值存放在h数组中下标k+1的位置，，，
            */
            p[i]=p[i-1]*P;
        }
        
        
        while(m>0){
            m--;
            int l1,r1,l2,r2;
            l1=sc.nextInt();
            r1=sc.nextInt();
            l2=sc.nextInt();
            r2=sc.nextInt();
            if(get(l1,r1)==get(l2,r2)){
                System.out.println("Yes");
            }else{
                System.out.println("No");
            }
        }
    }
}
```





# 三、搜索和图论

## 1、DFS（深度优先搜索）

### 是什么：

深度优先搜索（DFS）是一种图遍历算法（树是一种特殊的图），在搜索过程中沿着一个路径尽可能深入地访问图中的结点，直到无法继续深入为止，然后回溯到前一个结点，寻找其他未访问的相邻结点。在实现上，DFS通常使用递归或栈来维护遍历过的结点序列。 

### notes：深度优先搜索的实现是基于回溯的思想（因此一般用回溯法的题也可以用深度优先搜索解决）



![1685512908617](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1685512908617.png)



### 为什么：

深度优先搜索可以解决以下一些问题：

（1）连通性问题：深度优先搜索可以用于判断一个无向图或有向图是否连通，或者得到无向图或有向图的各个连通分量。

（2）图的遍历问题：深度优先搜索可以用于遍历无向图或有向图的全部结点，或者找到从给定结点出发到达另一个给定结点的路径。

（3）拓扑排序问题：深度优先搜索可以用于拓扑排序，用于找到一个有向无环图中各个结点的合法顺序，使得每个结点都排在它的前驱结点之后。

（4）带权图的最短路径问题：可以利用深度优先搜索加上剪枝等技巧，得到带权图中某个结点到另一个结点的最短路径。



### notes：需要注意的是，深度优先搜索不一定能够找到图的最优解

因为它可能会沿着比其他路径更长的路径进行搜索。对于某些问题，广度优先搜索或其他算法可能更适合找到最优解。



### 怎么办：

### 数据结构上如何实现深度优先搜索呢？

在数据结构上深度优先搜索是使用栈来实现的，利用栈来存储从图的起点到终点的这条路径上的所有点，所以需要栈的大小为O(h)（其中h代表图的深度）



### 算法上如何实现深度优先搜索呢？

在算法上是利用for循环嵌套递归函数来实现深度优先搜索的（for循环嵌套递归函数的结构和回溯法的结构是一模一样的），其中for循环用于遍历搜索树当前层的所有可能情况，递归函数用于遍历搜索树下一层的情况。

```java
public void dfs(){
    if(搜索结束条件){
        搜索结束要进行的操作
    }
    for(遍历搜索树当前层的所有可能情况){
        对当前层的每一种情况进行的操作；
        dfs();//对搜索树下一层进行遍历
        恢复现场（取消上述对于当前层的操作）；
    }
}

/*
如何理解上述for循环中先是“对当前层的每一种情况进行的操作”，然后又是“取消上述对于当前层的操作”？
注意这两步操作不是紧挨着的，中间隔了一步对于下一层的递归。
就拿下述对于数组{1，2，3}的全排列来说：
首先在第一层搜索dfs(index=0)中假设选定第一个数是1，接着进行第二层搜索dfs(index=1)，假设选定第二个数是2，接着进行第三层搜索dfs(index=2)，只能选定第三个数是3（这时候得到第一个全排列{1，2，3}），这时候想要进行第四层搜索发现已经满足循环结束条件了所以回溯到第三层搜索dfs(index=2)中的“取消上述对于当前层的操作”即取消选定第三个数是3的操作，接着还没出第三层搜索的for循环接下来我们就要在for循环中看看第三个数还能有什么其他可能，但是因为前两个数已经被选取了，所以只剩下3可选，但是3已经取过一次了，所以对于第三个数已经没有其他可能了，所以第三层搜索就此结束我们退回到第二层搜索，首先就是第二层搜索的“取消上述对于当前层的操作”即取消选定第二个数是2的操作，这时候还没有出第二层搜索的for循环接着我们就要在for循环中看看第二个数是否还有其他可能，发现是有的，因为之前第三层搜索已经把选定第三个数是3的操作给取消了，刚刚又取消了选定第二个数是2的操作，所以现在第二个数可选的范围有两个即2和3，但是因为第二个数为2我们已经取过了，所以这次我们选取第二个数为3，接着继续进行第二层搜索的for循环中的dfs()函数即进行第三层的搜索看看第三个数可以取什么，这时候可选的数只有2了，所以我们取第三个数为2（得到第二个全排列{1，3，2}），后续都是类似的过程。
*/
```



#### 全排列的搜索树示意图：

![1678694274368](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1678694274368.png)



### （842）排列数字

![1684894513189](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1684894513189.png)

解题思路：

这题就是之前做过的典型的用回溯法解决的题目，现在用深度优先搜索来解决；其实思想都是一样的，都是回溯的思想，关键就在于如何实现回溯呢？

这里就是典型的模板：用for循环嵌套递归实现回溯，其中for循环用于遍历搜索树中当前层的所有情况，递归函数用于遍历搜索树下一层的情况；最后不要忘了一趟搜索完成后要记得恢复现场

```Java
import java.util.*;
public class Main{
    static int N=10,n;
    static int[] path=new int[N];//用于记录每一次数组全排列的情况
    static boolean[] state=new boolean[N];//用于记录每一次的全排列中每一个数字是否被使用过
    static void dfs(int u){//这里的u表示当前遍历的是全排列的第几个位置（u的取值范围为1~n）
        if(u>n){//dfs函数结束的条件(当遍历完全排列的n个位置以后dfs函数就结束了)
            for(int i=1;i<=n;i++){
                System.out.print(path[i]+" ");
            }
            System.out.println();
            return;
        }
        for(int i=1;i<=n;i++){//for循环用于遍历全排列过程中当前层的所有可能情况
            if(!state[i]){
                path[u]=i;
                state[i]=true;
                dfs(u+1);//递归函数用于遍历全排列过程中下一层的情况
                state[i]=false;//别忘了恢复现场
            }
        }
    }
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        n=sc.nextInt();
        dfs(1);
    }
}
```



### （843）n皇后问题

![1684899113911](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1684899113911.png)

解题思路：

#### 思路一：按照行顺序遍历来实现n皇后问题

（1）这里我们按照行的顺序先遍历第一行看看第一行有哪些位置可以放置皇后，在第一行放置完皇后以后遍历第二行看看第二行有哪些位置可以放置皇后（因为第一行已经放置了皇后所以她所在的列以及主对角线和次对角线就不能再放置皇后了），就这样n皇后问题的搜索树已经明朗了。

上述按照行顺序确实可以保证每一行只有一个皇后，但是仅仅如此没办法保证每一列、每一个斜线上只有一个皇后的条件啊，所以我们还需要用一个数组来记录每一列的皇后放置情况，当遍历到当前层的第j列的时候发现第j列已经放置了皇后那么当前层的第j列就不能放置皇后了；同理我们再用两个数组来记录棋盘上所有的主对角线和次对角线上的皇后放置情况，当遍历到当前层的第j列的时候发现第j列对应的主对角线上已经放置了皇后则当前层的第j列就不能再放置皇后了**（需要注意的是当数组长度为N时主对角线（次对角线）的数目为 ：N * 根号2 条，所以为了记录每一个主对角线我们需要开辟大小为2*N的数组）**

（2）这里还有一个问题，那就是所有的主对角线以及次对角线该如何表示？举例如下图所示：该如何表示这里的5条次对角线呢？

这里我们以红线为例：其中的（3，2）和（2，3）这两个点在同一个次对角线上，那么该怎么表示这两个点所在的对角线呢？

所有的次对角线所在的直线方程都是y=x+b的形式，**如果想表示在同一个次对角线上的点的话，我们知道肯定是满足y=x+b这个形式的，这里的区别就在于b的不同，不同的b代表了不同的直线，**如何表示不同的b呢？

y=x+b ---> b=y-x   就可以了所以我们可以根据横纵坐标的关系来判断两个点是否在同一条次对角线上，所以这里我们就可以用y-x的值作为下标来标注是哪一个次对角线，不过这里还有一个问题：那就是y-x的值有可能是负的，数组下标不能是负值，所以我们可以给y-x的值加上一个数组大小n即：b=y-x+n，这样就可以确保下标为正数，同时不会数组越界，同时又可以保证不同的斜线对应的b值是不同的，就此可以完整表示每一个次对角线（主对角线也是同理：b=y+x）

![1685524390131](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1685524390131.png)

（3）按照行顺序遍历棋盘的时候，我们是不需要考虑n个皇后放不满的问题的但是在按格子顺序遍历的时候必须得考虑，为什么？

因为这里我们按照行顺序遍历的时候在每一行我们都尽可能找位置放皇后，必须把皇后放好了我们才进入到下一行，所以当遍历到第n+1行的时候本身就意味着我们在前n行将n个皇后放置好了否则它不会来到第n+1行这个位置

```Java
import java.util.*;
public class Main{
    static int N=10,n;
    static char[][] g=new char[N][N];//用于记录棋盘上的棋子摆放情况
    /*
    这里如何实现每一行、每一列、每一个斜线上不存在两个皇后呢？
    这里我们可以按照行顺序依次遍历每一行以此来实现每一行只有一个皇后，但是每一列、每一个斜线呢？
    这里我们可以用一个数组来记录每一列上是否放置了皇后，如果该列已经放置了皇后的话就不能再放了；
    再用两个数组分别记录所有主对角线、次对角线上是否放置了皇后，需要注意的是当数组长度为N时
    主对角线（次对角线）的数目为：N * 根号2 条，所以为了记录每一个主对角线我们需要开辟大小为2*N的数组
    */
    static boolean[] col=new boolean[N];//用于记录每一列是否放置了皇后
    static boolean[] dg=new boolean[2*N];//用于记录每一个主对角线上是否放置了皇后
    static boolean[] udg=new boolean[2*N];//用于记录每一个次对角线上是否放置了皇后
    
    static void dfs(int u){//这里的u代表当前遍历的是棋盘的第几行（u的取值范围为：1-n）
        if(u>n){//搜索结束条件（当遍历完棋盘的第n行就结束搜索）
            for(int i=1;i<=n;i++){
                for(int j=1;j<=n;j++){
                    System.out.print(g[i][j]);
                }
                System.out.println();
            }
            System.out.println();
            return;
        }
        /*
        注意这里为什么我们不用判断n个皇后放不满的情况，但是在按照格子顺序的时候就得考虑呢？
        因为这里我们按照行顺序遍历的时候在每一行我们都尽可能找位置放皇后，必须把皇后放好了
        我们才进入到下一行，所以当遍历到第n+1行的时候本身就意味着我们在前n行将n个皇后放置好了
        否则它不会来到第n+1行这个位置的
        */
        
        
        for(int j=1;j<=n;j++){//for循环用于遍历棋盘当前层的所有摆放位置
            if(!col[j] && !dg[j+u] && !udg[j-u+n]){
            /*
            如果当前层的第j列没有皇后，并且第j列对应的主对角线上没有皇后，
            并且第j列对应的次对角线上没有皇后，那么皇后就可以摆放在当前层的第j列的位置上
            */  
                g[u][j]='Q';
                col[j]=dg[j+u]=udg[j-u+n]=true;
                dfs(u+1);
                g[u][j]='.';//不要忘了恢复现场
                col[j]=dg[j+u]=udg[j-u+n]=false;
            }
        }
    }
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        n=sc.nextInt();
        for(int i=1;i<=n;i++){
            for(int j=1;j<=n;j++){
                g[i][j]='.';
            }
        }
        dfs(1);
    }
}
```





#### 思路二：按照格子顺序遍历来实现n皇后问题

（1）相对于行顺序遍历天然就可以排除每一行有两个皇后这种情况按照格子顺序遍历棋盘我们需要额外再开一个数组用来存储每一行是否放置了皇后

（2）按照格子顺序遍历的话，那么是如何遍历的呢？

我们按照每一个格子的坐标（i，j）去遍历，当要遍历下一个格子的时候固定i不变执行j++操作就行了。

（3）当我们到达了一行的末尾的时候该怎么办呢？

我们可以加上一个判断：j>n吗？如果j>n那么就代表已经遍历到了该行的末尾，此时我们执行j=1，然后令i=i+1即跳转到下一行第一个位置即可

（4）关于数组越界的问题，注意这里的数据n最大可以取到9，如果我们将数组大小设置成10然后从数组下标1处开始存储元素，那么刚好数组下标最后一个位置存储了元素，但是当我们想遍历下一个格子从而执行j++操作的时候就产生了数组越界；所以最好一开始我们就将数组下标定大一点

（5）另外还有一点需要注意，那就是这里按照格子顺序遍历的时候我们得考虑n个皇后放不满的问题。第一种方法按照行顺序遍历棋盘的时候我们没有考虑皇后放不满的情况，为什么这里要考虑呢？不考虑行不行？

因为第一种按照行顺序的时候我们是在每一行都要找机会放皇后，如果当前行没有放置皇后就没法进入下一行，所以如果已经遍历到了n+1行那就代表所有的n个皇后在前n行都已经被放置好了；但是这里按照格子顺序遍历的时候我们并没有说每个格子尽可能都要放置皇后，而是每个格子有两种选择：放置皇后、不放皇后。所以即使当前的格子不放皇后，也可以遍历到下一个格子，即使极端情况下前n行的所有格子都不放皇后，还是可以遍历到第n+1行，所以按照格子顺序遍历的时候我们得考虑皇后没有放满的情况

```Java
//n皇后问题的第二种遍历顺序：按照一个格子一个格子的顺序遍历
import java.util.*;
public class Main{
    static int N=10,n;
    static char[][] g=new char[N+1][N+1];
    /*
    用于记录棋盘上的棋子摆放情况，为什么要把数组大小设置为N+1呢？
    因为设置成N的话会产生数组越界，因为这里最多存在n=9个元素，而我们将N设置为10，
    所以如果我们将数组大小设置成N的话数组也就刚刚好比元素数目大一个，假如我们
    数组是从下标1位置开始存储元素的话那么就会产生数组最后一个元素刚好压界的情况；
    而下面递归函数dfs(int i,int j,int s)当遍历到下一个节点dfs(i,j+1,s)的时候就会
    产生越界了
    */
    /*
    这里当采用一个格子一个格子的顺序进行遍历的时候如何保证棋盘上每一行、每一列、每一个斜线上
    只有一个皇后呢？
    前面当我们采用按行顺序遍历的时候可以确保每一行有且仅有一个皇后，但是现在按照格子的顺序
    就没法保证每一行只有一个皇后了，必须采取别的手段来确保这个条件，我们采取的就是用一个数组
    来存储每一行摆放皇后的情况
    至于每一列、每一个斜线上有且仅有一个皇后就还是和前面的做法一样用数组表示：
    这里我们可以用一个数组来记录每一列上是否放置了皇后，如果该列已经放置了皇后的话就不能再放了；
    再用两个数组分别记录所有主对角线、次对角线上是否放置了皇后，需要注意的是当数组长度为N时
    主对角线（次对角线）的数目为根号N条，所以为了记录每一个主对角线我们需要开辟大小为2*N的数组
    */
    static boolean[] row=new boolean[N+1];//用于记录每一行是否放置了皇后
    static boolean[] col=new boolean[N+1];//用于记录每一列是否放置了皇后
    static boolean[] dg=new boolean[2*N];//用于记录每一个主对角线上是否放置了皇后
    static boolean[] udg=new boolean[2*N];//用于记录每一个次对角线上是否放置了皇后

    static void dfs(int i,int j,int s){
        //这里的i代表的是当前遍历的格子是第i行，这里的j代表当前遍历的格子是第j列，
        //这里的s代表当前已经放置了s个皇后了
        /*
        这部分是搜索结束条件：
        */
        if(i>n){//递归结束条件一：当遍历完n行以后不管n个皇后的位置有没有摆放好都结束搜索了
          return; 
        }
        if(s==n){//递归结束条件二：在第n行已经放置完了所有的n个皇后
            for(int u=1;u<=n;u++){
                for(int v=1;v<=n;v++){
                    System.out.print(g[u][v]);
                    }
                    System.out.println();
                }
            System.out.println();
            return;
        }
        /*
        第一种方法按照行顺序遍历棋盘的时候我们没有考虑皇后放不满的情况，为什么这里要考虑呢？
        不考虑行不行？
        因为第一种按照行顺序的时候我们是在每一行都要找机会放皇后，如果当前行没有放置皇后就没法
        进入下一行，所以如果已经遍历到了n+1行那就代表所有的n个皇后在前n行都已经被放置好了；
        但是这里按照格子顺序遍历的时候我们并没有说每个格子尽可能都要放置皇后，而是每个格子
        有两种选择：放置皇后、不放皇后。所以即使当前的格子不放皇后，也可以遍历到下一个格子，
        即使极端情况下前n行的所有格子都不放皇后，还是可以遍历到第n+1行，所以按照格子顺序遍历
        的时候我们得考虑皇后没有放满的情况
        */


        /*  
        这部分是对于搜索树当前层的所有可能情况的遍历（相当于原来的for循环的位置）
        这里本来应该是用for循环对于搜索树当前层的所有可能情况进行遍历，
        但是这里的所有可能情况也就只有两种：当前格子要么放皇后，要么不放皇后；
        所以我们就省去了for循环，直接对当前层遍历这两种情况
        */

        //这里因为是按照格子的顺序进行遍历，所以当遍历完一行的所有格子后需要进行处理
        if(j>n){//采用按格子顺序遍历时，当遍历完一行以后从第二行开头的格子接着遍历
            j=1;
            i++;
        }
        //当前位置不放皇后(则我们接着看下一个位置是否放皇后)
        g[i][j]='.';
        dfs(i,j+1,s);

        /*
        当前位置放皇后(当前位置怎么样才能放皇后呢？当前位置所在行、所在列、所在主对角线、
        次对角线都没有皇后那么当前位置才可以放置皇后)
        */
        if(!row[i] && !col[j] && !dg[j+i] && !udg[j-i+n]){
           g[i][j]='Q';
           row[i]=col[j]=dg[j+i]=udg[j-i+n]=true;
           dfs(i,j+1,s+1);//当前位置放置皇后以后继续遍历下一个位置
           g[i][j]='.';//记得恢复现场
           row[i]=col[j]=dg[j+i]=udg[j-i+n]=false;
        }
    }
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        n=sc.nextInt();
        for(int i=1;i<=n;i++){
            for(int j=1;j<=n;j++){
                g[i][j]='.';
            }
        }
        dfs(1,1,0);
    }
}
```



## notes：树和图是如何存储的？

树是一种特殊的图，无环连通图，所以我们只要了解关于图的存储就行了。



### 图的分类：

（1）有向图：有向图指图中的边是有方向的，比如一条a-->b的边，则只能从a走向b不能反过来走

（2）无向图：无向图是指图中的边是没有方向的，这点该如何实现呢？其实我们只要弄两个反方向的边就可以了。比如一条a--b的边意味着既能从a走向b又能从b走向a，所以我们弄一条a-->b的边，再弄一条b-->a的边就行了

综上，我们只要弄清楚有向图的存储就行了。



### 如何存储有向图？

（1）方式一：邻接矩阵（不好）

用一个n * n的二维矩阵来存储两个点直接是否连通（不过这种方式的空间复杂度高，为O(n^2)，所以如果不是密集矩阵的话用这种方式不划算）（这里的n为点的个数）

（2）方式二：邻接表（好）

用n个单链表来存储有向图，每个单链表存储的都是与该节点直接相连的节点（这里的n为点的个数）



### （846）树的重心

![1685454535557](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1685454535557.png)

解题思路：

#### 树的重心

#### 是什么：

将树中每一个节点作为拆分点都能得到几个连通块，然后找到当前节点对应的其中最大的那个连通块，对每个节点都进行上述操作找到对应的最大的连通块，然后在这么多连通块中找到最小的那个，它所对应的节点就是树的重心（树的重心可能不止一个），举例如下图所示：

![1686819638966](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1686819638966.png)



#### 怎么办：

##### 如何计算树的重心呢？

利用dfs算法，对每个节点计算一次以它作为拆分点后得到的最大连通块的大小，然后所有节点对应的最大连通块的大小进行一次比较找出其中的最小值，它所对应的节点就是树的重心

##### 这里有一个问题，如何表示树？

树是一种特殊的图，所以我们用邻接表（邻接表就是多个单链表）来表示树；又因为这里的边是无向边，所以我们用两条反向的边来表示一条无向边。树的表示示意图如下：

![1686821281770](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1686821281770.png)



#### notes：注意这里的节点编号和节点序号的区别

这里总共有n个节点（编号1~n）这是不变的，但是因为这里的是无向图而我们又是用两条相反的有向边以此来表示一条无向边的，所以自始至终总共会表示2n个节点（节点序号1~2n）



#### notes：注意这里的邻接表中的元素存储的是节点序号（序号范围1~2n），而不是节点编号（编号范围1~n）

因为就拿上图中的h[2]和h[3]这两个单链表来说，对同一个1节点但是它们的next指针完全不同，所以知道h[]数组里面存储的是节点序号（因为是无向图所以每个节点会被存储两遍，所以会出现不同的节点序号对应同一个节点编号的情况）

```Java
import java.util.*;
public class Main{
    static int N=100010,n,idx;
    static int[] e=new int[2*N];
    /*
    这里的数组大小为什么是2*N呢？
    因为这里是用来表示各个节点序号的数组，每个节点会被表示两次所以数组大小为2N
    */
    
    static int[] ne=new int[2*N];
    /*
    用来表示各个节点的next指针，为什么需要next指针呢？
    因为单链表h[i]中仅仅指向的是与i相连的其中一个节点，就拿示例中的1号节点来说2、3、4号节点都与它相连，
    但是h[1]仅仅指向2，那么要如何找到3号、4号节点呢？
    就得利用next指针
    
    为什么这里的数组大小是2*N呢？
    因为这里要表示的是节点序号对应的next指针
    */
    
    static int[] h=new int[N];//用来表示邻接表（多个单链表）
    /*
    为什么这里的数组大小是N呢？
    因为我们只需要知道每个节点编号对应的单链表就可以了
    */
    
    
    static boolean[] st=new boolean[N];//记录在深度优先搜索中n个节点是否被遍历过
    /*
    为什么这里的数组大小是N呢？
    因为我们只需要对所有编号为1~n的节点进行一次遍历就可以了
    */
    
    static void add(int a,int b){//添加一条由a指向b的边（这里采用的是头插法）
        e[++idx]=b;
        ne[idx]=h[a];
        h[a]=idx;
    }
    
    static int key=N;//用于记录树的重心对应的最大连通块的节点数目
    /*
    注意这里不能写成：
    static int key=n;
    
    注意Java程序的执行顺序是先初始化静态属性再执行静态方法，所以这里我们是先
    初始化静态属性n的值然后将n的值赋给另一个静态属性key，然后再执行静态的main方法的，
    在main方法中重新给属性n赋值，但是此时已经没法改变属性key的值了
    因为这里的n在一开始的时候并没有赋值，静态变量n没有赋值的话默认值就是0，所以
    连带着这里的key也被赋值成了0，虽然后续我们在main方法中给n重新赋值，但是
    key因为已经被赋值过了，所以即使后续的n被重新赋值也没法改变key的大小了
    
    下例就是典型例子：
    public class Main{
        static int n;//这里的n为静态变量，没有初始化则默认值就是0
        static int key=n;//这里将key也赋值为0
        public static void main(String[] args){
            n=30;
            System.out.println(key);//输出的结果：key=0
        }
    }
    */
    
    
    /*
    这里的dfs(u)方法返回的是以u为根的子树中总的节点数目
    为什么dfs()方法会返回这样一个奇怪的东西呢？就让dfs(u)方法返回以u作为拆分点得到的最大的
    连通块的大小这样不行吗？
    不行，因为这里的u节点可能会是中间节点，那么以它作为拆分点的话除了它的各个子树是它的
    连通块以外还有一个连通块在它的头上（整个树中除去以节点u作为根节点的子树的部分），那么那一部分
    的大小该如何计算呢？不好计算，所以我们改变策略就让dfs(u)方法返回以u作为拆分点得到的子树中
    总的节点数目，这样的话则它的头上的连通块的大小就是n-子树的节点的数目
    */
    static int dfs(int u){//对数值为u的节点进行深度优先搜索
    /*
    注意这里没有用if语句显式的标注搜索结束条件，那么这里的方法啥时候结束呢？
    会不会是个死循环一直不结束呢？
    其实这里的for循环会遍历搜索树当前层的所有节点是否是重心，在此之前在for循环内部
    的递归函数dfs会遍历搜索树下一层的节点是否是重心；就这样执行顺序是先一直向下递归
    直到无法递归然后遍历当前层的所有节点，直到当前层所有节点被遍历完然后结束当前递归
    并回溯到上一层。
    综上这里的搜索结束条件就是将树结构的所有节点都遍历了一遍再结束
    */
        st[u]=true;
        int num=0;//表示以u为根的连通块中节点数目的最大值
        int sum=1;//表示以u为根的子树总的节点数目
        for(int i=h[u];i!=-1;i=ne[i]){//for循环用于遍历搜索树当前层的所有情况
            int j=e[i];//要将节点序号转为节点编号
            if(!st[j]){
                int s=dfs(j);//递归函数用于遍历搜索树下一层的情况
                num=Math.max(num,s);//对以u为根节点的多个子树的大小进行比较选出最大的一个子树大小
                sum+=s;//sum得加上以u为根的各个子树的大小
            }
        }
        num=Math.max(num,n-sum);
        /*
        注意这里以u为根的连通块中除了以u为根的子树是它的连通块以外还有
        它头上的那部分（整个树结构中除去以u为根的这棵子树后的结构本身
        也是一个连通块）
        */
        
        key=Math.min(key,num);
        return sum;
    }
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        n=sc.nextInt();
        for(int i=1;i<N;i++){
            h[i]=-1;
        }
        
        for(int i=0;i<n-1;i++){
            int a=sc.nextInt();
            int b=sc.nextInt();
            add(a,b);
            add(b,a);
        }
        dfs(1);
        System.out.println(key);
    }
}
```











## 2、BFS（宽度优先搜索）

### 是什么：

宽度优先搜索（BFS）是一种图遍历算法（树是一种特殊的图），在搜索过程中按层次逐个访问与起始节点相邻的结点，即首先访问起始节点，然后访问与其距离为1的节点，然后是距离为2的节点，以此类推，直到遍历完所有与起始节点相连通的结点为止，每个节点只被访问一次。



### 为什么：

宽度优先搜索（BFS）可以解决以下一些问题：

（1）最短路径问题：BFS可以用于求解图中两个结点之间的最短路径，因为它能够按层次逐个遍历与起始节点相邻的结点，故能够获取起始节点到其他节点的所有不同路径，其中第一条到达目标节点的路径即为最短路径。

（2）最小生成树问题：BFS可以用于求解无向带权图的最小生成树，这时候可以将BFS队列约束为只存储与当前已访问节点集合距离为1的节点，从而逐层递进地发现与已访问节点集合距离为2的节点，以此类推。

（3）双向BFS问题：对于某些问题，可采用“双向BFS”的方式，即从起始节点和目标节点同时开始BFS遍历，直到两个遍历过程相遇。这种算法通常比单向BFS要快得多。

### notes：BFS只适用于无权图或非负权图的最短路径问题



### 怎么办：

### 数据结构上如何实现宽度优先搜索呢？

在数据结构上是利用队列来实现宽度优先搜索的，将图中位于同一层的所有节点入队，所以所需队列的大小就是O(2^h)（其中假设该图是二叉树，h为二叉树的高度）



### 算法上如何实现宽度优先搜索？

在算法上也是利用队列来实现宽度优先搜索的

```Java
//宽度优先搜索模板
queue<--初始状态  //将初始状态入队
while(queue 不空){
    取队头;
    扩展队头;
}
```



### （844）走迷宫

![1685454443927](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1685454443927.png)

解题思路：

这题就是典型的宽度优先搜索的题目，每一次从当前点可以同时向上下左右四个方向移动，这四个移动后的点就是宽度优先搜索树中位于同一层的点，即先扩展距离当前点距离为1的点（它上下左右的四个点），再扩展距离当前点距离为2的点（即距离上一步扩展的点距离为1的点，注意每个点只能被访问一次，要不然同一个点经过不同的路径导致距离不同就不好了，这里利用宽度优先搜索我们得到的是每个点的最短路径）

（1）注意这里的迷宫问题，它什么时候会停止呢？

当遍历完迷宫中所有的节点以后就结束了。

（2）那么访问的最后一个节点一定是我们要访问的目的点即右下角的那个点吗？

当然不是，这取决于迷宫的具体构造，但是不管怎样，最终当搜索结束后，我们一定可以得到迷宫中所有点的距离，然后我们选择输出我们想要的目的点的距离就好了

```Java
/*
如何实现宽度优先搜索呢？
用队列实现，所以这里我们要创建一个队列用来存储各个节点，但是又没有现成的类可以表示节点
所以我们还得另外创建一个节点类
*/

import java.util.*;

//定义的节点类（里面的两个属性分别代表点的横纵坐标）
class Pair{
    int first;
    int second;
    public Pair(int first,int second){
        this.first=first;
        this.second=second;
    }
}
public class Main{
    
    static int N=110,n,m;
    //创建的节点队列（这里用数组来实现队列，这里最多只有n*m个节点入队）
    static Pair[] q=new Pair[N*N];
    static int head=1,tail=0;//队首指针、队尾指针
    static int[][] g=new int[N][N];//用来存储迷宫的二维数组
    
    /*
    用来存储迷宫中每个点到起点的距离，这里的距离不是直线距离，而是采用宽度优先
    搜索时的路径长度（初始时设置每个点的距离都是-1）
    */
    static int[][] dist=new int[N][N];
    
    /*
    定义节点上下左右移动时的坐标向量变化情况，比如当向上移动时横坐标不变纵坐标减1，
    当向下移动时横坐标不变纵坐标加1（这里以水平方向向右为x轴正向，竖直方向向下为y轴正向）
    */
    static int[] dx={0,0,-1,1};
    static int[] dy={-1,1,0,0};
    
    static void bfs(){
        while(head<=tail){//当队列不空时
            Pair t=q[head++];//取队头
            for(int i=0;i<4;i++){
                int x=t.first+dx[i];//队头元素所对应的位置向上下左右移动
                int y=t.second+dy[i];
                /*
                如果队头元素向上下左右移动后没有出界，而且移动后的位置不是墙可以走，
                而且之前没有走过，就可以走过去
                */
                if(x>=1 && x<=n && y>=1 && y<=m && g[x][y]==0 && dist[x][y]==-1){
                    dist[x][y]=dist[t.first][t.second]+1;
                    /*
                    队头元素移动一个位置到达现在的位置所以现在的位置距离出发点的距离
                    就是队头元素的距离加1
                    */
                    q[++tail]=new Pair(x,y);//扩展队列
                }
            }
        }
        System.out.println(dist[n][m]);
    }
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        n=sc.nextInt();
        m=sc.nextInt();
        //进行一些初始化的工作
        for(int i=1;i<=n;i++){
            for(int j=1;j<=m;j++){
                dist[i][j]=-1;
            }
        }
        dist[1][1]=0;
        q[++tail]=new Pair(1,1);
        
        for(int i=1;i<=n;i++){
            for(int j=1;j<=m;j++){
                g[i][j]=sc.nextInt();
            }
        }
        bfs();
    } 
}
```



### （845）八数码

![1687352596794](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1687352596794.png)

解题思路：

是什么：

求3*3的矩阵从一个状态变换到另一个状态最少需要经过多少步变换



怎么办：

（1）这是一道典型的BFS的题目，关键就是如何将题目的意思表示成BFS的形式？

矩阵的每一个状态在这里都相当于一个节点，我们要寻找搜索树中从初始状态的节点到目标状态的节点之间的最短路径，由此就可以将本题转换成最短路径问题。

（2）如何表示每一个状态的3*3矩阵？

矩阵的每一个状态它的各行各列都对应唯一的数字和字母x，所以我们可以将3*3的矩阵对应的全部元素按照行优先顺序展开成一个字符串，字符串和各个状态的矩阵之间一一对应，由此我们就可以用一个字符串节点表示出来各个状态的矩阵，举例如下：

![1687353398446](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1687353398446.png)

（3）搜索树中的节点之间有什么关系？

以当前节点为例，与当前节点直接相连的节点都是可以由当前节点直接变换得来的（在3*3的矩阵中字符x与上下左右位置处的字符交换位置得到的新的节点）

![1687354276165](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1687354276165.png)

（4）如何存储搜索树中每一个节点距离初始状态对应的节点的距离？

用哈希表存储，其中哈希表的key值用来存储字符串，value值用来存储对应字符串和初始节点之间的距离



#### notes：本题还有一些问题没有解决，那就是本题为什么会出现空指针异常的问题，这个问题涉及到Java中字符串的存储位置等内容，但是已经不是算法的内容了，所以现在就先放着，以后再看。

```Java
import java.util.*;
public class Main{
    //宽度优先搜索中用到的队列
    static Queue<String> q=new LinkedList<>();
    /*
    用于存储每个节点与初始节点距离的哈希表（哈希表的key值表示每个节点，
    value值表示每个节点与起点的距离）
    */
    static HashMap<String,Integer> map=new HashMap<>();
    
    /*
    定义交换字符串s中i,j位置处字符的方法(Java中的字符串是不可变的没法通过
    改变字符串的字符实现对字符串的重新赋值操作，我们可以通过对字符数组的重新
    赋值然后将字符数组转换为字符串实现同样的目的)
    */
    static String swap(String s,int i,int j){
        char[] chars=s.toCharArray();
        char tmp=chars[i];
        chars[i]=chars[j];
        chars[j]=tmp;
        // s=chars.toString();//这里会产生空指针异常
        s=new String(chars);
        return s;
        /*
        这里必须得返回字符串s否则出现空指针异常，为什么？
        这里应该牵扯到Java中字符串的存储位置
        */
    }
    //定义节点上下左右移动时的坐标变换情况（向下为x轴正向，向右为y轴正向）
    static int[] dx={-1,1,0,0};
    static int[] dy={0,0,-1,1};
    //定义宽度优先搜索的方法
    static int bfs(String start){
        String end="12345678x";//定义目标节点
        
        q.add(start);//将初始情况入队
        map.put(start,0);
        while(!q.isEmpty()){//当队列不空的时候
            String s=q.poll();//取队头
            int pos=s.indexOf("x");//返回字符串s中字符x的位置
            int x=pos/3,y=pos%3;//返回字符串s中字符x在3*3矩阵中的下标
            for(int i=0;i<4;i++){
                int a=x+dx[i];//返回3*3矩阵中字符x上下左右位置处的下标
                int b=y+dy[i];
                if(a>=0&&a<=2 && b>=0&&b<=2){//3*3矩阵中字符x上下左右位置处的下标存在
                   int distance=map.get(s);
                   int pos2=a*3+b;//将3*3矩阵中的下标换算成一维字符串中的下标
                   s=swap(s,pos,pos2);//将字符x与上下左右位置处的字符交换位置
                   //(这里必须得将s重新赋值，否则会出现空指针异常，为什么？)
                   if(!map.containsKey(s)){//
                       map.put(s,distance+1);
                       q.add(s);
                   }
                   s=swap(s,pos,pos2);//别忘了将字符x换回来
                }
            }
        }
        if(map.containsKey(end)){
            return map.get(end);
        }else{
            return -1;//也可能存在没法将矩阵还原成目标的样式
        }
    }
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        char[] chars=new char[9];
        for(int i=0;i<9;i++){
            chars[i]=sc.next().charAt(0);
        }
        String start=new String(chars);
        System.out.println(bfs(start));
    }
}
```





### （847）图中点的层次

![1685459218664](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1685459218664.png)

解题思路：

因为所有边的长度都是1，且又是要求最短距离，所以这又是一道典型的宽度优先搜索的题目，就按照宽度优先搜索的模板来做就行了

```Java
import java.util.*;
public class Main{
    static int N=100010,n,m,idx,head=1,tail=0;
    static int[] e=new int[N];//这里存储的是各个节点序号对应的节点编号
    static int[] ne=new int[N];//这里存储的是各个节点序号的next指针
    static int[] h=new int[N];//邻接表中的元素是节点的序号不是编号
    static int[] queue=new int[N];//队列中的元素是节点的编号不是序号
    static int[] dist=new int[N];//用于记录每个节点序号距离1号节点的最短距离（初始都为-1）

    static void add(int a,int b){
        e[++idx]=b;
        ne[idx]=h[a];
        h[a]=idx;
    }

    static int bfs(){
        queue[++tail]=1;//将初始情况入队（编号为1的节点入队）
        while(head<=tail){//当队列不空的时候
            int i=queue[head++];//取队头
            for(int j=h[i];j!=-1;j=ne[j]){//扩展队头
               int k=e[j];//这里h[]数组中存储的元素是节点序号，而dist数组中存储的是节点编号，
                          //所以需要将节点序号转化为节点编号
               if(dist[k]==-1){
                   dist[k]=dist[i]+1;
                   queue[++tail]=k;
               } 
            }
        }
        return dist[n];
    }
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        n=sc.nextInt();
        m=sc.nextInt();
        //初始一些条件设置
        for(int i=1;i<=n;i++){
            dist[i]=-1;
            h[i]=-1;
        }
        dist[1]=0;

        for(int i=1;i<=m;i++){
            int a,b;
            a=sc.nextInt();
            b=sc.nextInt();
            add(a,b);
        }
        System.out.println(bfs());
    }
}
```





### （848）有向图的拓扑序列

![1685459279502](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1685459279502.png)

解题思路：

拓扑排序的本质就是宽度优先搜索，每一轮搜索都会将入度为0的顶点入队，然后与它们相连的顶点的入度就减少1，就这样每一轮搜索都是将入度为0的顶点入队

![1686916609468](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1686916609468.png)

```Java
/*
拓扑排序本质就是bfs，每一轮宽度优先搜索都会将入度为0的顶点入队，
然后与它们相连的顶点的入度就减少1，就这样每一轮搜索都是将入度
为0的顶点入队
*/
import java.util.*;
public class Main{
    static int N=100010,n,m,idx,head=1,tail=0;
    static int[] e=new int[N];//用于存储各个节点序号对应的编号
    static int[] ne=new int[N];//用于存储各个节点序号的next指针
    static int[] h=new int[N];//邻接表
    static int[] queue=new int[N];
    static int[] d=new int[N];//用于存储各个编号的顶点的入度
    
    static void add(int a,int b){//增加一条由a指向b的边，由此b的入度加1
        e[++idx]=b;
        ne[idx]=h[a];
        h[a]=idx;
        d[b]++;
    }
    static boolean topsort(){
        for(int i=1;i<=n;i++){//将初始情况入队（即将所有入度为0的点入队）
            if(d[i]==0){
                queue[++tail]=i;
            }
        }
        while(head<=tail){//当队列不空时
            int i=queue[head++];//取队头
            /*
            这里出队的过程仅仅只有队头指针的移动，元素依然存在于数组中
            */
            
            for(int j=h[i];j!=-1;j=ne[j]){//扩展队头
                int k=e[j];
                //这里的h[]数组中存储的是节点的序号而d[]数组中存储的
                //是节点的编号，所以得将节点序号转变为编号
                
                d[k]--;
                if(d[k]==0){
                    queue[++tail]=k;
                }
            }
        }
        if(tail==n){
            //如果图中没有环的话那么所有顶点都会入队，否则会有顶点没有入队的，
            //此时不存在拓扑序列
            return true;
        }
        return false;
    }
    
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        n=sc.nextInt();
        m=sc.nextInt();
        for(int i=1;i<=n;i++){
            h[i]=-1;
        }
        for(int i=1;i<=m;i++){
            int x,y;
            x=sc.nextInt();
            y=sc.nextInt();
            add(x,y);
        }
        if(topsort()){
            for(int i=1;i<=n;i++){
                //上述元素出队的过程中只有队头指针的移动，元素依然存在于数组之中
                System.out.print(queue[i]+" ");
            }
        }else{
            System.out.println(-1);
        }
    }
}
```



## 3、Dijkstra算法（用于求单源最短路且无负权边的问题）

### note：0x3f3f3f3f是什么

是一个十六进制数，代表的是一个特定的数值。在计算机编程中，它通常用作一个较大的正整数或无穷大的值。在一些算法中，特别是与图论相关的算法中，它可以表示无穷大或无效值。 



### 是什么：

用于求解单源最短路径的算法即求解某一个节点到其余所有节点的最短路径的算法



### 怎么办：

#### dijkstra算法的基本步骤

```Java
//dijkstra算法的常见应用场景
//问题描述：求1号节点到其余所有节点的最短路径

//dijkstra()算法的基本步骤
/*
集合s是将当前已经确定找到距离1号节点最短路径的节点放入该集合
*/
static int dijkstra(){
   	//（1）初始化距离
    dist[i]=+∞;
    dist[1]=0;

    //（2）进行n次迭代，每次将集合s以外的距离1号节点最近的节点t加入集合，并且用节点t更新其他节点的距离
    for i:1~n
        t<-不在s中的距离最近的点
        s<-t
        用t更新其他节点的距离 
}

```



#### 这里如何表示集合s呢？

我们得明确这里的集合s是什么？是我们已经确定了该节点距离1号节点的最短路径的节点就将它加入集合s中，所以我们完全可以用一个boolean[]类型的数组来充当这里的集合s，如果我们已经明确了当前节点距离1号节点的最短路径那么就将当前节点对应的boolean[]类型的数组元素值置为true表示将当前节点加入集合s中



#### 这里的图存在重边该如何处理？

重边是什么？重边指的是两个节点之间存在多条边，如下图所示，面对这种情况对于两点之间的距离我们只要选取两个点之间最短的那条边就好

![1688477678942](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1688477678942.png)



#### 这里的图存在自环该如何处理？

自环是什么？指的是某个点有一条自己指向自己的边，如下图所示，对于自环我们不需要任何处理，为什么？

因为假设我们现在从1号节点开始依次寻找最短边，先找到节点i后找到节点j，节点j存在自环，那么来回顾一下dijkstra算法的求解步骤：

（1）首先在所有节点中寻找距离1号节点最近的节点，找到就是1号节点本身（距离1号节点距离为0），将1号节点加入集合s，用1号节点更新其他节点到1号节点的距离

（2）接着在剩下的节点中寻找距离1号节点最近的那个节点，假设找到的就是节点i，将i号节点加入集合s，用i号节点更新其他节点到1号节点的距离

（3）接着在剩下的节点中寻找距离1号节点最近的那个节点，假设找到的就是节点j，将j号节点加入集合s，用j号节点更新其他节点到1号节点的距离（dist[k]=Math.min(dist[k]，dist[j]+g[j] [k])，**也只有在这一步才会用上节点j的出度，但是用上的也是j号节点指向k号节点的边的长度而不是j号节点指向自身的那条边的长度**）（这里的k泛指所有其他非j号节点，g[j] [k]就是节点j指向节点k的边的长度）

![1688477966906](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1688477966906.png)





### （849）Dijkstra求最短路I（朴素算法）

![1687358558955](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1687358558955.png)

解题思路：

注意这里的图是一个稠密图（m~n^2），所以对于稠密图我们使用邻接矩阵来存储，同时使用朴素版dijkstra算法来进行求解

```Java
import java.util.*;
public class Main{
    static int N=510,n,m,max=0x3f3f3f3f;//0x3f3f3f3f用于表示无穷大的值
    static int[][] g=new int[N][N];//用邻接矩阵来表示图（对于稠密图用邻接矩阵比邻接表更好）
    static int[] dist=new int[N];
    //记录各个点距离初始点(也就是1号点)的最短距离，数组元素初始化为无穷大
    static boolean[] st=new boolean[N];//用于记录每个点是否找到距离初始点的最短距离(充当集合s)
    
    static int dijkstra(){
        //（1）初始化距离
        Arrays.fill(dist,max);
        dist[1]=0;
        
        //（2）进行n次迭代，每次将一个不在集合s中的距离初始点最近的点加入集合
        for(int i=1;i<=n;i++){//这里的i表示循环的次数
            int t=-1;//这里的t表示每次找到的不在集合中且距离初始点最近的那个点
            for(int j=1;j<=n;j++){//这里的j表示节点编号，每次都要在这些节点当中寻找满足要求的的节点
                if(!st[j] && (t==-1 || dist[t]>dist[j])){
                    t=j;
                }
            }
            st[t]=true;//将节点t加入集合s中
            for(int k=1;k<=n;k++){//用节点t更新其他节点的距离
                dist[k]=Math.min(dist[k],dist[t]+g[t][k]);
            }
        }
        if(dist[n]!=max){
            return dist[n];
        }else{
            return -1;
        }
    } 
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        n=sc.nextInt();
        m=sc.nextInt();
        
        //一些初始情况的设定：将邻接矩阵中的边值设定为无穷大
        for(int i=1;i<N;i++){
            Arrays.fill(g[i],max);
        }
        
        for(int i=1;i<=m;i++){
            int x,y,z;
            x=sc.nextInt();
            y=sc.nextInt();
            z=sc.nextInt();
            g[x][y]=Math.min(g[x][y],z);
            //因为图中可能存在重边，那么此时两点之间的距离就是重边中的最小值
        }
        System.out.println(dijkstra());
    }
}
```



### （850）Dijkstra求最短路II（堆优化算法）

![1687527120616](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1687527120616.png)

解题思路：

#### 是什么：

在朴素版dijkstra算法的基础上加入了小根堆来进行优化从而提高了算法的效率



#### 为什么：

当边的数目达到10^5级别的时候如果再用朴素版dijkstra算法那么算法的时间复杂度就会是10^10级别造成超时，所以得用堆优化版dijkstra算法



#### 怎么办：

（1）为什么堆优化dijkstra算法可以提高算法效率？

因为朴素dijkstra算法需要经过n次循环，每次需要在n个点中找到不在集合s中的且距离1号节点距离最近的点，时间复杂度为O(n^2)；而堆优化dijkstra算法将所有节点放入小根堆中然后每次从小根堆中取出不在集合s中且距离1号节点最近的点就行了，时间复杂度为O(nlogn)即构建小根堆的时间复杂度

（2）本题的图是稀疏图，所以用邻接表来表示这里的图，但是这里的各个边的权值该如何表示呢？

重新令一个数组w[]来存储各个边的权值即可

（3）这里的小根堆里装的是什么？

是节点到1号节点的距离还是节点的编号呢？

答案是两者都得装，装距离是因为小根堆中需要用到距离进行排序，装编号是因为需要知道每个距离对应的是哪个编号的节点；所以我们得创建一个类，将节点距离1号节点的距离以及该节点的编号作为属性存储在该类中

（4）这里存在重边和自环有影响吗？

没有影响，因为所有的重边都被加入到小根堆中，小根堆会进行排序筛选出距离最近的那个，对于自环也是同样的道理。

这里为什么不人为对重边进行处理一下呢？就像在朴素算法中那样，对于重边人为进行一次筛选，选出距离最短的那条边？

一是没法做到，因为朴素算法中用的是邻接矩阵存储图，其中二维数组g[i] [j]专门用来存储由节点i指向节点j的边的权值；但是对于本题用邻接表来存储图缺乏一个专门用来存储由节点i指向节点j的边的权值的结构，虽然我们可以创建一个数组w[]来存储每条边的权值但是它存储的是边的序号对应的权值（比如第一条边的权值w[1]），它并没有指定每条边的起点和终点，所以可能上一秒还在说由节点i指向节点j的边，下一秒就在说由节点j指向节点k的边，然后又在说由节点i指向节点j的边，总而言之你并不知道由节点i指向节点j的边存储在哪里

二是没有必要去做，因为一股脑将所有的重边加入小根堆，小根堆会进行排序筛选出距离最近的那个点，不过这样也确实会造成小根堆中存储数据冗余

```Java
import java.util.*;
//定义一个类Pair来存储节点编号以及对应的距离初始节点的最短距离
class Pair implements Comparable<Pair>{
    public int first;//表示当前节点距离初始节点的最短距离
    public int second;//表示当前节点的编号
    
    public Pair(int first,int second){
        this.first=first;
        this.second=second;
    }
    @Override
    public int compareTo(Pair o){
        return Integer.compare(first,o.first);
    }
}
public class Main{
    static int N=150010,n,m,idx,max=0x3f3f3f3f;
    static int[] h=new int[N];//用来表示邻接表
    static int[] e=new int[N];//用来表示节点序号对应的节点编号
    static int[] ne=new int[N];//用来表示节点的next指针
    static int[] w=new int[N];//用来表示每个边的权值
    static int[] dist=new int[N];//用来记录每个点距离初始节点的最短距离
    static boolean[] st=new boolean[N];//用来记录每个点是否找到距离初始节点的最短距离
    
    static void add(int a,int b,int c){//定义一条由节点编号a指向节点编号b的有向边，边的权值为c
        e[++idx]=b;
        ne[idx]=h[a];
        h[a]=idx;
        w[idx]=c;
    }
    static int dijkstra(){
        PriorityQueue<Pair> heap=new PriorityQueue<>();//创建小根堆
        //(1)初始化距离
        Arrays.fill(dist,max);
        dist[1]=0;
        heap.add(new Pair(0,1));
        //(2)每次将不在集合s中的距离最近的点加入集合
        while(!heap.isEmpty()){
            Pair p=heap.poll();//小根堆中每次取的堆顶元素都是距离最小的元素
            int distance=p.first;
            int index=p.second;
            if(st[index]){
                continue;
            }
            st[index]=true;
            for(int i=h[index];i!=-1;i=ne[i]){
                int j=e[i];
                dist[j]=Math.min(dist[j],distance+w[i]);
                //这里有重边的话也不影响，因为小根堆会根据距离进行排序，只是会在小根堆中
                //存储冗余数据罢了
                heap.add(new Pair(dist[j],j));
            }
        }
        if(dist[n]==max){
            return -1;
        }else{
            return dist[n];
        }
    }
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        n=sc.nextInt();
        m=sc.nextInt();
        //一些初始情况的设定
        Arrays.fill(h,-1);
        for(int i=1;i<=m;i++){
            int x,y,z;
            x=sc.nextInt();
            y=sc.nextInt();
            z=sc.nextInt();
            add(x,y,z);
        }
        System.out.println(dijkstra());
    }
}
```



## 4、Bellman-Ford算法（适用于带有负权边的单源最短路问题，且是唯一适用于有边数限制的最短路问题）

### 是什么：

是一种用于求解单源最短路且有负权边的情况，且是唯一适用于有边数限制的最短路问题的解决办法。什么叫做有边数限制？例如：要求从源点到n号点最多经过k条边的最短距离，这里的最多k条边就是边数限制



### 为什么：

dijkstra算法无法适用于有负权边的情况，且后续介绍的最短路算法也无法解决有边数限制的问题，所以引入Bellman-Ford算法



### 怎么办：

#### Bellman-Ford算法的思路

（1）初始化距离，将源点到源点本身的距离设置为0，其他点距离源点的距离设置为无穷大

（2）第1轮循环找到距离源点距离为1条边（这里的1条边仅仅指代物理上的1条边不在乎这条边的权值）的所有节点，并利用源点更新他们到源点的距离

（3）第2轮循环找到距离源点距离为2条边的所有节点，并利用上一轮的节点更新当前这一轮节点到源点的距离

。。。



#### Bellman-Ford算法的一般步骤

```Java
static void bellman_ford(){
    //(1)初始化距离
    Arrays.fill(dist,max);
    dist[1]=0;
	//(2)经过k次循环（k表示对于边数的限制）
	for k次 //k表示对于边数的限制
    	//(3)每次循环中用上一轮的距离更新这一轮所有的边的距离
    	for 所有边（a,b,w） //这里a表示边的起点，b表示边的终点，w表示边的权值
        	dist[b]=Math.min(dist[b],backup[a]+w);//其中backup[]表示上一轮的距离
}

```

#### 如何理解上述Bellman-Ford算法？

（1）如何理解外层的k次循环？

这里的k表示对于边数的限制，当k=1时表示从源点经过1条边可以到达的节点，当k=2时表示从源点经过2条边可以到达的节点，，，当k=k时表示从源点经过k条边可以到达的节点

（2）如何理解内层的更新所有边的操作？

比如说上一轮我们找到了与源点相距k条边的节点i的距离，那么这一轮借由节点i的距离以及一条由i指向j的边那么我们就可以得到与源点相距k+1条边的节点j的距离。特别要注意的是，这里说的更新所有边是真的是所有的边，哪怕上一轮某个点的距离已经更新过了，这一轮同样需要更新（当然这一轮更新后该点的距离不变是最好，假如变得更短的话那么就证明了必须得更新），这一点是与dijkstra算法不同的



#### Bellman-Ford算法与Dijkstra算法的区别

以下图为例，dijkstra算法的步骤是这样的：

（1）初始化距离，将节点1的距离设置为0其他点的距离设置为无穷

（2）在当前所有点中找到距离节点1最近的点就是节点1本身，将它加入集合s，然后用它去更新剩下点（2、3、4号节点）的距离，这里因为1号节点只与2号节点直接相连所以也只能更新2号节点的距离（dist[2]=1）

（3）在剩下的点中找到距离节点1最近的点就是节点2，将它加入集合s，然后用节点2去更新剩下点（3、4号节点）的距离，这里因为2号节点与3号节点、4号节点直接相连所以可以更新3号、4号节点的距离（dist[3]=6，dist[4]=3）

（4）在剩下的点中找到距离节点1最近的点就是节点4，将它加入集合s，然后用节点4去更新剩下点（3号节点）的距离，这里因为没有从4号节点指向3号节点的边所以3号节点的距离没有更新

（5）在剩下的点中找到距离节点1最近的点就是节点3，将它加入集合s，至此所有节点都加入集合s，算法执行完毕（完全没有用到从节点3出发到达节点4的这条负权边）

由上述算法可以得到4号节点到1号节点的最短距离是3（路径是节点1->2->4），但其实根本不对，4号节点到1号节点的最短距离应该是1（路径是节点1->2->3->4）,造成上述错误的原因就是这里存在负权边



还是以下图为例，用bellman-ford算法的求解步骤是这样的：

（1）初始化距离，将节点1的距离设置为0其他点的距离设置为无穷

（2）利用节点1的距离以及从节点1出发的边更新其他点的距离，得到节点2到节点1的距离为1

（3）利用节点2的距离以及从节点2出发的边更新其他点的距离，得到节点4到节点1的距离为3，节点3到节点1的距离为6

（4）利用节点4的距离以及从节点4出发的边更新其他点的距离，发现没有一条边从节点4出发所以没法用节点4去更新其他点的距离；利用节点3的距离以及从节点3出发的边更新其他点的距离得到节点4的距离为1

由上述算法可以得到4号节点到1号节点的最短距离是1（路径是节点1->2->3->4），这是正确答案。

回顾上述算法与dijkstra算法的差异在哪？

无非就是dijkstra算法中存在有一个集合s，而一旦某个节点加入了集合s那么后续就没法再对它的距离进行更新了，而bellman-ford算法中没有这个限制，每次都是更新所有的边

![1688714400295](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1688714400295.png)



### （853）有边数限制的最短路

![1688696704536](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1688696704536.png)

解题思路：

典型的bellman-ford算法，注意在bellman-ford算法中对于图的表示没有必要再用邻接表或邻接矩阵了，随便用一个类，然后将边的起点、终点、权值放入类中作为属性即可

值得注意的有以下几点：

#### （1）在更新距离的时候为什么要复制上一轮的距离？

因为上一轮代表着从源点经过k条边到达的节点，这一轮代表着从源点经过k+1条边到达的节点；假设现在有一条由i指向j的边，上一轮得到节点i到源点的距离为dist1，这一轮我们应该由这个dist1来更新节点j的距离，但是好巧不巧的是这一轮发现了一条负权边通往节点i导致节点i到源点的距离变得更近了为dist2（但是节点i到源点的边数变成了k+1条），那难道我们这一轮可以用dist2去更新节点j到源点的距离吗？当然不行，因为规定了这一轮是源点经过k+1条边可以到达的节点，如果我们用dist2的话那么就势必会走过那条负权边所在的路，那么节点j到源点的边数就是k+2条边了。图示如下：

1、当k=2时意味着从源点经过2条边可以到达的节点，此时dist[4]=3，dist[5]=无穷大

2、当k=3时意味着从源点经过3条边可以到达的节点，此时dist[4]=1，dist[5]=5(路径为节点1->2->4->5)（也就是在这里发现了一条通往节点4的负权边，路径为节点1->2->3->4，但是此时我们不能用本轮的dist[4]去更新dist[5]，因为那样的话就会造成通往节点5的路径为节点1->2->3->4->5即从源点经过4条边才能到达节点5）

3、当k=4时意味着从源点经过4条边可以到达的节点，此时dist[4]=1，dist[5]=3

![1688718240286](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1688718240286.png)

#### （2）为什么最后的判断语句是dist[n]>max/2，而不是dist[n]==max呢？

一开始我们会设置除了源点外的所有节点的dist为max后续会对所有的边的距离进行更新，即使是那些从源点不可达的节点也会进行更新，现在假设节点j和n不可达的话，并且有一条由j指向n的负权边，那么后续依然会更新节点n的距离使得dist[n]!=max，但是不管怎么更新它依然是属于max这个数量级的但是dist[n]已经不等于原来的max了

![1688718836848](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1688718836848.png)

```Java
import java.util.*;
class Edge{
    //创建一个类用来表示边，这里的三个属性的意思如下：
    //a:边的起点
    //b:边的终点
    //w:边的权值
    int a,b,w;
    public Edge(int a,int b,int w){
        this.a=a;
        this.b=b;
        this.w=w;
    }
}
public class Main{
    static int N=510,M=10010,n,m,k,max=0x3f3f3f3f;
    static int[] dist=new int[N];
    static int[] backup=new int[N];//用来存储上一轮的dist数组
    static Edge[] edges=new Edge[M];//用来存储所有边的Edge类数组
    
    static void bellman_ford(){
        //（1）初始化距离
        Arrays.fill(dist,max);
        dist[1]=0;
        /*
        （2）进行k次迭代（k表示对于边数的限制，k=1表示单源经过1条边能到达的节点，
        k=2表示单源经过2条边能到达的节点，，，）
        */
        for(int i=1;i<=k;i++){
            backup=Arrays.copyOf(dist,n+1);
            /*
            复制上一轮的距离（为什么这里是n+1呢？）
            这里的dist表示要复制的数组，这里的n+1表示复制的数组的长度为n+1，
            即将数组dist中从0开始的n+1长度范围内的数组进行复制。
            为什么要这么做呢？
            因为我们使用dist数组时是从dist[1]开始存储数据的，一直存储到dist[n]，
            现在要复制dist数组必须从dist[0]开始复制而且必须得将dist[n]复制过来，
            所以长度就是n+1
            
            为什么要复制上一轮的距离呢？
            因为上一轮代表着从源点经过k条边到达的节点，这一轮代表着从源点经过
            k+1条边到达的节点；假设现在有一条由i指向j的边，上一轮得到节点i
            到源点的距离为dist1，这一轮我们应该由这个dist1来更新节点j的距离，
            但是好巧不巧的是这一轮发现了一条负权边通往节点i导致节点i到源点
            的距离变得更近了为dist2，那难道我们这一轮可以用dist2去更新节点j
            到源点的距离吗？当然不行，因为规定了这一轮是源点经过k+1条边可以
            到达的节点
            */
            
            //（3）用上一轮的距离更新所有边
            for(int j=1;j<=m;j++){
                Edge p=edges[j];
                int a=p.a;
                int b=p.b;
                int w=p.w;
                dist[b]=Math.min(dist[b],backup[a]+w);
            }
        }
        if(dist[n]>max/2){
            /*
            为什么这里是dist[n]>max/2，而不是dist[n]==max呢？
            因为假设节点j和n不可达的话，并且有一条由j指向n的负权边，那么
            后续依然会更新节点n的距离使得dist[n]!=max，但是不管怎么更新
            它依然是属于max这个数量级的
            */
            System.out.println("impossible");
        }else{
            System.out.println(dist[n]);
        }
    }
    
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        n=sc.nextInt();
        m=sc.nextInt();
        k=sc.nextInt();
        for(int i=1;i<=m;i++){
            int x,y,z;
            x=sc.nextInt();
            y=sc.nextInt();
            z=sc.nextInt();
            edges[i]=new Edge(x,y,z);
        }
        bellman_ford();
    }
}
```





## 5、spfa算法（适用于带有负权边的单源最短路问题，且算法效率一般情况下要比dijkstra，bellman_ford算法高）

### 是什么：

一种适用于带有负权边的单源最短路算法



### 为什么：

该算法的效率一般情况下要比dijkstra，bellman-ford算法高



### 怎么办：

#### 如何理解spfa算法？

spfa算法是在bellman-ford算法的基础上提出了优化，bellman-ford算法是在每次循环中更新所有的边，但其实这个过程是没有必要的，因为并不是所有的边每次都会被更新，所有边的长度始终都是确定的，那么就只有当边的起点距离被更新的时候该条边对应的终点距离才会被更新，所以我们可以弄一个队列将那些当前更新了距离的点加入队列，然后找出那些以队列中的点作为起点的边继续更新该条边对应的终点的距离然后继续将终点加入队列，重复上述过程



### （851）spfa求最短路

![1688871845743](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1688871845743.png)

解题思路：

就是典型的spfa算法

```Java
import java.util.*;
public class Main{
    static int N=100010,n,m,idx,max=0x3f3f3f3f,head=1,tail=0;
    /*
    这里的head,tail作为全局变量被赋值会出现数组下标越界的问题，具体原因见下述
    */
    static int[] e=new int[N];
    static int[] ne=new int[N];
    static int[] w=new int[N];
    static boolean[] st=new boolean[N];//用来标记每个顶点是否加入队列中
    static int[] dist=new int[N];
    static int[] h=new int[N];
    static int[] q=new int[N];//将更新了距离的顶点编号加入队列
    
    static void add(int a,int b,int c){
        e[++idx]=b;
        ne[idx]=h[a];
        h[a]=idx;
        w[idx]=c;
    }
    
    static int spfa(){
        head=1;
        tail=0;
        /*
        为什么要在这里给head,tail赋值？在外面全局变量的时候赋值不行吗？
        不行，因为注意看本题在main()方法中我们总共调用了两次spfa()算法，
        第一次调用spfa()算法是判断是否spfa()==max，这里调用了一次spfa()算法；
        然后发现它不等于max然后这时候接着第二次调用spfa()算法，想通过这次调用
        求出该算法的结果，但是因为是共用一个全局变量head,tail，因为上次调用
        过head,tail的缘故没有归位所以造成了这次的数组下标越界
        */
        //初始化距离
        Arrays.fill(dist,max);
        dist[1]=0;
        
        //将更新了距离的点加入队列
        q[++tail]=1;
        st[1]=true;//标记顶点加入队列
        
        while(head<=tail){//当队列不空的时候
            int i=q[head++];//取队头
            st[i]=false;//标记顶点出队列
            for(int j=h[i];j!=-1;j=ne[j]){//更新所有以当前顶点作为起点的边
                int k=e[j];
                if(dist[k]>dist[i]+w[j]){
                    dist[k]=dist[i]+w[j];
                    if(!st[k]){
                    //这里的if(!st[k])语句必须嵌套在上述if(dist[k]>dist[i]+w[j])语句内部
                        q[++tail]=k;
                        st[k]=true;
                    }
                }
                /*
                if(!st[k]){
                    要将这个if(!st[k])语句写在上面的if(dist[k]>dist[i]+w[j])语句内部，
                    为什么？
                    因为只有一个顶点的距离更新了我们才将它加入队列中，而不是随便看到
                    一个顶点发现它没有加入队列我们就将它加入队列，这样的话假如存在回路
                    那么就会产生死循环，将一个点加入队列然后取出来顺带将它出度的边的
                    终点加入队列，然后该终点出队列顺带又将它出度的边的终点加入队列
                    q[++tail]=k;
                    st[k]=true;
                }
                */
            }
        }
        return dist[n];
    }
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        n=sc.nextInt();
        m=sc.nextInt();
        Arrays.fill(h,-1);
        for(int i=1;i<=m;i++){
            int x,y,z;
            x=sc.nextInt();
            y=sc.nextInt();
            z=sc.nextInt();
            add(x,y,z);
        }
        //这里总共会调用两次spfa()算法
        if(spfa()==max){//第一次调用spfa()算法
            System.out.println("impossible");
        }else{
            System.out.println(spfa());//第二次调用spfa()算法
        }
    }
}
```





### （852）spfa判断负环

![1689044987458](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1689044987458.png)

解题思路：

#### 怎么办：

#### 如何用spfa算法判断一个图中是否存在负环？

spfa算法的基本思想就是将更新了距离（这里的距离特指到起点的距离）的顶点入队，然后找到以这些顶点作为起点的边再去更新对应终点的距离，然后再将这些终点入队，重复上述过程直到所有顶点的距离都完成更新。注意在这个过程中图中是不能存在负环的，因为假如存在负环的话那么绕负环一圈某个点的距离就减少一点，那么该点的距离会一直更新下去永无止尽直到该点的距离为负无穷，图示如下：

![1689047156406](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1689047156406.png)



#### 这里如何规定图的起点以及如何理解这里的距离呢？

与上述求单源最短路问题不同的是上述问题规定了起点就是1号节点，然后求1号节点到n号节点的最短路径；这里我们并没有给出图的起点，要求的是图中是否存在负环，因为该图可能是不连通的而且所有点都可能是起点。而且之前我们规定了每个点的距离特指的是到起点的距离，那么现在的这个距离到底指的是什么呢，从图中并没有看到一个明确的起点啊？

这里我们可以想象一个虚拟的0号节点作为起点，它和每个节点都存在一条虚拟的权值为0的边，现在要求每个节点到0号节点的最短距离（其实问题还是原来的问题但是因为加上了0号节点所以变得容易理解了）

```Java
//解法一：用数组实现队列，注意会有数组下标越界的问题
import java.util.*;
public class Main{
    static int N=2010,M=10010,n,m,idx,head=1,tail=0;
    /*
    这里的head,tail作为全局变量被赋值会出现数组下标越界的问题，具体原因见下述
    */
    static int[] e=new int[M];
    static int[] ne=new int[M];
    static int[] w=new int[M];
    static boolean[] st=new boolean[N];//用来标记每个顶点是否加入队列中
    static int[] dist=new int[N];//用来记录每个点的最短路径距离是多少
    static int[] cnt=new int[N];//用来记录每个点的最短路径经过多少条边
    static int[] h=new int[N];
    //static int[] q=new int[N];//将更新了距离的顶点编号加入队列
    /*
    这里会产生数组下标越界的问题，因为这里用数组模拟队列，数组的大小是固定的，
    后续虽然将元素移除队列但是这种移除是通过移动队列的head指针实现的，该元素
    依然处于数组之中占据着数组的位置，这里如何判断存在负环？就是通过判断是否
    存在一个点的最短距离需要经过n条边（需要经过n条边就意味着经过n+1个点，因为
    总共只有n个点所以一定会有两个点重合即是同一个点，即存在环；而又因为这里的是
    最短距离所以该环一定是负环，否则假如是正环的话那么平白无故绕一圈会导致距离
    增加）
    这里的情况就是存在负环但是该负环位于路径的后端且需要绕好多趟才能保证一个点
    的最短距离经过n条边而此时已经造成数组下标越界了
    
    这里的解决办法是：
    （1）开辟一个更大的数组充当队列
    （2）直接用现成的队列（它里面会针对元素出队进行处理不占用队列空间）
    */
    static int[] q=new int[10000000];//解法一：开辟更大的数组充当队列
    
    static void add(int a,int b,int c){
        e[++idx]=b;
        ne[idx]=h[a];
        h[a]=idx;
        w[idx]=c;
    }
    
    static boolean spfa(){
        head=1;
        tail=0;
        for(int i=1;i<=n;i++){
            q[++tail]=i;
            st[i]=true;
        }
        while(head<=tail){
            int i=q[head++];
            st[i]=false;
            for(int j=h[i];j!=-1;j=ne[j]){
                int k=e[j];
                if(dist[k]>dist[i]+w[j]){
                    dist[k]=dist[i]+w[j];
                    cnt[k]=cnt[i]+1;
                    if(cnt[k]>=n){
                        return true;
                    }
                    if(!st[k]){
                        q[++tail]=k;
                        st[k]=true;  
                    }
                }
            }
        }
        return false;
    }
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        n=sc.nextInt();
        m=sc.nextInt();
        Arrays.fill(h,-1);
        for(int i=1;i<=m;i++){
            int x,y,z;
            x=sc.nextInt();
            y=sc.nextInt();
            z=sc.nextInt();
            add(x,y,z);
        }
        if(spfa()){
            System.out.println("Yes");
        }else{
            System.out.println("No");
        }
    }
}
```

```Java
//解法二：直接用现成的队列，不用担心数组下标越界问题
import java.util.*;
public class Main{
    static int N=2010,M=10010,n,m,idx;
    static int[] e=new int[M];
    static int[] ne=new int[M];
    static int[] w=new int[M];
    static boolean[] st=new boolean[N];
    static int[] h=new int[N];
    static int[] dist=new int[N];
    static int[] cnt=new int[N];
    
    static void add(int a,int b,int c){
        e[++idx]=b;
        ne[idx]=h[a];
        w[idx]=c;
        h[a]=idx;
    }
    
    static boolean spfa(){
        Queue<Integer> q=new LinkedList<>();
        for(int i=1;i<=n;i++){
            q.add(i);
            st[i]=true;
        }
        while(!q.isEmpty()){
            int i=q.poll();
            st[i]=false;
            for(int j=h[i];j!=-1;j=ne[j]){
                int k=e[j];
                if(dist[k]>dist[i]+w[j]){
                    dist[k]=dist[i]+w[j];
                    cnt[k]=cnt[i]+1;
                    if(cnt[k]>=n){
                        return true;
                    }
                    if(!st[k]){
                        q.add(k);
                        st[k]=true;
                    }
                }
            }
        }
        return false;
    }
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        n=sc.nextInt();
        m=sc.nextInt();
        Arrays.fill(h,-1);
        for(int i=1;i<=m;i++){
            int a,b,c;
            a=sc.nextInt();
            b=sc.nextInt();
            c=sc.nextInt();
            add(a,b,c);
        }
        if(spfa()){
            System.out.println("Yes");
        }else{
            System.out.println("No");
        }
    }
}
```





## 6、Floyd算法（适用于求多源最短路问题）

### 是什么：

适用于求解多源最短路问题的算法



### 怎么办：

#### 如何理解floyd算法？

从任意节点i到任意节点j的最短路径不外乎两种可能：一是直接从i到j，二是从i经过若干个节点k到j。所以，我们假设d(i,j)为节点i到节点j的最短路径的距离（d(i,j)一开始初始化就是由i到j这条边的长度），对于每一个节点k，我们检查d(i,k) + d(k,j) < d(i,j)是否成立，如果成立，证明从i到k再到j的路径比i直接到j的路径短，我们便设置d(i,j) = d(i,k) + d(k,j)，这样一来，当我们遍历完所有节点k，d(i,j)中记录的便是i到j的最短路径的距离（注意这里的d(i,k)并不是一成不变的，而是我们也会不断探究d(i,k)是否具有更短的路径长度的，比如说d(i,k)>d(i,u)+d(u,k)则我们更新d(i,k)=d(i,u)+d(u,k)，后续当用到d(i,k)的时候用的都是更新后的d(i,k)）。 



### （854）floyd求最短路

![1689150997073](ACWing%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%E8%AF%BE.assets/1689150997073.png)



解题思路：

典型的floyd算法

```Java
import java.util.*;
public class Main{
    //这里是稠密图再加上要求的是多源最短路，所以用邻接矩阵来存储图
    static int N=210,M=20010,n,m,k,max=0x3f3f3f3f;
    static int[][] g=new int[N][N];//用邻接矩阵来表示图
    
    /*
    floyd算法是针对多源最短路问题的算法，它的原理就是利用二维数组来存储图，一开始
    二维数组d[i][j]存储的是由节点i指向节点j这条边的距离，后来经过更新d[i][j]存储的
    就是节点i指向节点j的最短路径了。
    
    如何理解这里的k？
    可以理解成从节点i走向节点j中间只经过1~k这些节点时的最短路径，因为这里的k也是会
    不断变化的，所以就相当于将从节点i走向节点j中间所有可能经过的点都尝试过一遍然后
    找到那个距离最短的一条路径
    */
    static void floyd(){
        for(int k=1;k<=n;k++){//从节点i走到节点j中间只经过1~k这些节点
            for(int i=1;i<=n;i++){
                for(int j=1;j<=n;j++){
                    g[i][j]=Math.min(g[i][j],g[i][k]+g[k][j]);
                }
            }
        }
    }
    
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        n=sc.nextInt();
        m=sc.nextInt();
        k=sc.nextInt();
        //初始化距离
        for(int i=1;i<=n;i++){
            for(int j=1;j<=n;j++){
                if(i==j){
                    g[i][j]=0;
                }else{
                    g[i][j]=max;
                }
            }
        }
        for(int i=1;i<=m;i++){
            int x,y,z;
            x=sc.nextInt();
            y=sc.nextInt();
            z=sc.nextInt();
            g[x][y]=Math.min(g[x][y],z);
        }
        floyd();
        for(int i=1;i<=k;i++){
            int x,y;
            x=sc.nextInt();
            y=sc.nextInt();
            if(g[x][y]>max/2){
                /*
                为什么这里是g[x][y]>max/2，而不是g[x][y]==max呢？
                g[i][j]=min(g[i][j],g[i][k]+g[k][j])，
                这里假设节点i没法到达节点k和节点j即g[i][k]=0x3f、g[i][j]=0x3f
                而且g[k][j]为负边，那么g[i][j]还是会被更新为0x3f加上g[k][j]这条
                负边的长度，虽然只更新了一点但它已经不再是0x3f了
                */
                System.out.println("impossible");
            }else{
                System.out.println(g[x][y]);
            }
        }
    }
}
```

