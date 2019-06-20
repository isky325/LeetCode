# 1 用两个栈模拟队列
用两个栈来实现一个队列，完成队列的Push和Pop操作。 队列中的元素为int类型 

# 1.1 思路
堆栈的特性是后入先出(LIFO),而队列的特性是先入先出(FIFO),这里，我们标记两个栈分别为S1,S2,入栈操就是往S1里push,出栈操作的话,这里要先判断S1是否为空,若为空,则我们需要将S1所有节点都输出到S2中,然后S2再输出,若不为空，则直接将S2进行pop()操作即可.<br>
● 由于栈是先进后出的，那么经过两次的入栈则会变为先进先出.

**异常判断**:

1. 当S1，S2皆为空时，进行pop()操作，应该报错。
2. 当S1，S2皆是满的时，我们进行Push()，应该报错吗？


#1.2 程序1
左程云的《程序员代码面试指南》的答案

	public class Solution {
	    Stack<Integer> stack1 = new Stack<Integer>();
	    Stack<Integer> stack2 = new Stack<Integer>();
	    
	    public void push(int node) {
	        stack1.push(node);
	    }
	    
	    public int pop() {
	    	if(stack1.isEmpty()&&stack2.isEmpty()){ // 如果s1和s2都空，则报异常
	            throw new RuntimeException();
	        }
	        else if(stack2.isEmpty()){ //若不为空，则直接将S2进行pop()
	            while(!stack1.isEmpty()){ //若s1不空的情况下，一直弹出元素
	                stack2.push(stack1.pop());
	            }
	        }
	        return stack2.pop();
	    }
	}

这里相当于把stack1当入口，把stack2当出口，新的数据只会进入stack1,而弹出的数据只会从stack2中弹出，互不影响；相当于把两个栈低连在一起形成队列!<br>

栈1为空，有两种情况：<br>
1. 栈2也为空，抛出异常；<br>
2. 栈2不为空，return语句，栈2弹出<br>

栈1不为空，有两种情况：<br>
1. 栈2也为空，栈1的数据出栈，同时进入栈2；<br>
2. 栈2不为空，栈1数据不变，等栈2return语句弹出所有语句，栈2弹出<br>

这种方法唯一的缺点就是，当push操作积攒的数据过多时，下一次pop操作的开销会变得很大，从而导致pop操作的开销不稳定；解决方法应该是，在push中设置阀值，以提升pop操作的稳定性（虽然在这里有点多余，但也可以作为一种思想分享一下）<br>

缺点分析的没问题，但是设置阈值不可取，你想，只有stack2为空时，才能从stack1中取数据，如果stack2不为空的话就从stack1中取数据会出错；而且，从stack1中取数据时必须全部取出来，否则取不到栈底的数据也会出错。<br><br><br>


#2 滑动窗口的最大值
给定一个数组和滑动窗口的大小，找出所有滑动窗口里数值的最大值。<br>

例如，如果输入数组{2,3,4,2,6,2,5,1}及滑动窗口的大小3，那么一共存在6个滑动窗口，他们的最大值分别为{4,4,6,6,6,5}； 针对数组{2,3,4,2,6,2,5,1}的滑动窗口有以下6个： {[2,3,4],2,6,2,5,1}， {2,[3,4,2],6,2,5,1}， {2,3,[4,2,6],2,5,1}， {2,3,4,[2,6,2],5,1}， {2,3,4,2,[6,2,5],1}， {2,3,4,2,6,[2,5,1]}。




## 2.1 我的方法
两个循环，外循环控制滑动窗口的个数，内循环判断大小，复杂度挺高。


	import java.util.*; // 这一句话不要少，否则报错
	public class Solution {
	    public ArrayList<Integer> maxInWindows(int [] num, int size)
	    {
	        if(num.length == 0 || size < 0){
	            return null;
	        }
	        ArrayList<Integer> list = new ArrayList<>();
	        
	        if(size == 0 || num.length < size){
	            return list;
	        }
	        int loopnum = num.length - size;
	        for(int i=0; i<=loopnum; i++){
	            int temp = num[i];
	            for(int j=i+1; j<i+size; j++){
	                if(temp < num[j]){
	                    temp = num[j];
	                }
	            }
	            list.add(temp);
	        }
	        return list;
	    }
	}

