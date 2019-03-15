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




