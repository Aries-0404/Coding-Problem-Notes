# Leetcode热题hot100

## （4）寻找两个正序数组的中位数（二分）

![1707489191485](Leetcode%E7%83%AD%E9%A2%98hot100.assets/1707489191485.png)

解题思路：

==这里要求时间复杂度为O(log(m+n))，所以很显然想到二分查找==，但是二分查找只适合有序数组，这里的两个数组虽然各自有序但是整体是无序的，没法对于整体利用二分查找啊。

对于一个有序数组a[]的中位数我们直接返回中间下标处的元素即可，那么对于两个有序数组a[]、b[]整体构成的中位数呢？先求出a[]数组的中位数，再求出b[]数组的中位数，然后二者求平均数吗？

当然不对，因为两个数组构成的整体而言它的中位数不一定就是a[]数组的中位数和b[]数组的中位数，举例：a[]数组很长但是里面的元素都很小，b[]数组很短但是里面的元素都很大，所以对于a[]数组、b[]数组整体而言b[]数组就相当于是直接放在a[]数组元素后面的，此时的中位数一定来自于a[]数组（和b[]数组没有关系）。

这里提供一个==可以用于求解两个有序数组中第k小数的二分查找算法==：

（1）首先我们来思考一下这样一个问题：对于两个有序数组如何通过暴力算法找到第k小数呢？

我们想到用双指针，一个指针left指向a[]数组开头元素，另一个指针right指向b[]数组开头元素，再用一个数count记录当前是第几小的数，然后进行比较并不断移动指针，如果a[left]>b[right]并且count<k则移动right指针，然后进行下一轮比较，这样当最后count=k时a[left]和b[right]两个元素中较小者一定就是第k小数。

上述算法时间复杂度为O(m+n)（假设两个数组a[]，b[]长度分别为m,n），因为这里两个指针每次都是移动一位，所以时间复杂度就是这么多。

（2）考虑改进算法。

分析一下上述算法，发现思路是对的，但是有点慢这个慢主要是指针移动慢导致的（指针每次移动一位），那么有没有办法让指针每次多移动几位呢？当然是可以的，具体介绍如下：

假设要找两个有序数组a[]、b[]中的第k小数，则每次我们让两个指针各自移动k/2位即代表每次从两个数组中各自取出==第 k/2 个数即a[k/2]和b[k/2]==，如果a[k/2]<b[k/2]则a[k/2]一定不会是两个有序数组中的第k小的数，为什么？

因为首先a[1]、a[2]、、、a[k/2]和b[1]、b[2]、、、b[k/2]这里就有k个元素了，在这些元素当中a[k/2]都没法排到第k位（因为a[k/2]<b[k/2]），更不用说对于全部a[]数组和b[]数组整体而言了；同理a[1]、a[2]、、、a[k/2-1]都不可能是两个有序数组中的第k小的数，所以我们可以直接将a[1]、a[2]、、、a[k/2]这k/2个数扔了，在剩下来的数组a[k/2+1]、a[k/2+2]、、、、和b[1]、b[2]、、、中继续寻找第k-(k/2)小的数，为什么这里是寻找第k-(k/2)小的数呢？

因为刚刚已经删去了a[1]、a[2]、、、a[k/2]这k/2个数，这些数也是占位置的。

这里还得注意一个问题，就是每次都能从数组中找到第k/2个元素吗？

当然不是，可能数组很短根本就没有第k/2个元素，此时我们就取该数组的最后一个元素即可。

分析上述改进算法：因为是寻找两个有序数组中的第k个数，每次从两个有序数组中寻找第k/2个数，然后抛弃其中的k/2个数，接着更新k=k-(k/2)，然后重复上述过程直到其中一个数组为空或者是k=1，时间复杂度为：O(logk)，这里的k因为是两个数组的中位数，所以k=(m+n)/2，所以时间复杂度为O(log(m+n)/2)即O(log(m+n))

```java
class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int len1=nums1.length;
        int len2=nums2.length;
        int totalLen=len1+len2;
        /*
        这里要注意数组的中位数可能有两个，当数组元素为偶数个时此时的中位数有两个，
        我们要返回的结果就是这两个中位数的平均值
        */
        if((totalLen&1)==1){//此时两个数组中总共有奇数个元素，此时的中位数只有一个
            return 1.0*findKth(nums1,0,len1-1,nums2,0,len2-1,totalLen/2+1);
        }else{
            int res1=findKth(nums1,0,len1-1,nums2,0,len2-1,totalLen/2);
            int res2=findKth(nums1,0,len1-1,nums2,0,len2-1,totalLen/2+1);
            return 1.0*(res1+res2)/2;
        }
    }

    //寻找两个有序数组a[l1~r1]、b[l2~r2]整体构成的第k小的数
    public int findKth(int[] a,int l1,int r1,int[] b,int l2,int r2,int k){
        int len1=r1-l1+1;
        int len2=r2-l2+1;
        if(len1>len2){
            return findKth(b,l2,r2,a,l1,r1,k);//确保先处理较短的数组
        }
        
        //这里的递归终止条件是其中一个数组为空或者是k=1
        if(len1==0){
            return b[l2+k-1];
        }
        if(k==1){
            return Math.min(a[l1],b[l2]);
        }

        /*
        当要找到两个数组整体构成的第k小的数时，每次从两个数组中分别取出第(k/2)个元素
        进行比较，这里要注意可能会遇到数组中没有第k/2个元素的情况，此时就取该数组的最后
        一个元素即可
        */
        int i=l1+Math.min(k/2,len1)-1;
        int j=l2+Math.min(k/2,len2)-1;
        
        if(a[i]<b[j]){
            return findKth(a,i+1,r1,b,l2,r2,k-(i-l1+1));
        }else{
            return findKth(a,l1,r1,b,j+1,r2,k-(j-l2+1));
        }
    }
}
```





## （11）盛最多水的容器（双指针）

![1708000634686](Leetcode%E7%83%AD%E9%A2%98hot100.assets/1708000634686.png)

解题思路：

==暴力做法：==

用两层for循环，其中外层for循环用于遍历容器的左边界，内层for循环用于遍历容器的右边界，时间复杂度O(n^2)

```java
class Solution {
    public int maxArea(int[] height) {
        int max=0;
        for(int i=0;i<height.length;i++){
            for(int j=i+1;j<height.length;j++){
                int h=Math.min(height[i],height[j]);
                max=Math.max(max,h*(j-i));
            }
        }
        return max;
    }
}
```



==双指针做法：==

