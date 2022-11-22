## algorithm

### 一些简单定义

#### 数据结构的概念

数据结构是计算机存储、组织数据的方式。数据结构是指相互之间存在一种或多种特定关系的数据元素的集合。
----来自百度

#### 算法的概念

算法就是定义良好的计算过程，它取一个或一组的值为输入，并产生出一个或一组值作为输出。简单来说，算法就是一系列的计算步骤，用来计算输入的数据。

#### 算法的时间复杂度和空间复杂度

如何衡量算法的好坏
对于算法的好坏，都是通过比较算法的时间复杂度和空间复杂度来判断。因为算法在编写成可执行程序后，运行时需要消耗时间资源和空间（内存）资源，如果哪个算法消耗的时间资源或者空间资源小，那么这个算法就更优化。

时间复杂度
时间复杂度是衡量一个算法的运行快慢的方法，它并不是程序的运行时间，因为不可能每个程序都要通过运行来得到运行时间，再进行比较。时间复杂度是一个函数，定量描述了函数的运行时间。

空间复杂度
空间复杂度也不是程序占用了多少字节的空间，空间复杂度算的是变量的个数，空间复杂度也是一个函数，是对一个算法在运行过程中临时占用存储空间大小的量度。

函数运行时所需要的栈空间(存储参数、局部变量、一些寄存器的信息等)在编译期间已经确定好了，因此，空间复杂度主要通过函数在运行时显式申请的额外空间来确定。

注意，在计算机的快速发展下，内存已经变得非常大了，所以人们慢慢从以前的关心空间复杂度变为了现在的关心时间复杂度，比如，在一个算法中，出现了以空间换取时间的做法。

#### 大O渐进表示法

大O渐进表示法适合于时间复杂度和空间复杂度。它规定了时间复杂度和空间复杂度的表示。

推导大O阶方法：

1.在表达式只有常数的情况下，用常数1进行替代。
2.在修改后的运行次数函数中，只保留最高阶项。
3.如果最高阶项存在且系数不是1，则去除与这个项目相乘的常数，得到的结果就是大O阶。

有些算法时间存在最好，平均和最坏的情况:
最坏情况：任意输入规模的最大运行次数（上界）
平均情况：任意输入规模的期望运行次数
最坏情况：任意输入规模的最小运行次数（下界）

在实际中一般关注的是算法的最坏运行情况。

### ACM输入输出

```java
import java.util.Scanner;

Scanner in = new Scanner(System.in);
//连续整数
while(in.hasNext()){
    int a = in.nextInt();
}
//有限整数
int n = in.nextInt();
while(n-- >0){
    int a = in.nextInt();
}

//空格分开的整数
while(in.hasNext()){
    String[] temp = in.nextLine().split(" ");
    for(String s:temp){
        
    }
}
//***********************************************

//使用BufferedReader or InputStreamReader
BufferedReader br = new BufferedReader(new INputtreamReader(System.in));

String line = br.readLine();

// 多整数
line.trim().split(" ");

// 第一行 两个数 后面跟一个数组
while(sc.hasNext()){
    int n = sc.nextInt();
    int l = sc.nextLong();
    long[] nums = new long[n];
    for(int i = 0;i<n;i++){
        nums[i] = sc.nextLong();//l最大10亿>2亿
    }
    
    //输出保留2位
    System.out.println(String.format("%.2f",gap/2.0));
}

//简单模板
import java.util.Scanner;

public class Main{
    public static void main(String[] args){
        Scanner sc = new Scanner(System.in);
        while(sc.hasNext()){
            int a = sc.nextInt();
            int b = sc.nextInt();
            
            System.out.println(a + b);
        }
    }
}

String.join(" ",strings);
```



### 链表

##### 反转链表（三种）

1. 使用栈(先进后出)

   ```java
   	//1.使用栈
       public static ListNode reverseListNode(ListNode head){
           Stack<ListNode> nodeStack = new Stack<>();
           while(head != null){
               nodeStack.push(head);
               head = head.next;
           }
   
           if(nodeStack.isEmpty())
               return null;
   
           ListNode node = nodeStack.pop();
           //记得把头结点留出来
           ListNode newHead = node;
   
           while(!nodeStack.isEmpty()){
               node.next = nodeStack.pop();
               node = node.next;
           }
   
           node.next = null;
           return newHead;
       }
   ```

