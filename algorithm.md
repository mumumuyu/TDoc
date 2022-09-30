## algorithm

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

### 排序

前面这些简单的或者高频问的必会+手撕

![image-20220727091452651](C:\Users\L\Desktop\文档\photo\image-20220727091452651.png)

#### 冒泡

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

##### 堆排序

![在这里插入图片描述](https://img-blog.csdnimg.cn/ea7b5663158e4148970351e05fd5a7b9.png)



##### 希尔排序



**基数排序**



##### **桶排序**



##### 归并排序



##### 计数排序



#### 查找

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