仔细观察上述暴力做法看看是否有可以改进的地方，在上述暴力算法中当考虑容器的左边界的时候我们会考虑每一块板，当考虑容器的右边界的时候我们也会考虑每一块板，但其实这大可不必，因为目的是使得容器的面积最大，而怎么做可以使得容器的面积最大呢？

那就是整个容器尽可能又宽又高这样的面积才是最大的，所以我们一开始固定容器的左右边界就是数组的两边，这样的容器是最宽的但是不一定是面积最大的（因为此时的高度不一定是最大的），所以我们该如何找面积最大的容器呢？依然是遍历所有可能的情况吗（用两层for循环，其中外层for循环用于遍历左边界的所有可能情况，内层for循环用于遍历右边界的所有可能情况），这种遍历所有可能情况的做法也太麻烦了，时间复杂度依然为O(n^2)丝毫没有改善。那么我们这时候该如何移动指针呢，此时到底应该是移动左边界还是右边界呢？

这就要看限制此时容器面积的是什么因素了：此时的宽度一定是最大的，那么限制容器面积的只可能是容器高度了，具体点就是左右边界高度二者中的最小值，具体分析见下图：

本题的关键就在于正确性的证明：类似贪心做法

![1708000854267](Leetcode%E7%83%AD%E9%A2%98hot100.assets/1708000854267.png)

```java
class Solution {
    public int maxArea(int[] height) {
        int l=0,r=height.length-1;
        int max=0;
        while(l<r){
            int h1=height[l],h2=height[r];
            if(h1<=h2){
                max=Math.max(max,h1*(r-l));
                l++;
            }else{
                max=Math.max(max,h2*(r-l));
                r--;
            }
        }
        return max;
    }
}
```





## （15）三数之和（双指针）

![1708003517606](Leetcode%E7%83%AD%E9%A2%98hot100.assets/1708003517606.png)

解题思路：

==暴力做法：==

首先我们将数组进行排序，然后利用三层for循环，其中最外层for循环用于遍历下标i，中间层for循环用于遍历下标j，最内层for循环用于遍历下标k，然后看看nums[i]+nums[j]+nums[k]是否等于0，同时还要注意[nums[i],nums[j],nums[k]]这个三元组不能重复，该算法的时间复杂度为O(n^3)，那既然是用三层for循环遍历所有可能情况的话为什么之前还要对数组进行排序呢？排序的目的是为了保证三元组不能重复的时候方便进行排除，比如nums[i]+nums[j]+nums[k]=0，接着遍历k=k+1的情况，如果此时nums[k+1]=nums[k]则我们直接跳过后续对于k的遍历



==双指针做法：==

观察上述暴力做法看看是否有可以改进的地方，我们发现当对数组进行排序以后再进行三重for循环就显得冗余，想想办法怎么可以改进，我们知道对于有序数组求和问题可以利用双指针同时遍历来降低时间复杂度，这里也是如此，我们可以利用一层for循环用来遍历k的情况，然后for循环内部用左右反向指针来遍历数组的所有元素，当然这里得排除重复元素的情况，该算法的时间复杂度为：先固定k然后左右指针遍历数组的所有元素，左右指针遍历数组的时间复杂度为O(n)；对于每一个k的时间复杂度都是O(n)，遍历k的所有可能情况，这里k的取值为O(n)级别，所以该算法的时间复杂度为O(n^2)

![1708051144113](Leetcode%E7%83%AD%E9%A2%98hot100.assets/1708051144113.png)

```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> ans=new ArrayList<>();
        Arrays.sort(nums);
        int len=nums.length;
        for(int k=0;k<len;k++){
            if(nums[k]>0){
                return ans;
            }
            if(k>=1 && nums[k]==nums[k-1]){
                continue;
            }
            int i=k+1,j=len-1;
            while(i<j){
                int sum=nums[k]+nums[i]+nums[j];
                if(sum==0){
                    List<Integer> list=new ArrayList<>();
                    list.add(nums[k]);
                    list.add(nums[i]);
                    list.add(nums[j]);
                    ans.add(list);
                    i++;
                    j--;
                    while(i<len && nums[i]==nums[i-1]){
                        i++;
                    }
                    while(j>=0 && nums[j]==nums[j+1]){
                        j--;
                    }
                }else if(sum<0){
                    i++;
                }else{
                    j--;
                }
            }
        }
        return ans;
    }
}
```





## （19）删除链表的倒数第N个节点（双指针）

![1708052566676](Leetcode%E7%83%AD%E9%A2%98hot100.assets/1708052566676.png)

解题思路：

本题就是利用左右同向指针的典型题目，首先我们将左右指针指向链表的头节点，然后让右指针先跑n步接着左右指针一起移动这样当最后右指针为空时此时的左指针指向的就是链表中倒数第n个节点，因为要删除链表中倒数第n个节点所以我们还得另外弄一个指针pre指向链表中倒数第n个节点前面的一个节点，然后执行pre.next=pre.next.next操作以此将链表中倒数第n个节点删除。

==不过需要注意的是这里当n等于链表长度的时候会删除链表的头节点，而删除链表头节点的操作和上述删除中间节点的操作有些不同所以我们需要特判一下看看n是否等于链表长度，如果不想要特判的话则我们给链表加上一个虚拟头节点dummyhead，并且dummyhead.next=head，==由此链表的头节点就变成了一个链表中一个普通的中间节点，要删除头节点的话就可以当成一个普通的中间节点来删。

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    //注意当n等于链表长度的时候这里会删除链表的头节点
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode dummyhead=new ListNode();
        dummyhead.next=head;
        //设置虚拟头节点指向链表的头节点，这样可以解决当要删除链表头节点的情况

        ListNode l=head;
        ListNode r=head;
        ListNode pre=dummyhead;
        while(n!=0){
            n--;
            r=r.next;
        }
        while(r!=null){
            pre=l;
            r=r.next;
            l=l.next;
        }
        pre.next=pre.next.next;
        return dummyhead.next;
    }
}
```







## （23）合并k个升序链表（归并排序）

![1707192221649](Leetcode%E7%83%AD%E9%A2%98hot100.assets/1707192221649.png)

解题思路：

这里的关键就在于如何看待链表数组中的n个元素，==如果就是将它们看作是一个个的有序链表则没法处理，相反如果将它们看作是一个个普通的数组元素然后利用归并进行不断划分递归则可以解决问题==

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {//采用归并排序的方式
    public ListNode mergeKLists(ListNode[] lists) {
        int r=lists.length-1;
        int l=0;
        if(l>r){
            return null;
        }
        if(l==r){
            return lists[0];
        }
        int mid=l+r>>1;
        ListNode left=mergeKLists(Arrays.copyOfRange(lists,l,mid+1));
        ListNode right=mergeKLists(Arrays.copyOfRange(lists,mid+1,r+1));
        return mergeSort(left,right);
    }

    public ListNode mergeSort(ListNode l1,ListNode l2){
        ListNode head=new ListNode();
        ListNode p=head;
        while(l1!=null && l2!=null){
            if(l1.val<=l2.val){
                p.next=l1;
                l1=l1.next;
                p=p.next;
            }else{
                p.next=l2;
                l2=l2.next;
                p=p.next;
            }
        }
        // while(l1!=null){//这里用while循环的话会导致死循环
        //     p.next=l1;
        // }
        // while(l2!=null){
        //     p.next=l2;
        // }
        if(l1!=null){
            p.next=l1;
        }
        if(l2!=null){
            p.next=l2;
        }
        return head.next;
    }
}
```