2. 双链表结构

   ```java
   	//2.双链表
       public static ListNode reverseListNode2(ListNode head) {
           ListNode newHead = null;
           while(head != null){
               ListNode next = head.next;//留存
               head.next = newHead;//head指向第二个链表的旧头节点
               newHead = head;//把head变成第二个链表新的头节点
               head = next;//重新赋值head
           }
           return newHead;
       }
   ```

3. 递归调用

   

**是否相交**

无脑一点用HashSet,遍历加入A链表节点，再遍历B是否有存在于其中的

二.双指针

由于链表的next只可能为一个结点，所以只要相交后，那必然后面全部一样

所以假设有两个链表A,B总结点数分别为m,n

A的不相交部分只可能是前面的部分a

B的不相交部分只可能是前面的部分b

C为相交部分的个数

那么对于有相交部分的链表

m = n 时候，那必然a = b,此时遍历a次得到

m !=n 时候，则需要到第二轮，双方从对方节点遍历到节点，共a+c+b次得到

对于没有相交的部分

遍历m + n次得到 null

```java
//双指针
    public ListNode getIntersectionNode2(ListNode headA, ListNode headB) {
        if(headA == null || headB == null)
            return null;
        ListNode pA = headA,pB = headB;
        while(pA!=pB){
            pA = pA == null ? headB:pA.next;
            pB = pB == null ? headA:pB.next;
        }
        return pA;
    }
```



##### 是否有环

这个比较简单，只要做个快慢指针，一个一次两步零一个一次一步就可以了，只要没有撞到一起，就没有环

```java
public static boolean hasCycle(ListNode head){
        /**
         * 记得判断是否为空
         */
        if(head == null){
            return  false;
        }

        ListNode slow = head;
        ListNode fast = head;
        while(fast.next != null && fast.next.next != null){
            slow = slow.next;
            fast = fast.next.next;

            if(slow == fast){
                return true;
            }
        }

        return false;
    }
```

##### 链表相加：

Long都寄了，长度不够，所以可以使用维护一个进位变量的方法

```java
public static ListNode addTwoNumbers2(ListNode l1, ListNode l2) {
        ListNode node = new ListNode();
        ListNode pre = node;//不直接操作原指针
        int t = 0;//进位变量
        while(l1 != null || l2 != null || t != 0){
            if(l1 != null){
                t += l1.val;
                l1 = l1.next;
            }
            if(l2 != null){
                t += l2.val;
                l2 = l2.next;
            }
            pre.next = new ListNode(t % 10);
            pre = pre.next;
            t /= 10;
        }
        return node.next;
    }
```

##### 约瑟夫环

😅随手写一个，超时了

```java
		public static int lastRemaining(int n, int m) {
        if(n == 1) return 0;
        if(m == 1) return n-1;

        ListNode head = new ListNode(0);
        ListNode node = head;
        for (int i = 1; i < n; i++) {
            node.next = new ListNode(i);
            node = node.next;
        }
        node.next = head;
        int count = n;
        while(count != 1){
            int buShu = m-2;
            while(buShu != 0){
                head = head.next;
                buShu -= 1;
            }
            head.next = head.next.next;
            head = head.next;
            count --;
        }
        return head.val;
    }
```

用ArrayList模拟循环链表

```java
	public static int lastRemaining3(int n, int m) {
        List<Integer> lists = new ArrayList<>(n);

        for (int i = 0; i < n; i++) {
            lists.add(i);
        }

        int index = 0;
        while(n>1){
            index = (index + m - 1) % n;
            lists.remove(index);
            n--;
        }
        return lists.get(0);
    }
```

数学方法反推位置——神中神

每次(当前index + 移动位数) % 上一轮剩余个数

```java
	public static int lastRemaining2(int n, int m) {
        int index = 0;
        for (int i = 2 ; i < n ; i ++){
            index = (index + m) % i;
        }
    }
```

### 回溯算法

差不多就是下一步行不通就继续在上一步的基础上做