运行时间：17ms；占用内存：9312k

## 2.2 双端列表
滑动窗口应当是队列，但为了得到滑动窗口的最大值，队列序可以从两端删除元素，因此使用双端队列。<br>

### 2.2.1
用一个双端队列，队列第一个位置保存当前窗口的最大值，当窗口滑动一次<br>
1. 判断当前最大值是否过期<br>
2. 新增加的值从队尾开始比较，把所有比他小的值丢掉<br>


	import java.util.*;
	public class Solution {
	    public ArrayList<Integer> maxInWindows(int [] num, int size)
	    {
	        ArrayList<Integer> res = new ArrayList<>();
	        if(size == 0) return res;
	        int begin; 
	        ArrayDeque<Integer> q = new ArrayDeque<>();
	        for(int i = 0; i < num.length; i++){
	            begin = i - size + 1;
	            if(q.isEmpty())
	                q.add(i);
	            else if(begin > q.peekFirst())
	                q.pollFirst();
	         
	            while((!q.isEmpty()) && num[q.peekLast()] <= num[i])
	                q.pollLast();
	            q.add(i);  
	            if(begin >= 0)
	                res.add(num[q.peekFirst()]);
	        }
	        return res;
	    }
	}

运行时间：26ms 占用内存：9516k


### 2.2.2
对新来的元素k，将其与双端队列中的元素相比较<br>
1）前面比k小的，直接移出队列（因为不再可能成为后面滑动窗口的最大值了!）<br>
2）前面比k大的X，比较两者下标，判断X是否已不在窗口之内，不在了，直接移出队列<br>
队列的第一个元素是滑动窗口中的最大值


	import java.util.*;
	public class Solution {
	    public ArrayList<Integer> maxInWindows(int [] num, int size)
	    {
	        ArrayList<Integer> ret = new ArrayList<>();
	        if (num == null || num.length < size || size < 1) {
	            return ret;
	        }
	         
	        LinkedList<Integer> indexDeque = new LinkedList<>();
	        for (int i = 0; i < size - 1; i++) {
	            while (!indexDeque.isEmpty() && num[i] > num[indexDeque.getLast()]) {
	                indexDeque.removeLast();
	            }
	            indexDeque.addLast(i);
	        }
	         
	        for (int i = size - 1; i < num.length; i++) {
	            while (!indexDeque.isEmpty() && num[i] > num[indexDeque.getLast()]) {
	                indexDeque.removeLast();
	            }
	            indexDeque.addLast(i);
	            if (i - indexDeque.getFirst() + 1 > size) {
	                indexDeque.removeFirst();
	            }
	            ret.add(num[indexDeque.getFirst()]);
	        }
	        return ret;
	    }
	}
运行时间：22ms 占用内存：9340k


	import java.util.*;
	public class Solution {
	    public ArrayList<Integer> maxInWindows(int [] num, int size) {
	        if (num == null || num.length == 0 || size <= 0 || num.length < size) {
	            return new ArrayList<Integer>();
	        }
	        ArrayList<Integer> result = new ArrayList<>();
	        //双端队列，用来记录每个窗口的最大值下标
	        LinkedList<Integer> qmax = new LinkedList<>();
	        int index = 0;
	        for (int i = 0; i < num.length; i++) {
	            while (!qmax.isEmpty() && num[qmax.peekLast()] < num[i]) {
	                qmax.pollLast();
	            }
	            qmax.addLast(i);
	            //判断队首元素是否过期
	            if (qmax.peekFirst() == i - size) {
	                qmax.pollFirst();
	            }
	            //向result列表中加入元素
	            if (i >= size - 1) {
	                result.add(num[qmax.peekFirst()]);
	            }
	        }
	        return result;
	    }
	}



#3