## （33）搜索旋转排序数组（二分）

![1707485689298](Leetcode%E7%83%AD%E9%A2%98hot100.assets/1707485689298.png)

解题思路：

我们可以对有序数组进行二分查找，但是对于==旋转之后的数组已经不是有序的了，这时候该怎么办呢？==

可以==将数组一分为二，其中一定有一半数组是有序的，另一半数组可能有序也可能无序==（具体取决于当初旋转的那个位置是不是数组中点），==假如另一半数组无序的话该怎么处理呢？对另一半无序数组继续进行一分为二的操作，则又可以找到一半有序、一半无序的数组，重复上述过程直到将数组划分成只有一个元素==

```java
class Solution {
    /*
    这里得明确一个思想：那就是将数组进行旋转以后再一分为二之后，其中一定有一半数组是有序的，
    另一半数组可能有序也可能无序（这具体取决于当初旋转的位置，如果当初是在数组中点进行旋转
    的话则另一半数组也是有序的否则另一半数组无序），我们对于有序数组利用二分查找，对于无序
    数组继续进行上述一分为二的操作然后又可以找到一半有序数组一半无序数组，然后重复上述过程，
    直到最后数组被分割成了只有一个元素
    */
    public int search(int[] nums, int target) {
        int res=search2(nums,0,nums.length-1,target);
        if(nums[res]==target){
            return res;
        }else{
            return -1;
        }
    }

    public int search2(int[] nums,int l,int r,int k){
        if(l==r){
            return l;
        }
        int mid=l+r>>1;
        if(nums[l]<nums[mid]){//说明数组中[l,mid]这一段是有序的
            int index=binary_Search(nums,l,mid,k);
            if(nums[index]==k){//说明要找的目标值k就在[l,mid]这一段内
                return index;
            }else{
                //说明要找的目标值k不在[l,mid]这一段内，所以接下来要去[mid+1,r]这一段
                //无序数组中继续寻找目标值k
                return search2(nums,mid+1,r,k);
            }
        }else{//说明数组中[l,mid]这一段是无序的，那么[mid+1,r]这一段就是有序的
            int index=binary_Search(nums,mid+1,r,k);
            if(nums[index]==k){
                return index;
            }else{
                return search2(nums,l,mid,k);
            }
        }
    }
    public int binary_Search(int[] nums,int l,int r,int k){
        while(l<r){
            int mid=l+r+1>>1;
            if(nums[mid]<=k){
                l=mid;
            }else{
                r=mid-1;
            }
        }
        return l; 
    }
}
```



## （42）接雨水（dp,双指针）

![1708758379595](Leetcode%E7%83%AD%E9%A2%98hot100.assets/1708758379595.png)

解题思路：

==动态规划：==

```java

```

==双指针：==

```java

```







## （56）合并区间（排序）

![1706866115445](Leetcode%E7%83%AD%E9%A2%98hot100.assets/1706866115445.png)

解题思路：

这里将各个子区间按照左端点进行排序，为什么不按照右端点进行排序呢？

```java
class Solution {
    public int[][] merge(int[][] intervals) {
        int len=intervals.length;
        Pair[] pairs=new Pair[len];
        for(int i=0;i<len;i++){
            pairs[i]=new Pair(intervals[i][0],intervals[i][1]);
        }
        Arrays.sort(pairs);
        List<int[]> list=new ArrayList<>();
        /*
        集合只能装载对象，不能装载基本数据类型，比如集合不能装载int类型但可以装载Integer类型，
        不过数组是对象不是基本数据类型（哪怕int[]数组也是对象）
        */
        
        int left=pairs[0].l;
        int right=pairs[0].r;
        for(int i=1;i<len;i++){
            if(pairs[i].l>right){
                list.add(new int[]{left,right});
                left=pairs[i].l;
                right=pairs[i].r;
            }else{
                right=Math.max(right,pairs[i].r);
            }
        }

        list.add(new int[]{left,right});//上述操作会漏掉最后一个区间的处理
        int[][] ans=new int[list.size()][];
        for(int i=0;i<list.size();i++){
            ans[i]=list.get(i);
        }
        return ans;
    }
}
class Pair implements Comparable<Pair>{
    int l,r;
    public Pair(int l,int r){
        this.l=l;
        this.r=r;
    }
    @Override
    public int compareTo(Pair p){
        return Integer.compare(this.l,p.l);
    }
}
```





## （148）排序链表（归并排序）

![1707203749456](Leetcode%E7%83%AD%E9%A2%98hot100.assets/1707203749456.png)

解题思路：

