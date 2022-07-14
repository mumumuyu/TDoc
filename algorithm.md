## algorithm

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

### 排序/查找

**二分查找相关**

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

```java

```