### 分治算法



## 排序/查找

简单说下时间复杂度和空间复杂度

所谓时间复杂度：就是程序执行基本操作的次数，他是大概的，并不精确

如n^2 + 2n + 10 => O(n^2)

对于二分查找，比如查找了x次 那就是

2^x = N => O(logN)

空间复杂度：就是程序临时空间所使用元素的个数

hh，五分钟想起来的东西忘了

### 排序

前面这些简单的或者高频问的必会+手撕

![image-20220727091452651](.\photo\image-20220727091452651.png)

#### 不同情况下选择

比较排序算法中时间复杂度最低为O(nlgn)



#### 希尔

按下标增量来

比如10个，10/2 =>5（1和6）,5/2=>2（1和3）

#### 冒泡

当数组已经有序时，不需要再遍历许多次，可以定义flag变量来记录数组是否已经有序，有序则退出循环。

```java
public static void bubble(int[] nums){
    for (int i = 0; i < nums.length-1; i++) {
        for (int j = i+1; j < nums.length; j++) {
            if (nums[i]>nums[j]){
                int temp = nums[i];
                nums[i] = nums[j];
                nums[j] = temp;
            }
        }
    }
}
```

#### 插入

```java
public static void insert(int[] nums){
        for (int i = 1; i < nums.length; i++) {
            int target = nums[i];
            int j = i - 1;
//            while(j>=0 && nums[j]<target) {
//                nums[j+1] = nums[j];
//            }
            for (j=i-1; j >= 0 && nums[j]>target; j--) {
                    nums[j+1] = nums[j];
            }
            nums[j+1]= target;
        }
    }
```

#### 选择

```java
public static void choose(int[] nums){
    for (int i = 0; i < nums.length - 1; i++) {
        int target = nums[i];
        int min = nums[i];
        int minTarget = i;
        for (int j = i+1; j < nums.length; j++) {
            if(nums[j]<min){
                min = nums[j];
                minTarget = j;
            }
        }
        int temp = nums[i];
        nums[i] = nums[minTarget];
        nums[minTarget] = temp;
    }
}
```

#### 快速排序

1.选出一个大小适中的元素，放在数组首元素位置。
2.选择一种快速排序算法。并返回一个可递归数据。
3.进行递归。

选择大小适中的元素

为了保证时间复杂度，我们不能遍历所有的数据，然后找出中间值，所以我们取首，中，尾三个元素的中间值作为该大小适中的元素。



初代：

本质上每一次递归都将中间值大的元素放在中间值的后面，比中间值小的元素放在中间值左边，即找到中间值在排好序的数组中的位置。



```java
public static void quickSort3(int[] arr,int low,int high){
    int i,j,temp,t;
    if(low>high){
        return;
    }
    i=low;
    j=high;
    //temp就是基准位
    temp = arr[low];
    while (i<j) {
        //先看右边，依次往左递减
        while (temp<=arr[j]&&i<j) {
            j--;
        }
        //再看左边，依次往右递增
        while (temp>=arr[i]&&i<j) {
            i++;
        }
        //如果满足条件则交换
        if (i<j) {
            t = arr[j];
            arr[j] = arr[i];
            arr[i] = t;
        }
    }
    //最后将基准为与i和j相等位置的数字交换
    arr[low] = arr[i];
    arr[i] = temp;
    //递归调用左半数组
    quickSort3(arr, low, j-1);
    //递归调用右半数组
    quickSort3(arr, j+1, high);
}
```

堆排序