本题就是对于一个无序链表用归并排序的方式将它变成一个有序链表，==注意归并排序一定要指定排序的范围（这样好进行后续的一分为二操作，前面的数组给定下标范围（l，r）后续找到中间位置的下标mid进行一分为二的操作，这里的链表也是如此给定头节点head和尾节点tail方便后续找到链表的中间节点进行一分为二的操作）==

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode sortList(ListNode head) {
        if(head==null){
            return head;
        }
        //这里创建的tail指针指向链表最后一个节点
        ListNode tail=head;
        while(tail.next!=null){
            tail=tail.next;
        }
        return merge_Sort(head,tail);
    }

    
    public ListNode merge_Sort(ListNode head,ListNode tail){
        if(head==null || head==tail){//当链表中没有元素或者只有一个元素的话直接返回不需要再排序
            return head;
        }
        ListNode slow=head,fast=head,pre=head;
        //这里的slow、fast代表快慢指针，那么pre指针指向的是什么呢？

        while(fast!=null && fast.next!=null){
            //为什么循环条件这样写呢？具体见快慢指针这一节的内容
            pre=slow;
            slow=slow.next;
            fast=fast.next.next;
        }
        /*
        观察上述while循环我们发现，每次循环时都是先给pre指针赋值：pre=slow，接着slow指针进行
        移动，fast指针进行移动，最后循环结束时fast指针指向链表末尾，slow指针指向链表中间
        节点（如果只有一个中间节点的话则slow指针指向该节点，如果有两个中间节点的话则slow指针
        指向两个中间节点中的后一个），而pre指针则指向中间节点的前一个节点
        */
        
        
        pre.next=null;
        ListNode l=merge_Sort(head,pre);
        ListNode r=merge_Sort(slow,tail);
        /*
        前面我们已经知道了slow指针指向链表的中间节点，而pre指针指向中间节点的前一个节点，
        我们根据pre指针将链表一分为二，前半部分为(head,pre)段，后半部分为(slow,tail)段，
        接着对这两段子链表继续进行递归的操作。
        这里有两个问题：（1）为什么令pre.next=null?（2）为什么要根据pre指针进行链表的划分，
        根据slow指针进行划分不行吗？
        （1）我们将链表划分为了两部分，(head,pre)段和(slow,tail)段，然后对这两段分别进行
        排序，如果不令pre.next=null的话那么pre.next本身应该是slow，这样在给链表前半部分
        (head,pre)段进行排序的时候指针会顺着一直延伸到(slow,tail)段，这样就不是单独给
        (head,pre)段进行排序了。
        （2）其实不论根据哪个指针进行链表的划分，只要划分以后问题的规模变小了就是可以的（反映在
        本题中就是子链表的数目相较于原链表变少了则该划分结果就是可行的），所以理论上只要是链表
        中间的某个节点进行划分都可以，那这里就有一个问题：slow指针始终指向链表中间的某个节点吗，
        它不会指向链表的尾节点吗？
        答案是slow指针不是始终指向链表的中间节点它也会指向链表的尾节点，当不断进行链表的划分
        以后最后链表被划分成了只包含两个节点的小链表，对于这个小链表再进行划分的时候初始时
        slow指针、fast指针都指向链表的头节点，循环结束后fast指针指向了尾节点后面的null节点，
        而slow指针指向了尾节点，所以此时如果再根据slow指针进行划分的话问题规模完全没有变小（原先
        链表中就有两个节点，然后划分以后链表中还有两个节点，接着一直划分，就在这里陷入了死循环）
        */
        return merge(l,r);
    }
    
    public ListNode merge(ListNode l1,ListNode l2){
        ListNode head=new ListNode();
        ListNode p=head;
        while(l1!=null && l2!=null){
            if(l1.val<=l2.val){
                p.next=l1;
                p=p.next;
                l1=l1.next;
            }else{
                p.next=l2;
                p=p.next;
                l2=l2.next;
            }
        }
        if(l1!=null){
            p.next=l1;
        }
        if(l2!=null){
            p.next=l2;
        }
        return head.next;
    }
}

```





## （200）岛屿数量（dfs）

![1708610754962](Leetcode%E7%83%AD%E9%A2%98hot100.assets/1708610754962.png)

解题思路：

这里我们的想法就是通过dfs来遍历这个二维数组，但是不像前面两题中有着明显的搜索树层次感，比如：在排列数字问题中的层次就是当前排列的是第几个位置的数字，而在n皇后问题中的层次就是当前排列的是第几行的皇后，而本题的层次是什么呢？

看不出来，但是借由n皇后问题中的第二种思路：按照格子顺序来遍历n皇后问题，我们可以得到启发，本题我们也可以按照二维数组的顺序来遍历每一个点即：dfs(int i，int j)（其中i，j是二维数组中的下标），按照这个顺序的话我们就可以看出此时的层次就是每次访问的点（==假如当前层表示正在访问(i，j)点，则搜索树的下一层就是代表访问(i，j)点的上下左右四个相邻节点==），按照dfs的模板同时借着这个思路我们往下做：

（1）for循环遍历搜索树当前层的所有可能情况：

这里搜索树当前层表示当前正在访问的节点（i，j），那么当前层有多少种可能情况呢？
其实就两种：要么当前节点是陆地节点，要么是海洋节点；这里总共就两种可能情况我们也没必要用for循环来遍历了，直接就用if-else分支来进行遍历就可以

（2）递归搜索树下一层的所有情况：

这里搜索树下一层的所有节点是：在二维数组中与当前节点相邻的上下左右四个节点，我们在for循环内部（前面已经说了这里不用for循环，那就在if-else分支内部）依次递归访问这四个节点

（3）恢复现场：

这里一定要理解的就是恢复现场的操作，在探索完当前层一个分支后，你需要撤销在这个分支上做的操作，以便在探索当前层下一个分支时能够回到当前层的原始状态。也就是说我们恢复现场是为了不耽误对当前层下一个分支的遍历。

#### notes：注意对于恢复现场这一操作的理解

==正确解法1：==

定义搜索结束条件为：下标越界、当前节点已经访问过

```java
class Solution {
    /*
    每个位置与其上下左右四个方向的位置是相邻的，在探索搜索树下一层的过程中我们需要
    依次遍历这四个相邻节点
    */
    static int[] dx=new int[]{-1,1,0,0};
    static int[] dy=new int[]{0,0,-1,1};
    
    static int N=310;
    static boolean[][] visited=new boolean[N][N];//用于记录二维数组中的每个点是否被访问过
     
    
    public static int numIslands(char[][] grid) {
        /*
        每一次调用numIslands()方法之前都要给静态变量visited[][]重新初始化，
        防止上一轮的数据污染这一轮
        */
        for(int i=0;i<N;i++){
            for(int j=0;j<N;j++){
                visited[i][j]=false;
            }
        }
        
        int count = 0; // 用于记录岛屿数量
        for (int i = 0; i < grid.length; i++) {
            for (int j = 0; j < grid[0].length; j++) {
                if (grid[i][j] == '1' && !visited[i][j]) {
                    dfs(grid,i, j);
                    count++; // 发现新岛屿
                }
            }
        }
        return count;
    }

