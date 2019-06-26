# 用LinkedList实现栈  
![](https://img.shields.io/badge/Stack-DataStructures-orange.svg)

<br>

# 解题思路
- 使用 LinkedList 可以实现很多队列、栈的数据结构，并且有很多方法很类似，但是有细小的差别
- getFirst 和 element 都返回列表的头，但是不删除它，如果列表为空，抛出异常 
- peek 实现的功能一样，但是列表为空时返回 null
- removeFirst 和 remove 都是删除并返回列表的头，如果列表为空抛出异常
- pool 实现的功能一样，但是列表为空时返回 null

<br>

# 最优解
	public class MyStack<T> {
	    private LinkedList<T> storage = new LinkedList<T>();
	
	    // 入栈
	    public void push(T v){
	        storage.addFirst(v);
	    }
	    
	    // 获得栈顶
	    public T peek(){
	        return storage.getFirst();
	    }
	
	    // 出栈
	    public T pop(){
	        return storage.removeFirst();
	    }
	    
	    // 栈是否为空
	    public boolean empty(){
	        return storage.isEmpty();
	    }
	
	    // 打印
	    public String toString(){
	        return storage.toString();
	    }
	}