![在这里插入图片描述](https://img-blog.csdnimg.cn/ea7b5663158e4148970351e05fd5a7b9.png)





**基数排序**



#### **桶排序**



#### 归并排序



#### 计数排序



#### 查找

1. 顺序查找算法：按照数据的顺序一项一项逐个查找，所以不管数据顺序如何，都要从头到尾的遍历一次。速度比较慢，它的时间复杂度是 T=O(n)。
2. 二分查找算法：将数据分割成两等份，然后用键值(要查找的数据)与中间值进行比较，逐个缩小查找范围。速度比顺序查找快，它的时间复杂度是 T=O(log n)。
3. 插补查找算法：按照数据的分布，利用公式预测键值所在的位置，快速缩小键值所在序列的范围，慢慢逼近，知道查找到数据为止，这中算法比二分法查找速度还快，它的时间复杂度为 T=O(log log(n))。
4. 分块查找算法：要求是顺序表，它是顺序查找算法的一种改进方法，它的时间复杂度是 T=O(log以2为底m的对数+N/m)。
5. 斐波拉契查找算法：斐波拉契查找算法就是在二分法的基础上根据斐波拉契数据进行分割。用键值(想要查找的数据)与黄金分割点进行比较。逐渐缩小查找范围。它的时间复杂度是 T=O(log 2n)。
6. 哈希查找算法：把一些复杂的数据，通过某种函数映射关系，映射成更加易于查找的方式。这种方法速度最快，它的时间复杂度是 T=O(1)。

查找算法的名称	时间复杂度（大O表示法）

| 顺序查找算法     | O(n)                     |
| ---------------- | ------------------------ |
| 二分查找算法     | O(log n)                 |
| 插补查找算法     | O(log log n)             |
| 分块查找算法     | O(log以2为底m的对数+N/m) |
| 斐波拉契查找算法 | O(log 2n)                |
| 哈希查找算法     | O(1)                     |

##### 正则表达

```txt
正则表达式 '^[a-zA-Z0-9''-'\s]{1,30}$' 代表什么意思？
[A-z] 等于 [a-zA-Z] 表示匹配所有大小写字母
0-9 表示数字
"-' 都是表示本义字符,匹配这三个符号.
\s 匹配非换行的所有空白字符
以上全部在[]中代表匹配以上所有内容.
然后{1,30}代表匹配1次到30次,最少1次.
头尾的^和$代表匹配一个完整的字符串,如果这个字符串中出现任何一个不是[]中内容的字符这个字符串就无法匹配.

最后^$匹配的定义可以是单行的也可以是多行的,这需要靠正则的选项判断.
```

**先利用快慢指针找到链表中心位置**

快：一步两格

慢：一步一格

```java
while(fast != null && fast.next != null){
    fast = fast.next.next;
    slow = slow.next;
}
```



##### **双指针**

```java
		int start = 0,end = numbers.length-1;
        int[] ints = new int[2];

        while(start < end){
            if(numbers[start]+ numbers[end] > target)
                end--;
            if(numbers[start]+ numbers[end] < target)
                start++;
            if (numbers[start]+ numbers[end] == target){
                ints[0] = start;
                ints[1] = end;
                break;
            }
        }
```

##### **二分查找相关**

```java
public static int firstBadVersion(int n) {
    int start = 1 , end = n;
    while(start  < end){
        //这种写法int过大可能会越界超过int范围
        int mid = start + end >> 1;
        //这种相对好点
        //start / 2 + end / 2
        //这种更快
        //start + (end - start) / 2
        if(isBadVersion(mid)){
           end = mid;
        }else{
            start = mid + 1;
        }
    }
    return start;
}

public static boolean isBadVersion(int n){
    if(n<4){
        return false;
    }else{
        return true;
    }
}
```

子数组(和，积)

##### 滑动窗口

对于正整数连续序列的和或者积可以用

它右指针遍历数组，左指针为了让值偏向于目标而向右滑动

```java
	//滑动窗口，妙啊
	//滑动窗口模板，l是窗口左边界，r是窗口右边界，窗口中的值一定是连续值。
    //当窗口中数字和小于target时，r右移; 大于target时，l右移; 
    public static int numSubarrayProductLessThanK(int[] nums, int k) {
        int start = 0, end = 0, product = 1, count = 0;
        while (end < nums.length){
            product *= nums[end];
            //积>=k，左侧窗口右移
            while (start <= end && k <= product){
                product /= nums[start ++] ;
            }

            if(start <= end){
                count += end - start + 1;
            }
            end++;
        }

        return count;
    }
```

但它并不适用于含有负数的

##### 朴素前缀和

可以弥补上述用于含有负数的

```java
//朴素前缀和
public static int subarraySum2(int[] nums, int k) {
    int count = 0,sum = 0;
    HashMap<Integer,Integer> maps = new HashMap<>();
    maps.put(0,1);
    for (int num:nums){
        sum += num;
        count += maps.getOrDefault(sum - k,0);
        maps.put(sum,maps.getOrDefault(sum,0)+1);
    }
    return count;
}
```

得到最大和子数组

```java
public static int maxSubArray(int[] nums){
    int maxSum = Integer.MIN_VALUE;
    int minSum = 0,sum = 0;
    for (int num:nums){
        sum += num;
        maxSum = Math.max(maxSum,sum-minSum);
        minSum = Math.min(minSum,sum);
    }
    return maxSum;
}
```

找第K大值

可以构造一个优先队列，PriorityQueue

### 字符串

##### 暴力匹配

```java
	// 暴力匹配算法
	public static int violenceMatch(String s1, String s2) {
		char[] c1 = s1.toCharArray();
		char[] c2 = s2.toCharArray();
	int length = c1.length;
	int length2 = c2.length;
 
	int i = 0;
	int j = 0;
	while (i < length && j < length2) {
 
		if (c1[i] == c2[j]) {
			i++;
			j++;
		} else {
			i = i - (j - 1);
			j = 0;
		}
	}
 
	if (j == length2) {
		return i - j;
	} else {
		return -1;
	}
}
```

##### KMP

```java
	// 先来一个方法，获取到一个字符串（子串）的部分匹配值
	public static int[] kmpNext(String string) {
		// 创建一个数组，大小为string.length
		int[] next = new int[string.length()];
	next[0] = 0;// 如果字符串只有一个字符，那部分匹配值就是0；
	for (int i = 1, j = 0; i < next.length; i++) {
		while (j > 0 && string.charAt(i) != string.charAt(j)) {
			j = next[j - 1];
		}
 
		if (string.charAt(i) == string.charAt(j)) {
			j++;
		}
		next[i] = j;
	}
	return next;
}
 
// kmp算法
public static int kmp(String s1, String s2, int[] next) {
	for (int i = 0, j = 0; i < s1.length(); i++) {
		while (j > 0 && s1.charAt(i) != s2.charAt(j)) {
			j = next[j - 1];
		}
 
		if (s1.charAt(i) == s2.charAt(j)) {
			j++;
		}
 
		if (j == s2.length()) {
			return i - (j - 1);
		}
	}
	return -1;
}
```
### **树**

关于平衡二叉树:

- 是二叉树、任意结点的左子树的值均小于根节点的值，右子树均大于根节点的值

- 没有键值相等

  [平衡二叉树](https://so.csdn.net/so/search?q=平衡二叉树&spm=1001.2101.3001.7020)(AVL树)

- 定义

  - 左右字数的高度差的绝对值不超过1，并且两子树都是平衡二叉树
  - 没有键值相等
  - 高度差，又名平衡因子，范围为[-1,0,1]，在此规定==平衡因子 bf = 右子树的高度-左子树的高度==

插入操作：

保证度绝对值<=1，如果超过了就需要进行(左旋右旋左旋右旋)以保证变回二叉树，示例：(图参考CSDN)

- ”左左失衡，parent右旋“：

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329183218702.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjEyNTMxMA==,size_16,color_FFFFFF,t_70)

- “左右失衡，cur左旋，parent右旋”：

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329183443225.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjEyNTMxMA==,size_16,color_FFFFFF,t_70)