    /*
    我们观察这个dfs方法，它可以从一个点出发就将所有的节点遍历完整吗？
    答案是不能的，因为虽然我们可以从当前这个节点出发向上下左右四个方法延伸，
    然后延伸后的节点继续向上下左右四个方向延伸，但是这种延伸不是无节制的，
    在遇到越界、当前节点已经访问过、访问到海洋节点的时候就会终止，所以每一次
    调用dfs方法我们都可以完整的访问完一个岛屿，不会进行多余的访问，所以最后
    我们遍历二维数组然后对每个节点进行一次dfs搜索，当然不是每一次调用它都会执行，
    因为有的点之前已经被访问过一次了（比如上一层的陆地节点向下延伸的时候访问到了
    这一层的某个节点，则当我们遍历到这一层的该节点的时候就不会再执行dfs方法了），
    所以最后我们只要统计dfs方法执行了多少次就行了
    */
    public static void dfs(char[][] grid,int a,int b){
        //定义搜索结束条件
        if(!isLocation(grid,a,b) || visited[a][b]==true){
            /*
            如果a,b位置不合法或者是该位置已经被访问过了则直接返回
            */
            return;
        }
        
        /*
        遍历搜索树当前层的所有可能情况：
        当前层其实就两种情况：要么当前节点是陆地节点，要么是海洋节点，所以我们也没有
        必要用for循环来遍历搜索树当前层的所有可能情况，直接用if-else就能遍历所有可能情况
        */
        if(grid[a][b]=='0'){//如果搜索树当前节点是海洋节点则直接返回
            return;
        }else{//如果搜索树当前节点是陆地节点的话
            //对搜索树当前层分支进行的操作
            visited[a][b]=true;//将当前节点标记为已访问过
            for(int i=0;i<4;i++){
                /*
                遍历搜索树下一层的所有可能情况，这里要注意搜索树下一层同时有四种情况
                对应于当前节点的上下左右四个节点
                */
                int x=a+dx[i];
                int y=b+dy[i];
                dfs(grid,x,y);
            }
            
            /*
            注意这里在递归结束以后为什么没有恢复现场的操作？
            因为恢复现场指的是在探索完当前层一个分支后，撤销对于该分支的操作，以免影响
            遍历当前层的其他分支。
            这里当前层就两个分支：要么是海洋节点、要么是陆地节点，我们正在遍历的分支是
            陆地节点分支对该分支进行操作，对于海洋节点分支是直接返回的不做任何操作，所以
            根本不用恢复现场，因为不会对另一个分支产生任何影响。
            */
        }
    }
    //这是判断位置i,j是否合法的函数
    public static boolean isLocation(char[][] grid,int i,int j){
        if(i>=0 && i<grid.length && j>=0 && j<grid[0].length){
            return true;
        }else{
            return false;
        }
    }
}
```





## （215）数组中的第k个最大元素（快速选择算法）

![1707121612051](Leetcode%E7%83%AD%E9%A2%98hot100.assets/1707121612051.png)

解题思路：

就是快速选择算法，不过这里尤其要注意的是不要将数组下标和数组元素个数混合在一起使用否则很容易出错

```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        return quickSelect(nums,0,nums.length-1,k);
    }

    public int quickSelect(int[] nums,int l,int r,int k){
        if(l==r){
            return nums[l];
        }
        int mid=nums[(l+r)/2];
        int i=l-1,j=r+1;
        while(i<j){
            do{i++;}while(nums[i]<mid);
            do{j--;}while(nums[j]>mid);
            if(i<j){
                swap(nums,i,j);
            }
        }
        /*
        下述过程中犯了一个很明显的错误：那就是将数组元素个数和数组下标混合起来使用了。
        举个例子，长度为len的数组经过快速排序算法（快速选择）以后数组中第k个最大元素的
        下标将始终是len-k，这一点是不会改变的，而这里我们将每次更新len为子数组的长度，
        这样是不对的
        
        int len=r-l+1;
        //这里的的len是子数组中元素个数（每次迭代的时候会更新l和r为子数组的左右下标，
        //所以顺带着len也更新为了子数组的长度）
        
        if(j<(len-k)){
        //此时说明数组中第K大的元素在数组右半部
        //这里的j和len-k是数组中的下标
            return quickSelect(nums,j+1,r,k);
        }else{
            return quickSelect(nums,l,j,k-((len-1)-(j+1)+1));
        }
        */
        
        //注意下述中是如何完全用下标来表示元素个数的
        if(r-j+1>k){
            return quickSelect(nums,j+1,r,k);
        }else{
            return quickSelect(nums,l,j,k-(r-(j+1)+1));
        }
    }
    public void swap(int[] nums,int i,int j){
        int t=nums[i];
        nums[i]=nums[j];
        nums[j]=t;
    }
}
```





## （238）除自身以外数组的乘积（前缀与后缀）

![1707792633857](Leetcode%E7%83%AD%E9%A2%98hot100.assets/1707792633857.png)

解题思路：

```java
class Solution {
    /*
    本题明确说明了不要使用除法，所以我们使用前缀数组和后缀数组，
    其中前缀数组L[i]记录的是nums[0]*nums[1]*...*nums[i-1]的结果，
    后缀数组R[i]记录的是nums[i+1]*nums[i+2]*...*nums[n-1]的结果
    */
    public int[] productExceptSelf(int[] nums) {
        int len=nums.length;
        int[] l=new int[len];//创建的前缀数组
        int[] r=new int[len];//创建的后缀数组
        l[0]=1;
        for(int i=1;i<len;i++){
            l[i]=l[i-1]*nums[i-1];
        }
        r[len-1]=1;
        for(int j=len-2;j>=0;j--){
            r[j]=r[j+1]*nums[j+1];
        }

        int[] ans=new int[len];
        for(int i=0;i<len;i++){
            ans[i]=l[i]*r[i];
        }
        return ans;
    }
}
```







## （240）搜索二维矩阵2（二分查找）

![1707472860887](Leetcode%E7%83%AD%E9%A2%98hot100.assets/1707472860887.png)

解题思路：

因为二维数组中的每一行都是有序的，所以可以对二维数组的每一行元素分别进行二分查找，最终的时间复杂度为O(n*logm)，其中n代表二维数组的行数、m代表二维数组的列数

```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        int n=matrix.length;
        boolean flag=false;
        for(int i=0;i<n;i++){
            int res=binary_Search(matrix[i],target);
            if(res==target){
                flag=true;
                return flag;
            }
        }
        return flag;
    }
    public int binary_Search(int[] nums,int k){
        int l=0,r=nums.length-1;
        while(l<r){
            int mid=l+r+1>>1;
            if(nums[mid]<=k){
                l=mid;
            }else{
                r=mid-1;
            }
        }
        return nums[l];
    }
}
```





## （287）寻找重复数（哈希表）

![1707473145633](Leetcode%E7%83%AD%E9%A2%98hot100.assets/1707473145633.png)

解题思路：

本题就是在遍历数组元素的过程中将元素放入哈希表中，如果发现哈希表中已经存在过该元素了则发现重复的元素，如果哈希表中不存在该元素的话则将该元素正常放入哈希表即可

```java
class Solution {
    public int findDuplicate(int[] nums) {
        HashMap<Integer,Integer> map=new HashMap<>();
        int len=nums.length;
        for(int i=0;i<len;i++){
            if(map.containsKey(nums[i])){
                return nums[i];
            }else{
                map.put(nums[i],1);
            }
        }
        return 0;
    }
}
```





## （300）最长递增子序列（动态规划、贪心）

![1707488351856](Leetcode%E7%83%AD%E9%A2%98hot100.assets/1707488351856.png)

解题思路：

==动态规划解法：时间复杂度O(n^2)==

本题采用动态规划解法的话很常见，就不多说了

```java
class Solution {
    //普通动态规划的做法，时间复杂度O(n^2)
    public int lengthOfLIS(int[] nums) {
        int len=nums.length;
        int[] dp=new int[len];
        for(int i=0;i<len;i++){
            dp[i]=1;
        }
        for(int i=1;i<len;i++){
            for(int j=0;j<i;j++){
                if(nums[j]<nums[i]){
                    dp[i]=Math.max(dp[i],dp[j]+1);
                }
            }
        }
        int max=0;
        for(int i=0;i<len;i++){
            max=Math.max(max,dp[i]);
        }
        return max;
    }
}
```

==贪心解法：时间复杂度O(nlogn)==

本题采用贪心解法的话，详见acwing中动态规划这一章的最长上升子序列2问题的介绍

```java
class Solution {
    //当采用贪心的做法，时间复杂度O(nlogn)
    public int lengthOfLIS(int[] nums) {
        int[] q=new int[nums.length+1];
        int len=0;
        for(int i=0;i<nums.length;i++){
            int l=0,r=len;
            while(l<r){
                int mid=l+r+1>>1;
                if(q[mid]<nums[i]){
                    l=mid;
                }else{
                    r=mid-1;
                }
            }
            len=Math.max(len,l+1);
            q[l+1]=nums[i];
        }
        return len;
    }
}
```







## （337）打家劫舍3（dfs）

![1705980801344](Leetcode%E7%83%AD%E9%A2%98hot100.assets/1705980801344.png)

解题思路：

本题与acwing中动态规划里面的没有上司的舞会这题几乎一样，但是在那里每一个节点都是有编号的，可以用dp[u] [0]表示探寻以u为根的子树的最大值（不包括u这个节点），用dp[u] [1]表示探寻以u为根的子树的最大值（包括u这个节点）。而这里节点u是一个TreeNode变量，不是一个编号，没法做到每一个节点与一个编号一一对应，所以也就没法用一个dp[] []二维数组来存储每个节点的情况。

那么这里是如何做的呢？

这里采用深度优先搜索的方式遍历每一个节点，该dfs()方法在遍历每一个节点时返回一个包含两个元素的数组int[] res=new int[2];  其中res[0]用来记录以当前节点为根的子树的最大值（不包括当前节点），res[1]用来记录以当前节点为根的子树的最大值（包括当前节点）。

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public int rob(TreeNode root) {
        int[] result = robSub(root);
        return Math.max(result[0], result[1]);
    }

    private int[] robSub(TreeNode root) {
        if (root == null) {
            return new int[]{0, 0};
        }

        int[] left = robSub(root.left);
        int[] right = robSub(root.right);

        // result[0]表示不抢劫当前节点的最大值，result[1]表示抢劫当前节点的最大值
        int[] result = new int[2];

        // 如果我们不抢劫当前节点，我们可以选择抢劫或者不抢劫其子节点的最大值
        result[0] = Math.max(left[0], left[1]) + Math.max(right[0], right[1]);

        // 如果我们抢劫当前节点，我们就不能抢劫其子节点
        result[1] = root.val + left[0] + right[0];

        return result;
    }
}

```







