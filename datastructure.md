# Data structure 学习笔记
>    本文来源于网络，可能存在错漏之处，仅供参考。

## 递归
>    [递归：如何用三行代码找到 “最终推荐人”？(王争)](https://www.jianshu.com/p/32eacb1a8337?utm_campaign=maleskine&utm_content=note&utm_medium=seo_notes&utm_source=recommendation)

### 1. 递归需要满足的三个条件
+ 一个问题的解可以分解为几个子问题的解
+ 这个问题与分解之后的子问题，除了数据规模不同，求解思路完全一样
+ 存在递归终止条件  

### 2. 如何编写递归代码
+ 写出递推公式
+ 找到终止条件
- 假设这里有 n 个台阶，每次你可以跨 1 个台阶或者 2 个台阶，请问走这 n 个台阶有多少
种走法？如果有 7 个台阶，你可以2，2，2，1这样上去，也可以1，2，1，1，2这样子上
去，走法有很多，那么如何用编程求得总共有多少种走法呢？
```bash
//递推公式
f(n) = f(n-1) + f(n-2)
//下面是边界情况
//还剩一个台阶有 1 种情况
f(1) = 1;
//还剩两个台阶有 2 种情况，1、1或者 2
f(2) = 2;
```
```bash
int f(int n){
    if(n==1) return 1;
    if(n==2) return 2;
    return f(n-1) + f(n-2);
}
```
- 写递归代码的关键就是找到如何将大问题分解为小问题的规律，并且基于此写出递推公式， 然后再推敲终止条件，最后将递推公式和终止条件翻译成代码。

### 3. 思维误区
- 想清楚整个递和归的过程的做法是一个思维误区。
- 编写递归代码的关键是，只要遇到递归，我们就把它抽象成一个递推公式，不用想一层层的调用关系，不要试图用人脑去分解递归的每个步骤。

### 4. 递归代码要警惕堆栈溢出
- 在学习栈的时候，我们了解到函数调用会使用栈来保存临时变量，每调用一个函数，都会将临时变量封装为栈帧压入内存栈，等函数执行完成返回后，才出栈。那么当调用层次很深的时候，一直压入栈，就会有堆栈溢出的风险。
- 我们可以在代码中限制递归调用的最大深度（就是多少层）来解决这个问题。递归调用超过一定深度（比如1000）之后，我们就不继续往下再递归了，直接返回报错。
```bash
//电影院例子新版
//全局变量
int depth = 0;

int f(int n){
    ++depth;
    if(depth > 1000) throw new XXXException();
    
    if(n == 1) return 1;
    return f(n - 1) + 1;
}
```

### 5. 递归代码要警惕重复计算
- 为了避免重复计算，我们可以通过一个数据结构（比如散列表）来保存已经求解过的 f(k)。当递归调用到 f(k) 时，先看下是否已经求解过了，如果是，则直接从散列表中取值返回，不需要重复计算，这样就能避免重复计算的问题了。
```bash
public int f(int n) {
  if (n == 1) return 1;
  if (n == 2) return 2;
  
  // hasSolvedList 可以理解成一个 Map，key 是 n，value 是 f(n)
  if (hasSolvedList.containsKey(n)) {
    return hasSovledList.get(n);
  }
  
  int ret = f(n-1) + f(n-2);
  hasSovledList.put(n, ret);
  return ret;
}
```

### 6. 将递归代码改为非递归代码
- 递归的表达度高、简洁但是空间复杂度高。
```bash
int f(int n){
    int result = 1;
    for(int i = 2;i <= n;i++){
        result = result +1;
    }
    
    return result;
}
```
```bash
int f(int n) {
  if (n == 1) return 1;
  if (n == 2) return 2;
  
  int ret = 0;
  int pre = 2;
  int prepre = 1;
  for (int i = 3; i <= n; ++i) {
    ret = pre + prepre;
    prepre = pre;
    pre = ret;
  }
  return ret;
}
```

### 7.最终推荐人
```bash
long findRootReferrerId(long actorId) {
  Long referrerId = select referrer_id from [table] where actor_id = actorId;
  if (referrerId == null) return actorId;
  return findRootReferrerId(referrerId);
}
```

## 数组的全排列
>    [参考](https://blog.csdn.net/k346k346/article/details/51154786)  
### 递归实现
- n!表示n个元素全排列的个数。
- 需要消耗大量的栈空间，如果函数栈空间不够，那么就运行不下去了，而且函数调用开销也比较大。
- 无重复数的实现(注意事项：循环将数组中所有元素与第一个元素交换时，再对子数组进行全排列后，需要将第一个元素交换回来，以供下一个元素与第一个元素交换。)
```bash
public class FullPermutation {
	public static int sum=0;
	private static void printArray(int array[]){
		System.out.print("{");
		for(int i = 0; i < array.length; i ++){
			System.out.print(array[i]);
		}
		System.out.print("}");
	}
	private static void swap(int array[], int i, int j){
		int temp = array[i];
		array[i] = array[j];
		array[j] = temp;
	}
	private static void permutation(int array[],int index){
		if(index==array.length){//全排列结束
	        ++sum;
	        printArray(array);
	    }
	    else
	        for(int i=index;i<array.length;++i){
	            //将第i个元素交换至当前index下标处
	            swap(array,index,i);

	            //以递归的方式对剩下元素进行全排列
	            permutation(array,index+1);

	            //将第i个元素交换回原处
	            swap(array,index,i);
	        }
	}
	public static void main(String args[]){
		int array[] = {1,2,3};
		permutation(array,0);
		System.out.print(sum);
	}
	
}
```
- 有重复数的实现（去重的全排列就是从第一个数字起每个数分别与它后面非重复出现的数字交换。）
```bash
private static boolean isSwap(int array[],int index){
        for(int i=index+1;i<array.length;++i)
            if(array[index]==array[i])
                return false;
        return true;
	}

private static void permutation(int array[],int index){
		if(index==array.length){//全排列结束
	        ++sum;
	        printArray(array);
	    }
	    else
	        for(int i=index;i<array.length;++i){
	        	if(isSwap(array,i)){
		            //将第i个元素交换至当前index下标处
		            swap(array,index,i);
	
		            //以递归的方式对剩下元素进行全排列
		            permutation(array,index+1);
	
		            //将第i个元素交换回原处
		            swap(array,index,i);
	        	}
	        }
	}
```

### 非递归的实现
- 全排列的非递归实现需要用到元素排列后的字典序。所谓的字典序就是按照元素的大小对形成排列进行排序。
- 利用字典序来生成全排列的算法思想是：将集合A中的元素的排列，与某种顺序建立一一映射的关系，按照这种顺序，将集合的所有排列全部输出。这种顺序需要保证，既可以输出全部的排列，又不能重复输出某种排列。字典序就是用此种思想输出全排列的一种方式。
- 先排序，再由后向前找第一个替换点，然后由向后向前找第一个比替换点所在元素大的数与替换点交换，最后颠倒替换点后的所有数据。
- [代码1](https://blog.csdn.net/k346k346/article/details/51154786)
- [代码2](https://blog.csdn.net/napoay/article/details/79879529)

## 二叉树遍历
>    [参考1](https://blog.csdn.net/My_Jobs/article/details/43451187)
>    [参考2](https://blog.csdn.net/mingwanganyu/article/details/72033122)

- 前序遍历：根结点 ---> 左子树 ---> 右子树
- 中序遍历：左子树---> 根结点 ---> 右子树
- 后序遍历：左子树 ---> 右子树 ---> 根结点
- 层次遍历：只需按层次遍历即可

### 1. 前序遍历
- 递归
```bash
public void preOrderTraverse1(TreeNode root) {
		if (root != null) {
			System.out.print(root.val+"  ");
			preOrderTraverse1(root.left);
			preOrderTraverse1(root.right);
		}
	}
```
- 非递归  

+ 对于任意一个结点node，具体步骤如下：
+ a)访问之，并把结点node入栈，当前结点置为左孩子；
+ b)判断结点node是否为空，若为空，则取出栈顶结点并出栈，将右孩子置为当前结点；否则重复a)步直到当前结点为空或者栈为空（可以发现栈中的结点就是为了访问右孩子才存储的）。  

```bash
public void preOrderTraverse2(TreeNode root) {
		LinkedList<TreeNode> stack = new LinkedList<>();
		TreeNode pNode = root;
		while (pNode != null || !stack.isEmpty()) {
			if (pNode != null) {
				System.out.print(pNode.val+"  ");
				stack.push(pNode);
				pNode = pNode.left;
			} else { //pNode == null && !stack.isEmpty()
				TreeNode node = stack.pop();
				pNode = node.right;
			}
		}
	}
```

### 2. 中序遍历
- 递归
```bash
public void inOrderTraverse1(TreeNode root) {
		if (root != null) {
			inOrderTraverse1(root.left);
			System.out.print(root.val+"  ");
			inOrderTraverse1(root.right);
		}
	}
```

- 非递归
```bash
public void inOrderTraverse2(TreeNode root) {
		LinkedList<TreeNode> stack = new LinkedList<>();
		TreeNode pNode = root;
		while (pNode != null || !stack.isEmpty()) {
			if (pNode != null) {
				stack.push(pNode);
				pNode = pNode.left;
			} else { //pNode == null && !stack.isEmpty()
				TreeNode node = stack.pop();
				System.out.print(node.val+"  ");
				pNode = node.right;
			}
		}
	}
```

### 3. 后序遍历
- 递归
```bash
public void postOrderTraverse1(TreeNode root) {
		if (root != null) {
			postOrderTraverse1(root.left);
			postOrderTraverse1(root.right);
			System.out.print(root.val+"  ");
		}
	}
```

- 非递归

### 4. 层次遍历（BFS）广度优先遍历
- 层次遍历的代码比较简单，只需要一个队列即可，先在队列中加入根结点。之后对于任意一个结点来说，在其出队列的时候，访问之。同时如果左孩子和右孩子有不为空的，入队列。
- 数据结构：队列
- 父节点入队，父节点出队列，先左子节点入队，后右子节点入队。递归遍历全部节点即可
```bash
public void levelTraverse(TreeNode root) {
		if (root == null) {
			return;
		}
		LinkedList<TreeNode> queue = new LinkedList<>();
		queue.offer(root);
		while (!queue.isEmpty()) {
			TreeNode node = queue.poll();
			System.out.print(node.val+"  ");
			if (node.left != null) {
				queue.offer(node.left);
			}
			if (node.right != null) {
				queue.offer(node.right);
			}
		}
	}
```

### 5. 深度优先遍历（DFS）
- 其实深度遍历就是上面的前序、中序和后序。但是为了保证与广度优先遍历相照应，也写在这。代码也比较好理解，其实就是前序遍历。
- 数据结构：栈
- 父节点入栈，父节点出栈，先右子节点入栈，后左子节点入栈。递归遍历全部节点即可
```bash
public void depthOrderTraverse(TreeNode root) {
		if (root == null) {
			return;
		}
		LinkedList<TreeNode> stack = new LinkedList<>();
		stack.push(root);
		while (!stack.isEmpty()) {
			TreeNode node = stack.pop();
			System.out.print(node.val+"  ");
			if (node.right != null) {
				stack.push(node.right);
			}
			if (node.left != null) {
				stack.push(node.left);
			}
		}
	}
```

## 十大排序算法
>    [参考](http://www.codeceo.com/article/10-sort-algorithm-interview.html#0-tsina-1-10490-397232819ff9a47a7b7e80a40613cfe1)

### 冒泡排序
- 通过与相邻元素的比较和交换来把小的数交换到最前面。从后向前冒泡.
- 时间复杂度为O(n^2)；空间复杂度为O(1)；最坏时间复杂度O(n^2)
```bash
public class BubbleSort {

    public static void bubbleSort(int[] arr) {
        if(arr == null || arr.length == 0)
            return ;
        for(int i=0; i<arr.length-1; i++) {
            for(int j=arr.length-1; j>i; j--) {
                if(arr[j] < arr[j-1]) {
                    swap(arr, j-1, j);
                }
            }
        }
    }

    public static void swap(int[] arr, int i, int j) {
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
}
```

### 选择排序
- 和冒泡排序有点类似，都是在一次排序后把最小的元素放到最前面，但是选择排序是通过对整体的选择。只是选择排序只有在确定了最小数的前提下才进行交换，大大减少了交换的次数。
- 例：对5,3,8,6,4这个无序序列进行简单选择排序，首先要选择5以外的最小数来和5交换，也就是选择3和5交换。
- 选择排序的时间复杂度为O(n^2)；空间复杂度(1)；最坏时间复杂度O(n^2)。
```bash
public class SelectSort {

    public static void selectSort(int[] arr) {
        if(arr == null || arr.length == 0)
            return ;
        int minIndex = 0;
        for(int i=0; i<arr.length-1; i++) { //只需要比较n-1次
            minIndex = i;
            for(int j=i+1; j<arr.length; j++) { //从i+1开始比较，因为minIndex默认为i了，i就没必要比了。
                if(arr[j] < arr[minIndex]) {
                    minIndex = j;
                }
            }

            if(minIndex != i) { //如果minIndex不为i，说明找到了更小的值，交换之。
                swap(arr, i, minIndex);
            }
        }

    }

    public static void swap(int[] arr, int i, int j) {
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }

}
```

### 插入排序
- 插入排序是通过比较找到合适的位置插入元素来达到排序的目的的。
- 例子，对5,3,8,6,4这个无序序列进行简单插入排序，首先假设第一个数的位置时正确的，想一下在拿到第一张牌的时候，没必要整理。然后3要插到5前面，把5后移一位，变成3,5,8,6,4.想一下整理牌的时候应该也是这样吧。然后8不用动，6插在8前面，8后移一位，4插在5前面，从5开始都向后移一位。
- 注意在插入一个数的时候要保证这个数前面的数已经有序。
- 简单插入排序的时间复杂度也是O(n^2);空间复杂度O(1);最坏时间复杂度O(n^2)。
```bash
public class InsertSort {

    public static void insertSort(int[] arr) {
        if(arr == null || arr.length == 0)
            return ;

        for(int i=1; i<arr.length; i++) { //假设第一个数位置时正确的；要往后移，必须要假设第一个。

            int j = i;
            int target = arr[i]; //待插入的

            //后移
            while(j > 0 && target < arr[j-1]) {
                arr[j] = arr[j-1];
                j --;
            }

            //插入 
            arr[j] = target;
        }

    }

}
```

### 快速排序
- 快速排序是比较和交换小数和大数，这样一来不仅把小数冒泡到上面同时也把大数沉到下面。
- 时间复杂度O(nlogn);最坏时间复杂度O(n^2);空间复杂度O(logn)。
```bash
对5,3,8,6,4这个无序序列进行快速排序，思路是右指针找比基准数小的，左指针找比基准数大的，交换之。

5,3,8,6,4 用5作为比较的基准，最终会把5小的移动到5的左边，比5大的移动到5的右边。

5,3,8,6,4 首先设置i,j两个指针分别指向两端，j指针先扫描（思考一下为什么？）4比5小停止。然后i扫描，8比5大停止。交换i,j位置。

5,3,4,6,8 然后j指针再扫描，这时j扫描4时两指针相遇。停止。然后交换4和基准数。

4,3,5,6,8 一次划分后达到了左边比5小，右边比5大的目的。之后对左右子序列递归排序，最终得到有序序列。
```

```bash
public class QuickSort {
    //一次划分
    public static int partition(int[] arr, int left, int right) {
        int pivotKey = arr[left];
        int pivotPointer = left;

        while(left < right) {
            while(left < right && arr[right] >= pivotKey)
                right --;
            while(left < right && arr[left] <= pivotKey)
                left ++;
            swap(arr, left, right); //把大的交换到右边，把小的交换到左边。
        }
        swap(arr, pivotPointer, left); //最后把pivot交换到中间
        return left;
    }

    public static void quickSort(int[] arr, int left, int right) {
        if(left >= right)
            return ;
        int pivotPos = partition(arr, left, right);
        quickSort(arr, left, pivotPos-1);
        quickSort(arr, pivotPos+1, right);
    }

    public static void sort(int[] arr) {
        if(arr == null || arr.length == 0)
            return ;
        quickSort(arr, 0, arr.length-1);
    }

    public static void swap(int[] arr, int left, int right) {
        int temp = arr[left];
        arr[left] = arr[right];
        arr[right] = temp;
    }

}
```
- 其实上面的代码还可以再优化，上面代码中基准数已经在pivotKey中保存了，所以不需要每次交换都设置一个temp变量，在交换左右指针的时候只需要先后覆盖就可以了。这样既能减少空间的使用还能降低赋值运算的次数。优化代码如下：

```bash
public class QuickSort {

    /**
     * 划分
     * @param arr
     * @param left
     * @param right
     * @return
     */
    public static int partition(int[] arr, int left, int right) {
        int pivotKey = arr[left];

        while(left < right) {
            while(left < right && arr[right] >= pivotKey)
                right --;
            arr[left] = arr[right]; //把小的移动到左边
            while(left < right && arr[left] <= pivotKey)
                left ++;
            arr[right] = arr[left]; //把大的移动到右边
        }
        arr[left] = pivotKey; //最后把pivot赋值到中间
        return left;
    }

    /**
     * 递归划分子序列
     * @param arr
     * @param left
     * @param right
     */
    public static void quickSort(int[] arr, int left, int right) {
        if(left >= right)
            return ;
        int pivotPos = partition(arr, left, right);
        quickSort(arr, left, pivotPos-1);
        quickSort(arr, pivotPos+1, right);
    }

    public static void sort(int[] arr) {
        if(arr == null || arr.length == 0)
            return ;
        quickSort(arr, 0, arr.length-1);
    }

}
```
- 总结快速排序的思想：冒泡+二分+递归分治。

### 堆排序
- 堆排序是借助堆来实现的选择排序，思想同简单的选择排序，以下以大顶堆为例。
- 注意：如果想升序排序就使用大顶堆，反之使用小顶堆。原因是堆顶元素需要交换到序列尾部。
- 如何由一个无序序列键成一个堆？可以直接使用线性数组来表示一个堆，由初始的无序序列建成一个堆就需要自底向上从第一个非叶元素开始挨个调整成一个堆。
- 如何在输出堆顶元素之后，调整剩余元素成为一个新的堆？首先是将堆顶元素和最后一个元素交换。然后比较当前堆顶元素的左右孩子节点，因为除了当前的堆顶元素，左右孩子堆均满足条件，这时需要选择当前堆顶元素与左右孩子节点的较大者（大顶堆）交换，直至叶子节点。我们称这个自堆顶自叶子的调整成为筛选。
- 从一个无序序列建堆的过程就是一个反复筛选的过程。若将此序列看成是一个完全二叉树，则最后一个非终端节点是n/2取底个元素，由此筛选即可。
- 时间复杂度O(nlogn);最坏时间复杂度O(nlogn);空间复杂度O(1)。

```bash
public class HeapSort {

    /**
     * 堆筛选，除了start之外，start~end均满足大顶堆的定义。
     * 调整之后start~end称为一个大顶堆。
     * @param arr 待调整数组
     * @param start 起始指针
     * @param end 结束指针
     */
    public static void heapAdjust(int[] arr, int start, int end) {
        int temp = arr[start];

        for(int i=2*start+1; i<=end; i*=2) {
            //左右孩子的节点分别为2*i+1,2*i+2

            //选择出左右孩子较小的下标
            if(i < end && arr[i] < arr[i+1]) {
                i ++; 
            }
            if(temp >= arr[i]) {
                break; //已经为大顶堆，=保持稳定性。
            }
            arr[start] = arr[i]; //将子节点上移
            start = i; //下一轮筛选
        }

        arr[start] = temp; //插入正确的位置
    }

    public static void heapSort(int[] arr) {
        if(arr == null || arr.length == 0)
            return ;

        //建立大顶堆
        for(int i=arr.length/2; i>=0; i--) {
            heapAdjust(arr, i, arr.length-1);
        }

        for(int i=arr.length-1; i>=0; i--) {
            swap(arr, 0, i);
            heapAdjust(arr, 0, i-1);
        }

    }

    public static void swap(int[] arr, int i, int j) {
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }

}
```

### 希尔排序
- 希尔排序是插入排序的一种高效率的实现，也叫缩小增量排序。
- 简单的插入排序中，如果待排序列是正序时，时间复杂度是O(n)，如果序列是基本有序的，使用直接插入排序效率就非常高。希尔排序就利用了这个特点。
- 基本思想是：先将整个待排记录序列分割成为若干子序列分别进行直接插入排序，待整个序列中的记录基本有序时再对全体记录进行一次直接插入排序。
- 希尔排序是插入排序改良的算法，希尔排序步长从大到小调整，第一次循环后面元素逐个和前面元素按间隔步长进行比较并交换，直至步长为1，步长选择是关键。
- 时间复杂度O(nlogn);最坏时间复杂度O(n^s);空间复杂度O(1)。
```bash
public class ShellSort {

    /**
     * 希尔排序的一趟插入
     * @param arr 待排数组
     * @param d 增量
     */
    public static void shellInsert(int[] arr, int d) {
        for(int i=d; i<arr.length; i++) {
            int j = i - d;
            int temp = arr[i];    //记录要插入的数据  
            while (j>=0 && arr[j]>temp) {  //从后向前，找到比其小的数的位置   
                arr[j+d] = arr[j];    //向后挪动  
                j -= d;  
            }  

            if (j != i - d)    //存在比其小的数 
                arr[j+d] = temp;

        }
    }

    public static void shellSort(int[] arr) {
        if(arr == null || arr.length == 0)
            return ;
        int d = arr.length / 2;
        while(d >= 1) {
            shellInsert(arr, d);
            d /= 2;
        }
    }

}
```

## 归并排序
- 归并排序是另一种不同的排序方法，因为归并排序使用了递归分治的思想，所以理解起来比较容易。
- 其基本思想是，先递归划分子问题，然后合并结果。把待排序列看成由两个有序的子序列，然后合并两个子序列，然后把子序列看成由两个有序序列。
- 倒着来看，其实就是先两两合并，然后四四合并。最终形成有序序列。
- 时间复杂度为O(nlogn)；最坏时间复杂度为O(nlogn);空间复杂度为O(n)。
```bash
public class MergeSort {

    public static void mergeSort(int[] arr) {
        mSort(arr, 0, arr.length-1);
    }

    /**
     * 递归分治
     * @param arr 待排数组
     * @param left 左指针
     * @param right 右指针
     */
    public static void mSort(int[] arr, int left, int right) {
        if(left >= right)
            return ;
        int mid = (left + right) / 2;

        mSort(arr, left, mid); //递归排序左边
        mSort(arr, mid+1, right); //递归排序右边
        merge(arr, left, mid, right); //合并
    }

    /**
     * 合并两个有序数组
     * @param arr 待合并数组
     * @param left 左指针
     * @param mid 中间指针
     * @param right 右指针
     */
    public static void merge(int[] arr, int left, int mid, int right) {
        //[left, mid] [mid+1, right]
        int[] temp = new int[right - left + 1]; //中间数组

        int i = left;
        int j = mid + 1;
        int k = 0;
        while(i <= mid && j <= right) {
            if(arr[i] <= arr[j]) {
                temp[k++] = arr[i++];
            }
            else {
                temp[k++] = arr[j++];
            }
        }

        while(i <= mid) {
            temp[k++] = arr[i++];
        }

        while(j <= right) {
            temp[k++] = arr[j++];
        }

        for(int p=0; p<temp.length; p++) {
            arr[left + p] = temp[p];
        }

    }
}
```

## 计数排序
>    [参考](https://blog.csdn.net/liyuming0000/article/details/46913357)
- 如果在面试中有面试官要求你写一个O(n)时间复杂度的排序算法，你千万不要立刻说：这不可能！
- 虽然前面基于比较的排序的下限是O(nlogn)。
- 但是确实也有线性时间复杂度的排序，只不过有前提条件，就是待排序的数要满足一定的范围的整数，而且计数排序需要比较多的辅助空间。
- 其基本思想是，用待排序的数作为计数数组的下标，统计每个数字的个数。然后依次输出即可得到有序序列。
- 计数排序的时间复杂度和最坏时间复杂度、空间复杂度为O(n+k)。
```bash
- 找出待排序的数组中最大和最小的元素 
- 统计数组中每个值为i的元素出现的次数，存入数组C的第i项 
- 对所有的计数累加（从C中的第一个元素开始，每一项和前一项相加） 
- 反向填充目标数组：将每个元素i放在新数组的第C(i)项，每放一个元素就将C(i)减去1
```
```bash
public class CountSort {

    public static void countSort(int[] arr) {
        if(arr == null || arr.length == 0)
            return ;

        int max = max(arr);

        int[] count = new int[max+1];
        Arrays.fill(count, 0);

        for(int i=0; i<arr.length; i++) {
            count[arr[i]] ++;
        }

        int k = 0;
        for(int i=0; i<=max; i++) {
            for(int j=0; j<count[i]; j++) {
                arr[k++] = i;
            }
        }

    }

    public static int max(int[] arr) {
        int max = Integer.MIN_VALUE;
        for(int ele : arr) {
            if(ele > max)
                max = ele;
        }

        return max;
    }

}
```

## 桶排序
- 桶排序算是计数排序的一种改进和推广，但是网上有许多资料把计数排序和桶排序混为一谈。其实桶排序要比计数排序复杂许多。
- 桶排序是计数排序的变种，把计数排序中相邻的m个”小桶”放到一个”大桶”中，在分完桶后，对每个桶进行排序（一般用快排），然后合并成最后的结果。
- 桶排序的平均时间复杂度为线性的O(N+C)，其中C=N*(logN-logM)。如果相对于同样的N，桶数量M越大，其效率越高，最好的时间复杂度达到O(N)。 当然桶排序的空间复杂度 为O(N+M)，如果输入数据非常庞大，而桶的数量也非常多，则空间代价无疑是昂贵的。此外，桶排序是稳定的。
```bash
假设有一组长度为N的待排关键字序列K[1....n]。首先将这个序列划分成M个的子区间(桶) 。
然后基于某种映射函数 ，将待排序列的关键字k映射到第i个桶中(即桶数组B的下标 i) ，那么该关键字k就作为B[i]中的元素(每个桶B[i]都是一组大小为N/M的序列)。接着对每个桶B[i]中的所有元素进行比较排序(可以使用快排)。然后依次枚举输出B[0]….B[M]中的全部内容即是一个有序序列。
bindex=f(key)   其中，bindex 为桶数组B的下标(即第bindex个桶), k为待排序列的关键字。桶排序之所以能够高效，其关键在于这个映射函数，它必须做到：如果关键字k1<k2，那么f(k1)<=f(k2)。也就是说B(i)中的最小数据都要大于B(i-1)中最大数据。很显然，映射函数的确定与数据本身的特点有很大的关系。
```
```bash
public class BucketSort {

    public static void bucketSort(int[] arr) {
        if(arr == null && arr.length == 0)
            return ;

        int bucketNums = 10; //这里默认为10，规定待排数[0,100)
        List<List<Integer>> buckets = new ArrayList<List<Integer>>(); //桶的索引

        for(int i=0; i<10; i++) {
            buckets.add(new LinkedList<Integer>()); //用链表比较合适
        }

        //划分桶
        for(int i=0; i<arr.length; i++) {
            buckets.get(f(arr[i])).add(arr[i]);
        }

        //对每个桶进行排序
        for(int i=0; i<buckets.size(); i++) {
            if(!buckets.get(i).isEmpty()) {
                Collections.sort(buckets.get(i)); //对每个桶进行快排
            }
        }

        //还原排好序的数组
        int k = 0;
        for(List<Integer> bucket : buckets) {
            for(int ele : bucket) {
                arr[k++] = ele;
            }
        }
    }

    /**
     * 映射函数
     * @param x
     * @return
     */
    public static int f(int x) {
        return x / 10;
    }

}
```


## 基数排序
- 基数排序又是一种和前面排序方式不同的排序方式，基数排序不需要进行记录关键字之间的比较。
- 基数排序是一种借助多关键字排序思想对单逻辑关键字进行排序的方法。
- 所谓的多关键字排序就是有多个优先级不同的关键字。
- 比如说成绩的排序，如果两个人总分相同，则语文高的排在前面，语文成绩也相同则数学高的排在前面。
-如果对数字进行排序，那么个位、十位、百位就是不同优先级的关键字，如果要进行升序排序，那么个位、十位、百位优先级一次增加。
- 基数排序是通过多次的收分配和收集来实现的，关键字优先级低的先进行分配和收集。
- 时间复杂度O(N*M);最坏时间复杂度O(N*M);空间复杂度O(M)。

```bash
public class RadixSort {

    public static void radixSort(int[] arr) {
        if(arr == null && arr.length == 0)
            return ;

        int maxBit = getMaxBit(arr);

        for(int i=1; i<=maxBit; i++) {

            List<List<Integer>> buf = distribute(arr, i); //分配
            collecte(arr, buf); //收集
        }

    }

    /**
     * 分配
     * @param arr 待分配数组
     * @param iBit 要分配第几位
     * @return
     */
    public static List<List<Integer>> distribute(int[] arr, int iBit) {
        List<List<Integer>> buf = new ArrayList<List<Integer>>();
        for(int j=0; j<10; j++) {
            buf.add(new LinkedList<Integer>());
        }
        for(int i=0; i<arr.length; i++) {
            buf.get(getNBit(arr[i], iBit)).add(arr[i]);
        }
        return buf;
    }

    /**
     * 收集
     * @param arr 把分配的数据收集到arr中
     * @param buf 
     */
    public static void collecte(int[] arr, List<List<Integer>> buf) {
        int k = 0;
        for(List<Integer> bucket : buf) {
            for(int ele : bucket) {
                arr[k++] = ele;
            }
        }

    }

    /**
     * 获取最大位数
     * @param x
     * @return
     */
    public static int getMaxBit(int[] arr) {
        int max = Integer.MIN_VALUE;
        for(int ele : arr) {
            int len = (ele+"").length();
            if(len > max)
                max = len;
        }
        return max;
    }

    /**
     * 获取x的第n位，如果没有则为0.
     * @param x
     * @param n
     * @return
     */
    public static int getNBit(int x, int n) {

        String sx = x + "";
        if(sx.length() < n)
            return 0;
        else
            return sx.charAt(sx.length()-n) - '0';
    }

}
```

## 总结
- ![sorts](https://raw.githubusercontent.com/PaulGuo5/Webnotes/master/img/sorts.png)