- “右右失衡，parent左旋”：

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329183732549.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjEyNTMxMA==,size_16,color_FFFFFF,t_70)

- “右左失衡，cur右旋，parent左旋”：

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329183451132.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjEyNTMxMA==,size_16,color_FFFFFF,t_70)

##### 最深深度

```java
public static int maxHeight(TreeNode root){
        return root==null? 0:Math.max(maxHeight(root.left),maxHeight(root.right))+1;
    }
```

##### 验证搜索二叉树

思路是只要左边的最大值小于当前节点的数而右边最小的数值大于当前节点就可以

**深度优先**

起始就是先找到最底下的，再来

```java
public static void inOrder(TreeNode root,Set<Integer> set){
    if (root == null)
        return;
    inOrder(root.left,set);
    set.add(root.val);
    inOrder(root.right,set);
}
```

**广度优先**

```java
```



#### 构造树

##### 前中序

```java
public static TreeNode buildTree2(int[] preorder, int[] endorder) {
        int n = preorder.length;
        if (n == 0)
            return null;
        int rootVal = preorder[0], rootIndex = 0;
        for (int i = 0; i < n; i++) {
            if (endorder[i] == rootVal) {
                rootIndex = i;
                break;
            }
        }
        TreeNode root = new TreeNode(rootVal);
        root.left = buildTree(Arrays.copyOfRange(preorder, 1, 1 + rootIndex), Arrays.copyOfRange(endorder, 0, rootIndex));
        root.right = buildTree(Arrays.copyOfRange(preorder, 1 + rootIndex, n), Arrays.copyOfRange(endorder, rootIndex + 1, n));

        return root;
    }

    public static String print(int[] n){
        StringBuilder stringBuilder = new StringBuilder();
        for(int i :n){
            stringBuilder.append(i);
        }
        return stringBuilder.toString();
    }
```