## （338）比特位计数（位运算）

![1706067198341](Leetcode%E7%83%AD%E9%A2%98hot100.assets/1706067198341.png)

解题思路：

位运算的知识，详见acwing中的lowbit方法介绍

```java
class Solution {
    int lowbit(int x){
        return x&(-x);
    }
    public int[] countBits(int n) {
        int[] res=new int[n+1];
        for(int i=0;i<=n;i++){
            int x=i;
            int num=0;
            while(x!=0){
                x-=lowbit(x);
                num++;
            }
            res[i]=num;
        }
        return res;
    }
}
```



## （347）前k个高频元素（排序）

![1707131327019](Leetcode%E7%83%AD%E9%A2%98hot100.assets/1707131327019.png)

解题思路：

创建一个类Pair，里面包含两个属性l和r，其中l代表元素数值r代表元素出现次数，然后根据r将所有的Pair进行排序，找出最大的k个Pair就好。==这里需要注意的就是如何得到hashmap中每一个配对的key-value值==

```java
import java.util.Map.Entry;
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        HashMap<Integer,Integer> map=new HashMap<>();
        for(int num:nums){
            if(map.containsKey(num)){
                map.put(num,map.get(num)+1);
            }else{
                map.put(num,1);
            }
        }
        int len=map.size();
        Pair[] pairs=new Pair[len];

        /*
        如何获取hashmap中的每一个配对的key-value值呢？
        方式1：分别获取hashmap中的key值集合和value值集合
        Set<Integer> keys=map.keySet();//返回hashmap中的所有key值
        Collection<Integer> values=map.values();//返回hashmap中的所有value值
        但是没法往下做了
        */

        //直接获取hashmap中的键值对集合
        Set<Entry<Integer,Integer>> entrySet=map.entrySet();
        int i=0;
        for(Entry<Integer,Integer> entry:entrySet){
            int l=(int)entry.getKey();
            int r=(int)entry.getValue();
            pairs[i]=new Pair(l,r);
            i++;
        }
        Arrays.sort(pairs);
        int[] res=new int[k];
        for(int j=0;j<k;j++){
            res[j]=pairs[len-1-j].l;
        }
        return res;
    }

}
class Pair implements Comparable<Pair>{
    int l;
    int r;
    public Pair(int l,int r){
        this.l=l;
        this.r=r;
    }
    @Override
    public int compareTo(Pair p){
        return Integer.compare(this.r,p.r);
    }
}
```







## （448）找到所有数组中消失的数字（哈希表）

![1706068008096](Leetcode%E7%83%AD%E9%A2%98hot100.assets/1706068008096.png)

解题思路：

在遍历每一个数组元素的同时将它们加入到哈希表中，后续遍历1~n中的每一个数看它们是否在哈希表中，如果在则说明它们出现过如果不在则说明它们没有出现过

