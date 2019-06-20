# 1 用两个栈模拟队列
![](https://img.shields.io/badge/Stack-DataStructures-orange.svg)<br>
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