#### 前序

```java
//先序
    public static void preOrder(TreeNode node , List<Integer> list){
        if(node == null)
            return;
        list.add(node.val);
        preOrder(node.left,list);
        preOrder(node.right,list);
    }

    public static List<Integer> endorderTraversal(TreeNode root) {
        List<Integer> arrayList = new ArrayList<>();
        endOrder(root,arrayList);
        return arrayList;
    }
```

中序后序同理

### 递归

爬楼梯

```java
	public static int numWays(int n) {
        if(n < 2)
            return 1;
        int a = 1, b = 1,sum = 0;
        for (int i = 2; i <= n; i++) {
            sum = (a + b) % 1000000007;
            a = b;
            b = sum;
        }
        return sum;
    }

    /**
     * hhh,这种写法直接超时,但是easy
     * @param n
     * @return
     */
    public int numWays2(int n) {
        if(n < 2)
            return 1;
        return (numWays2(n-1) + numWays2(n-2))%1000000007;
    }
```

二维递归

一个数组找出是否可以分为两部分和一样的数组



背包问题



### 雪花算法

一般用于用户id的创建，唯一ID生成算法，且具备有序性和可扩展性。

生成出的ID是一个64bits的整数，其中时间戳占41位，数据中心ID占5位，机器ID占5位，自增序列占12位，还有1位是符号位。

先了解一下为什么要用这个算法

- 创建用户id如果通过数据库id自增的方式：

  - 那每次插入数据都会占用自增锁和插入锁

  - 尝试爆破就可以大概了解公司业务量

- 使用redis String进行increment，如果需要高可用，还需要设置集群服务器对应自增步长

- 使用UUID生成唯一ID，但不具有有序性，查找能力较差

各个结构

各个BIT位作用
        1. 41位时间戳，用来根据时间变化做递增，并且可以和最后一次生成时间做对比，如果处于同一毫秒。该如何处理
        2. 数据中心ID，相同的代码，相同的时间，但数据中心ID不同，就不会生成出相同的UUID。
机器ID，相同的代码，相同的时间，相同数据中心，但机器ID不同，就不会生成出相同的UUID。
        3. 递增序列，如果处于同一毫秒内，递增序列则可以自增，保证ID的不唯一，自增ID最大12字节也就是2^12-1(4095)，也就是说，一台机器，可以在同1毫秒内生成4096个ID(为0时也算一个)，一秒就可以生成4,096,000‬个ID。

### 存在两数和

一般可以用hash表，比如

```java
//如果ints唯一
public boolean find(ArrayList<Integer> ints,int sum){
    HashSet<Integer> intMap = new HashSet<>();
    for (Integer inta:ints){
        if (intMap.contains(sum - inta))
            return true;
        intMap.add(inta);
    }
    return false;
}
```

也可以通过双指针

```java
//有序数组
public boolean find(int[] ints,int sum){
    for (){
        
    }
}
```



### 实用小接口

```java
//集合排序(倒序)
Collections.sort(ouList,(a,b)->b-a);
//数组排序
Arrays.sort(nums)
```