```java
class Solution {
    public List<Integer> findDisappearedNumbers(int[] nums) {
        List<Integer> list=new ArrayList<>();

        HashMap<Integer,Integer> map=new HashMap<>();
        int n=nums.length;
        for(int i=0;i<n;i++){
            if(map.containsKey(nums[i])){
                continue;
            }else{
                map.put(nums[i],1);
            }
        }
        for(int i=1;i<=n;i++){
            if(!map.containsKey(i)){
                list.add(i);
            }
        }
        return list;
    }
}
```





## （461）汉明距离（位运算）

![1706076286737](Leetcode%E7%83%AD%E9%A2%98hot100.assets/1706076286737.png)

解题思路：

位运算的知识，就是先进行(a ^ b)的操作，然后看（a^b）这个数的二进制表示中有几个1，还是用到前面的lowbit算法

```java
class Solution {
    int lowbit(int x){
        return x&(-x);
    }
    public int hammingDistance(int x, int y) {
        int a=x^y;
        int res=0;
        while(a!=0){
            a-=lowbit(a);
            res++;
        }
        return res;
    }
}
```



## （463）岛屿的周长（dfs）

![1708675305754](Leetcode%E7%83%AD%E9%A2%98hot100.assets/1708675305754.png)

解题思路：

现在主要有以下几个问题：

（1）如何计算岛屿周长呢？

将岛屿外围每条边的长度相加

（2）如何计算岛屿外围每条边呢，==怎样会形成岛屿外围的一条边呢==？

==①当陆地节点与海洋节点相邻时会形成一条边==

==②当陆地节点位于边界时会形成一条边==

本题还是用dfs的思想，借由前面一题“岛屿数量”我们已经知道当由一个陆地节点进行延伸的时候它会延伸到所有与它相连的陆地节点（碰到边界或者是海洋节点就停止延伸），根据这个思想我们可以首先找到一个陆地节点然后在该节点处进行dfs搜索它会自动进行延伸，然后我们看它延伸的情况，当遇到海洋节点或者是边界时就进行长度加1的操作，最后统计这个长度就行了。

为什么一定得先找到一个陆地节点然后在该节点处进行dfs搜索？

因为并不是随便的一个海洋节点就能构成一条边，也并不是随便的一个位于边界上的节点就能构成一条边，一定得是由陆地节点延伸过去的。

```java
class Solution {
    static int[] dx=new int[]{-1,1,0,0};
    static int[] dy=new int[]{0,0,-1,1};
    public int islandPerimeter(int[][] grid) {
        int N=110;
        boolean[][] visited=new boolean[N][N];
        int ans=0;//用于记录岛屿周长
        for(int i=0;i<grid.length;i++){
            for(int j=0;j<grid[0].length;j++){
                if(grid[i][j]==1 && visited[i][j]==false){
                    ans=dfs(grid,visited,i,j);
                }
            }
        }
        return ans;
    }
    public int dfs(int[][] grid,boolean[][] visited,int a,int b){
        if(a<0 || a>=grid.length || b<0 || b>=grid[0].length || grid[a][b]==0){
            return 1;
        }
        if(visited[a][b]){
            return 0;
        }
        visited[a][b]=true;
        int count=0;
        for(int i=0;i<4;i++){
            int x=a+dx[i];
            int y=b+dy[i];
            count+=dfs(grid,visited,x,y);
        }
        return count;
    }
}
```









## （496）下一个更大元素1（单调栈）

![1708418501759](Leetcode%E7%83%AD%E9%A2%98hot100.assets/1708418501759.png)

解题思路：

因为我们要寻找元素右边第一个比它大的元素，所以我们得维护一个从左到右递增的栈，但是有一个问题：前面一题单调栈的题目因为我们是要寻找元素左边第一个比它小的元素，当我们遍历到该元素的时候该元素左边的所有元素都已经被遍历过了而且我们已经维护好了现有的单调栈，而这里我们要寻找元素右边第一个比它大的元素如果我们继续采用从左到右的遍历顺序的话，当我们遍历到某一个元素的时候该元素右边的所有元素都还没有被遍历过自然也就没法维护并利用单调栈进行求解，所以我们得采用从右到左的遍历顺序并维护单调栈，但这里就需要注意一个问题：那就是当我们采用从左到右的遍历顺序的时候要求单调栈是递增的，如果我们将数组遍历方向反过来的话那么单调栈的方向就是递减的（因为假如说现在有一个递增的数组，当我们采用从左到右的遍历数组的时候自然可以维护一个递增的单调栈；当我们采用从右到左的遍历顺序的时候先将数组最后一个元素入栈，接着将数组倒数第二个元素入栈，，，）

```java
class Solution {
    public int[] nextGreaterElement(int[] nums1, int[] nums2) {
        //这题的暴力算法很简单，时间复杂度为O(m*n)，现在来试试更简单的单调栈方法
        //这里我们用数组来实现单调栈
        HashMap<Integer,Integer> map=new HashMap<>();
        //用来存储nums2数组中每个数对应的右边第一个比它大的数

        int n=nums2.length;
        int[] stk=new int[n];//代表单调栈
        int head=-1;//栈的头指针
        for(int i=nums2.length-1;i>=0;i--){//将数组2的元素倒叙入单调栈
            while(head>=0 && stk[head]<=nums2[i]){
                head--;
            }
            if(head>=0){
                map.put(nums2[i],stk[head]);
            }else{
                map.put(nums2[i],-1);
            }
            stk[++head]=nums2[i];
        }
        int[] ans=new int[nums1.length];
        for(int i=0;i<nums1.length;i++){
            ans[i]=map.get(nums1[i]);
        }
        return ans;
    }
}
```









## （543）二叉树的直径（dfs）

![1706171235225](Leetcode%E7%83%AD%E9%A2%98hot100.assets/1706171235225.png)

解题思路：

