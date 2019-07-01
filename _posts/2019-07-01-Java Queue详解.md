## Java的Queue接口

Queue是java.util包中提供的接口，它是一个先进先出结构（FIFO first in first out），也实现了Collection接口，因此也属于集合类。   

它总共有6个方法：add()、offer()、remove()、pull()、element()
、peek()，主要有2个实现类LinkedList和PriorityQueue，还有1个Deque接口（双端队列）。

源码如下：

```
public interface Queue<E> extends Collection<E> {

  boolean add(E e);
  
  boolean offer(E e);
  
  E remove();
  
  E poll();
  
  E element();
  
  E peek();
}
```


### 方法解析
添加元素：`add`、`offer`

  * 相同点：未超过容量，在队列尾部加入元素，返回true
  * 不同点：超过容量，add()方法会抛出异常，offer()方法返回false

删除元素：`remove`、`poll`

  * 相同点：容量大于0的时候，删除并返回队头删除的元素
  * 不同点：容量为0的时候，romove()会抛出异常，poll()返回null

获取队头元素，不删除：`element`、`peek`

  * 相同点：容量大于0的时候，返回队头的元素，不删除
  * 不同点：容量为0的时候，element()会抛出异常，peek()返回null

### 实现类
1. **PriorityQueue**  
PriorityQueue保存队列元素的顺序不是按照元素添加的顺序来保存的，而是在添加元素的时候对元素的大小排序后再保存的。因此在PriorityQueue中使用peek()或pool()取出队列中头部的元素，取出的不是最先添加的元素，而是最小的元素。

	PriorityQueue不允许插入null元素，它还需要对队列元素进行排序，PriorityQueue有两种排序方式：
	
	自然排序：采用自然排序的PriorityQueue集合中的元素必须实现Comparator接口，而且应该是一个类的多个实例，否则可能导致ClassCastException异常。
	
	定制排序：创建PriorityQueue队列时，传入一个Comparable对象，该对象负责对所有队列中的所有元素进行排序。采用定制排序不要求必须实现Comparator接口。

	  ```
	  /**
	  * @author mx
	  * @date 2019/7/1 5:07 PM
	  */
	  public class PriorityQueueTest {
	  
	    public static void main(String[] args) {
	  
	      PriorityQueue queue = new PriorityQueue();
	        queue.add(6);
	        queue.offer(-3);
		     queue.add(20);
		     queue.offer(18);
		
	  //        queue.add("123");
	  //        queue.offer("c");
	  //        queue.add("bsdsada");
	  //        queue.add(null);
		
	        while (queue.size() > 0) {
	          System.out.println(queue.remove());
	        }
	    }
	  }
	  ```  
	  
	  ```
	  //输出结果
	  -3
	  6
	  18
	  20
	  ```
	
2. **Deque**  
Deque为Queue的一个子接口，代表了一个双端队列，Deque定义了一些方法：
  
  ```
  void addFirst(Object e): 　　将指定元素添加到双端队列的头部。

  void addLast(Object e):　　将指定元素添加到双端队列的尾部。
	
  Iterator descendingItrator():　　返回该双端队列对应的迭代器，该迭代器以逆向顺序来迭代队列中的元素。
	
  Object getFirst():　　获取但不删除双端队列的第一个元素。
	
  Object getLast():　　获取但不删除双端队列的最后一个元素。
	
  boolean offFirst(Object e):　　将指定元素添加到双端队列的头部。
	
  boolean offLast(OBject e):　　将指定元素添加到双端队列的尾部。
	
  Object peekFirst():　　获取但不删除双端队列的第一个元素；如果双端队列为空，则返回null。
	
  Object PeekLast():　　获取但不删除双端队列的最后一个元素；如果双端队列为空，则返回null。
	
  Object pollFirst():　　获取并删除双端队列的第一个元素；如果双端队列为空，则返回null。
	
  Object pollLast():　　获取并删除双端队列的最后一个元素；如果双端队列为空，则返回null。
	
  Object pop()(栈方法):　　pop出该双端队列所表示的栈的栈顶元素。相当于removeFirst()。
	
  void push(Object e)(栈方法)：　　将一个元素push进该双端队列所表示的栈的栈顶。相当于addFirst()。
	
  Object removeFirst():　　获取并删除该双端队列的第一个元素。
	
  Object removeFirstOccurence(Object o):　　删除该双端队列的第一次出现的元素o。
	
  Object removeLast():　　获取并删除该双端队列的最后一个元素o。
	
  Object removeLastOccurence(Object o):　　删除该双端队列的最后一次出现的元素o。
  ```
  
  ArrayDeque例子：
  
  ```
  /**
   * @author mx
   * @date 2019/7/1 5:37 PM
   */
  public class ArrayDequeTest {

    public static void main(String[] args) {

      ArrayDeque<Object> deque = new ArrayDeque<>();

        deque.add("tom");
        deque.offer("lucy");
        deque.offer("jack");

        System.out.println(deque);
        //输出[tom, lucy, jack]

        System.out.println(deque.peek());
        //输出tom

        System.out.println(deque);
        //输出[tom, lucy, jack]

        System.out.println(deque.poll());
        //输出tom

        System.out.println(deque);
        //输出[lucy, jack]
      }
  }
  ```
  
  ArrayDeque作为Stack例子：
  
  ```
  /**
  * @author mx
  * @date 2019/7/1 5:58 PM
  */
	public class DequeStack {
	
    /**
     * 利用ArrayDeque模拟栈的操作
     * @param args
     */
      public static void main(String[] args) {
        ArrayDeque<Object> deque = new ArrayDeque<>();
	
        deque.push("Ironman");
	
        deque.push("American Captain");
	
        deque.push("Thor");
	
        System.out.println(deque);
        //输出[Thor, American Captain, Ironman]
	
        System.out.println(deque.peek());
        //输出Thor
	
        System.out.println(deque);
        //输出[Thor, American Captain, Ironman]
	
        System.out.println(deque.pop());
        //输出Thor
	
        System.out.println(deque);
        //输出[American Captain, Ironman]
	
        System.out.println(deque.pop());
        //输出American Captain
	
        System.out.println(deque);
        //输出[Ironman]
      }
  }
  ```
  
3. **LindedList** 

  源码如下：
  
  ```
  public class LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable {
    
  }
  ```
  
  可以看到，LindedList是List和Deque的实现，因此它是一个集合，可以根据索引来随机访问集合中的数据。此外，它还是Deque接口的实现类，因此也可以作为一个双端队列，或栈来使用。   
  LinkedList与ArrayList、ArrayDeque实现不同，ArrayList和ArrayDeque内部以数组的形式类保存集合中的元素，因此随机访问时有很好的性能；而LindedList是以链表的形式来保存集合中的元素的，随机访问性能较差，但是插入和删除元素的性能很好。
  虽然Vector也是以数组形式来保存集合的元素，但是它实现了线程同步，因此随机读取，插入和删除性能都不太好。