dfs的知识，这里就是利用dfs实现遍历二叉树的每一个节点，尤其得注意这里的节点深度更新的操作。

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {

    /*
    本方法用于深度优先搜索遍历以u为根的子树中的每一个节点，同时在遍历节点的时候
    记录每一个节点的左右子树的最大深度，res[0]记录u的左子树的最大深度，
    res[1]记录u的右子树的最大深度
    */
    static int max=0;//用于记录二叉树的直径
    int[] dfs(TreeNode u){
        if(u==null){
            return new int[]{0,0};
        }
        int[] res=new int[2];
        TreeNode l=u.left,r=u.right;
        int[] ans1=dfs(l);
        int[] ans2=dfs(r);
        res[0]=Math.max(ans1[0],ans1[1])+1;
        /*
        这里必须得进行+1操作，为什么？
        因为假如就写成：
        res[0]=Math.max(ans1[0],ans1[1]);
        这样以u为根的左子树的深度就直接等于了以u的左儿子l为根的左子树的深度，也就是说
        这个深度完全没有更新，就这样一直进行dfs搜索直到最后节点为空直接返回深度0，又因为
        后续的所有节点的深度都没有进行更新所以所有节点的深度都为0.

        但是进行了+1操作以后感觉这个意义又不对了啊，假如对于最后一层的节点u它没有儿子节点l和r，
        所以当遍历到它的儿子节点l和r的时候直接返回数组{0,0}，接着又遍历回节点u的时候此时会进行
        +1操作，代码如下：
        int[] ans1=dfs(l);//ans1={0,0}
        int[] ans2=dfs(r);//ans2={0,0}
        res[0]=Math.max(ans1[0],ans1[1])+1;//res[0]=Math.max(0,0)+1=1
        res[1]=Math.max(ans2[0],ans2[1])+1;
        意味着以u为根的左子树的深度为1（而这是不对的，因为它就没有左子树所以深度为0），
        那这代表上述我们不该进行+1操作？
        当然不是，我们已经分析过了必须进行+1操作否则节点深度没有更新，但是现在进行+1操作以后
        好像也说不通啊，其实能说得通，只要我们将此时的1理解为节点数目就可以了（以u为根的左子树
        的最大深度上有多少个节点，这里的节点包含u这个根节点）
        */
        res[1]=Math.max(ans2[0],ans2[1])+1;
        max=Math.max(max,res[0]+res[1]-2);
        /*
        这里进行二叉树直径更新的时候为什么要进行-2操作？
        max=Math.max(max,res[0]+res[1]-2);
        上述我们已经介绍过了res[0]存储的是以u为根的左子树的最大深度上包含的节点数目（包含u这个
        根节点），所以在计算以u为根的左子树的最大深度的时候得进行-1操作，同理在计算右半边的时候
        也是如此
        */
        return res;
    }

    public int diameterOfBinaryTree(TreeNode root) {
        if(root==null){
            return 0;
        }
        max=0;
        /*
        因为max被声明为静态变量，如果这里的diameterOfBinaryTree()方法被多个测试用例
        调用的话会出现上一次的运行结果影响到这一次的运行，所以每一次调用
        diameterOfBinaryTree()方法时需要将max重新置0
        */
        dfs(root);
        return max;
    }
}
```





## （560）和为k的子数组（前缀和）

![1707794077256](Leetcode%E7%83%AD%E9%A2%98hot100.assets/1707794077256.png)

解题思路：

暴力算法：首先确定两个指针i和j，然后计算nums[i~j]范围内的求和结果，该算法的时间复杂度为O(n^ 3)（因为当固定了i指针和j指针以后需要计算nums[i]+nums[i+1]+...+nums[j]的数值，这个时间复杂度也是O(n)级别的），通过前缀和可以将时间复杂度优化到O(n^ 2)

```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        int len=nums.length;
        int[] s=new int[len+1];
        for(int i=1;i<=len;i++){
            s[i]=s[i-1]+nums[i-1];
        }
        int cnt=0;
        for(int i=1;i<=len;i++){
            for(int j=1;j<=i;j++){
                if((s[i]-s[j-1])==k){
                    cnt++;
                }
            }
        }
        return cnt;
    }
}
```





## （617）合并二叉树（dfs，bfs）

![1706176064637](Leetcode%E7%83%AD%E9%A2%98hot100.assets/1706176064637.png)

解题思路：

这里就是dfs的知识和bfs的知识

==采用bfs的方法==

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public TreeNode mergeTrees(TreeNode root1, TreeNode root2) {
        if(root1==null){
            return root2;
        }
        if(root2==null){
            return root1;
        }
        Queue<TreeNode> q=new LinkedList<>();
        Queue<TreeNode> q1=new LinkedList<>();
        Queue<TreeNode> q2=new LinkedList<>();
        TreeNode root=new TreeNode(root1.val+root2.val);
        q.add(root);
        q1.add(root1);
        q2.add(root2);
        while(!q1.isEmpty() && !q2.isEmpty()){
            TreeNode p=q.poll();
            TreeNode p1=q1.poll();
            TreeNode p2=q2.poll();
            TreeNode l1=p1.left,l2=p2.left;
            if(l1!=null || l2!=null){
                if(l1!=null && l2!=null){
                    TreeNode l=new TreeNode(l1.val+l2.val);
                    p.left=l;
                    q.add(l);
                    q1.add(l1);
                    q2.add(l2);
                }else if(l1!=null){
                    p.left=l1;
                }else{
                    p.left=l2;
                }
            }
            TreeNode r1=p1.right,r2=p2.right;
            if(r1!=null || r2!=null){
                if(r1!=null && r2!=null){
                    TreeNode r=new TreeNode(r1.val+r2.val);
                    p.right=r;
                    q.add(r);
                    q1.add(r1);
                    q2.add(r2);
                }else if(r1!=null){
                    p.right=r1;
                }else{
                    p.right=r2;
                }
            }
        }
        return root;
    }
}
```



==采用dfs的方法==

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public TreeNode mergeTrees(TreeNode root1, TreeNode root2) {
        if(root1==null && root2==null){
            return null;
        }
        if(root1==null){
            return root2;
        }
        if(root2==null){
            return root1;
        }
        TreeNode root=new TreeNode(root1.val+root2.val);
        root.left=mergeTrees(root1.left,root2.left);
        root.right=mergeTrees(root1.right,root2.right);
        return root;
    }
}
```





## （695）岛屿的最大面积（dfs）

![1708676393607](Leetcode%E7%83%AD%E9%A2%98hot100.assets/1708676393607.png)

解题思路：

```java
class Solution {
    static int[] dx=new int[]{-1,1,0,0};
    static int[] dy=new int[]{0,0,-1,1};
    public int maxAreaOfIsland(int[][] grid) {
        int N=55;
        boolean[][] visited=new boolean[N][N];
        int max=0;
        for(int i=0;i<grid.length;i++){
            for(int j=0;j<grid[0].length;j++){
                max=Math.max(max,dfs(grid,visited,i,j));
            }
        }
        return max;
    }
    public int dfs(int[][] grid,boolean[][] visited,int a,int b){
        //搜索结束条件
        if(a<0 || a>=grid.length || b<0 || 
            b>=grid[0].length || grid[a][b]==0 || visited[a][b]){
            return 0;
        }
        //遍历搜索树当前层的所有可能情况
        visited[a][b]=true;
        int count=1;
        //递归搜索树下一层的情况
        for(int i=0;i<4;i++){
            int x=a+dx[i];
            int y=b+dy[i];
            count+=dfs(grid,visited,x,y);
        }
        return count;
    }
}
```





