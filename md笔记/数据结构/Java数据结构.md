Java数据结构和算法

包括：线性结构、非线性结构

**线性结构**

- 线性结构作为最常用的数据结构，其特点是数据元素之间存在一对一的线性关系；
- 线性结构有两种不同的存储结构，即顺序存储结构(数组)和链式存储结构(链表)；
  - 顺序存储的线性表称为顺序表，顺序表中的存储元素是连续的；
  - 链式存储的线性表称为链表，链表中的存储元素不一定是连续的，元素节点中存放数据元素以及相邻元素的地址信息。
-  线性结构常见的有：数组、队列、链表和栈。

**非线性结构**

- 非线性结构包括：二维数组，多维数组，广义表，树结构，图结构。

## 1、稀疏数组

- 稀疏数组可以看做是普通数组的压缩，但是这里说的普通数组是值无效数据量远大于有效数据量的数组
  - 原数组中存在大量的无效数据，占据了大量的存储空间，真正有用的数据却少之又少；
  - 压缩存储可以节省存储空间以避免资源的不必要的浪费，在数据序列化到磁盘时，压缩存储可以提高IO效率。

- 当一个数组中大部分元素为０，或者为同一个值的数组时，可以使用稀疏数组来保存该数组。

```java
import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStream;
import java.io.OutputStreamWriter;
import java.io.UnsupportedEncodingException;

public class SparseArray {

	public static void main(String[] args) throws IOException {
		int[][] arr = new int[11][11];
		arr[1][2] = 1;
		arr[5][3] = 2;
		arr[7][7] = 9;
		System.out.println("原始数组：");
		print(arr);
		System.out.println("稀疏数组：");
		int[][] sparseArr = changeToSparseArray(arr);
		print(sparseArr);
		save(sparseArr);
		System.out.println("读取到的稀疏数组：");
		int[][] sparseArr2=find();		
		print(sparseArr2);
		System.out.println("转化为原始数组：");
		int[][] arr2=changeToArr(sparseArr2);
		print(arr2);
	}

	private static int[][] changeToArr(int[][] sparseArr) {
		int[][] arr=new int[sparseArr[0][0]][sparseArr[0][1]];
		for (int i = 1; i < sparseArr.length; i++) {
			arr[sparseArr[i][0]][sparseArr[i][1]]=sparseArr[i][2];
		}
		return arr;
	}

	// 原始转化为稀疏数组
	public static int[][] changeToSparseArray(int[][] arr) {
		int count = 0;// 计数器
		// 遍历获得非零元素数
		for (int i = 0; i < arr.length; i++) {
			for (int j = 0; j < arr[i].length; j++) {
				if (arr[i][j] != 0) {
					count++;
				}
			}
		}
		// 转换
		int[][] sparseArr = new int[count + 1][3];
		sparseArr[0][0] = arr.length;
		sparseArr[0][1] = arr[0].length;
		sparseArr[0][2] = count;
		count = 1;
		for (int i = 1; i < arr.length; i++) {
			for (int j = 0; j < arr[i].length; j++) {
				if (arr[i][j] != 0) {
					sparseArr[count][0] = i;
					sparseArr[count][1] = j;
					sparseArr[count][2] = arr[i][j];
					count++;
				}
			}
		}
		return sparseArr;
	}

	// 存盘
	public static void save(int[][] sparseArr) throws IOException {
		String path = "D://save.txt";
		OutputStream outputStream = new FileOutputStream(new File(path));
		OutputStreamWriter outputStreamWriter = new OutputStreamWriter(outputStream, "utf-8");
		BufferedWriter bufferedWriter = new BufferedWriter(outputStreamWriter);
		for (int i = 0; i < sparseArr.length; i++) {
			for (int j = 0; j < sparseArr[i].length; j++) {
				bufferedWriter.write(sparseArr[i][j] + " ");
			}
			bufferedWriter.write("\n");
		}
		bufferedWriter.close();
		outputStreamWriter.close();
		outputStream.close();
	}

	// 读取磁盘
	public static int[][] find(){
		String path = "D://save.txt";
		int[][] sparseArr=null;
		try(BufferedReader bufferedReader=new BufferedReader(new InputStreamReader(new FileInputStream(path),"utf-8"))) {
			String line=bufferedReader.readLine();
			int[] tempArr=stringToInt(line);
			sparseArr=new int[tempArr[2]+1][3];
			sparseArr[0]=tempArr;
			int count=1;
			while((line=bufferedReader.readLine())!=null) {
				sparseArr[count]=stringToInt(line);
				count++;
			}
		} catch (UnsupportedEncodingException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (FileNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (IOException e1) {
			// TODO Auto-generated catch block
			e1.printStackTrace();
		}
		return sparseArr;
	}
	
	//字符串转化为数组
	public static int[] stringToInt(String str) {
		String []arr=str.split(" ");
		int[] arr2=new int[arr.length];
		for (int i = 0; i < arr.length; i++) {
			arr2[i]=Integer.parseInt(arr[i]);
		}
		return arr2;
	}

	// 输出二维数组
	public static void print(int[][] arr) {
		for (int i = 0; i < arr.length; i++) {
			for (int j = 0; j < arr[i].length; j++) {
				System.out.printf("%d\t", arr[i][j]);
			}
			System.out.println();
		}
	}

}

```

## 2、队列

- 队列是一种特殊的线性表，特殊之处在于它只允许在表的前端（front）进行删除操作，而在表的后端（rear）进行插入操作，和栈一样，队列是一种操作受限制的线性表。进行插入操作的端称为队尾，进行删除操作的端称为队头。
- 队列是一种基于先进先出（FIFO）策略的集合类型。

数组实现队列出现的问题：

1. 为什么用环形队列:如果不是环形队列：我们每次取出对头元素时，如果font指针向后挪动一位，取出的元素位置将无法再插入值；如果不移动font指针，就需要将整个数组向前挪动一位。前者造成了极大的空间浪费，后者又带来了过多的性能浪费。
2. 为什么需要一个空节点：如果不留一个空节点，就无法直接判断队列是满还是空。n个元素的队列可能出现n+1种可能：如一个三个元素的队列font-rear：0-0（空）、0-1、0-2、0-0（满）,而如果引入一个空节点就正好有n+1个元素对应n+1种可能（rear指向下一个节点）：0-0（空）、0-1、0-2、0-3（满）。（当然，可以额外用一个flag值来判断是否为空）。

```java
import java.util.Scanner;

public class MyQueueTest {
    public static void main(String[] args) {
        MyQueue myQueue = new MyQueue(4);
        boolean flag = true;
        Scanner input = new Scanner(System.in);
        while (flag) {
            System.out.println("s(show):显示队列\n" +
                    "e(sxit):退出程序\n" +
                    "a(add):添加数据到队列\n" +
                    "g(get):从队列取出数据\n" +
                    "h(head):查看队头的数据");
            String selection = input.next();
            switch (selection) {
                case "a":
                    System.out.println("请输入一个数：");
                    int a = input.nextInt();
                    myQueue.add(a);
                    break;
                case "s":
                    myQueue.showQueue();
                    break;
                case "h":
                    System.out.println(myQueue.headQueue());
                    break;
                case "g":
                    System.out.println("取出元素："+myQueue.peek());
                    break;
                case "e":
                    flag=false;
                    break;
                default:
                    System.out.println("请输入正确格式的选择：");
                    break;
            }
        }
    }
}

class MyQueue {
    private int front = 0;
    private int rear = 0;
    private int maxSize;
    private int[] queueArray;

    MyQueue(int len) {
        maxSize = len;
        queueArray = new int[len];
    }

    //判断队列是否为空
    public boolean isEmpty() {
        if (rear == front) {
            return true;
        }
        return false;
    }

    //判断队列是否已满
    public boolean isFull() {
        if ((rear + 1) % maxSize == front) {
            return true;
        }
        return false;
    }

    //展示整个队列
    public void showQueue() {
        if (isEmpty()) {
            System.out.println("队列为空，无法显示");
            return;
        }
        for (int i = front; i < front + size(); i++) {
            System.out.printf("a[%d]=%d\n",i,queueArray[i]);
        }
    }

    //返回队列元素数
    private int size() {
        return (rear - front + maxSize) % maxSize;
    }

    //添加队列元素
    public void add(int num) {
        if (isFull()) {
            System.out.println("队列已满，无法添加");
            return;
        }
        queueArray[rear]=num;
        rear=(rear+1)%maxSize;
    }

    //取出队列头元素
    public int peek() {
        if (isEmpty()) {
            throw new RuntimeException("队列为空，无法取出");
        }
        int value = queueArray[front];
        front=(front+1)%maxSize;
        return value;
    }

    //返回队头元素
    public int headQueue() {
        if (isEmpty()) {
            throw new RuntimeException("队列为空，无法返回");
        }
        return queueArray[front];
    }


}
```

## 3、链表

链表是有序的列表

### 3.1 单向链表

- 内存存储示意图：

![image-20200420111446374](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200420111446374.png)

- 逻辑结构示意图：

![image-20200420111325757](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200420111325757.png)

- 链表是以结点的形式存储，是链式存储;
- 每个节点都包含data域、next域：指向下一个节点;
- 链表的各个节点不一定是连续存储；
- 链表分带头结点的链表和没有头节点的链表，根据实际需求确定使用。

单链表实现出现的问题：

1. 为什么headNode不能改动，而需要每次定义一个temp实现功能：因为一旦headNode位置变化，就无法定位到单链表头节点，因为单链表通常情况下只能从头到尾单向遍历。
2. 为什么插入需要找到插入位置的前一位而不是后一位：如果找的是后一位，虽然node.next可以指向后一位，但无法找出前一位指向node,原因同上。
3. 单链表反转：先定义一个reserveNode的临时头结点，标记单链表第一个结点的next结点，然后取出第一个结点，让该结点指向reserveHeadNode的next结点，最后让reserveNode指向该结点，后续结点以此类推。最后，让headNode头节点指向reserveHeadNode的next结点。
4. 单链表逆序输出：利用栈的先进后出原理。

```java
package com.dreamin.first;

import java.util.Stack;

public class MyLinkListTest {

    public static void main(String[] args) {
        MyLinkList myLinkList = new MyLinkList();
        System.out.println("单链表已创建");
        Node node1 = new Node(1, "张三");
        Node node2 = new Node(2, "李四");
        Node node3 = new Node(3, "王五");
        myLinkList.addByOrder(node2);
        myLinkList.addByOrder(node3);
        myLinkList.addByOrder(node1);
        myLinkList.addByOrder(node2);
        myLinkList.show();
        Node node4 = new Node(3, "王小五");
        myLinkList.update(node4);
        myLinkList.show();
        myLinkList.delete(2);
        myLinkList.show();
        System.out.println("单链表节点数:" + nodeNum(myLinkList.getHeadNode()));
        System.out.println(getValue(1, myLinkList.getHeadNode()));
        reserve(myLinkList.getHeadNode());
        System.out.println("倒转后：");
        myLinkList.show();
        System.out.println("逆序遍历：");
        reservePrint(myLinkList.getHeadNode());

    }

    //获取单链表节点数
    public static int nodeNum(Node temp) {
        int num = 0;
        while (true) {
            if (temp.next == null) {
                break;
            }
            num++;
            temp = temp.next;
        }
        return num;
    }

    //获取单链表倒数第k个元素
    public static Node getValue(int k, Node temp) {
        if (temp.next == null) {
            return null;
        }
        int length = nodeNum(temp);
        int index = length - k;
        if (index < 0) {
            return null;
        }
        for (int i = 0; i <= index; i++) {
            temp = temp.next;
        }
        return temp;
    }

    //单链表反转
    public static void reserve(Node headNode) {
        if(headNode==null||headNode.next==null){
            return;
        }
        Node temp = headNode;
        Node reserveHeadNode = new Node(0, "");
        temp = temp.next;
        Node next = null;
        while (temp != null) {
            next = temp.next;
            temp.next = reserveHeadNode.next;
            reserveHeadNode.next = temp;
            temp = next;
        }
        headNode.next = reserveHeadNode.next;
    }

    //逆序遍历打印输出
    public static void reservePrint(Node headNode){
        Stack<Node> nodeStack=new Stack<>();
        if(headNode==null||headNode.next==null){
            System.out.println("链表为空，无法输出");
            return;
        }
        Node temp=headNode;
        while (temp.next!=null){
            nodeStack.add(temp.next);
            temp=temp.next;
        }
        while (!nodeStack.empty()){
            System.out.println(nodeStack.pop());
        }
    }

}


//创建单链表类
class MyLinkList {
    //建立头节点
    private Node headNode = new Node(0, "");//头节点不需要赋任何具有实际意义的值,也不需要改动位置

    public Node getHeadNode() {
        return headNode;
    }

    //通过顺序添加节点
    public void addByOrder(Node node) {
        Node temp = headNode;//获取头节点
        boolean flag = false;//待节点是否已经存在
        while (true) {
            if (temp.next == null) {
                break;//为空时退出
            }
            if (temp.next.id > node.id) {
                break;//找出待插入节点位置的前一个节点
            } else if (temp.next.id == node.id) {
                flag = true;//判断节点是否已存在
                break;
            }
            temp = temp.next;
        }
        if (flag == false) {//如果该节点不存在，执行插入操作
            node.next = temp.next;
            temp.next = node;
            System.out.println("结点" + node.id + "已插入");
        } else {
            System.out.println("结点" + node.id + "已存在");
        }
    }

    //修改节点信息
    public void update(Node node) {
        Node temp = headNode;
        boolean flag = false;//判断节点是否存在
        while (true) {
            if (temp == null) {
                System.out.println("对应节点不存在，无法修改");
                break;
            }
            if (temp.id == node.id) {
                temp.name = node.name;
                break;
            }
            temp = temp.next;
        }
    }

    //删除节点信息
    public void delete(int id) {
        Node temp = headNode;
        boolean flag = false;//判断节点是否存在
        while (true) {
            if (temp.next == null) {
                System.out.println("对应节点不存在，无法删除");
                break;
            }
            if (temp.next.id == id) {
                temp.next = temp.next.next;
                System.out.println("节点已删除");
                break;
            }
            temp = temp.next;
        }
    }

    //显示链表
    public void show() {
        Node temp = headNode;
        if (temp.next == null) {
            System.out.println("链表为空，无法显示");
            return;
        }
        temp = temp.next;
        while (true) {
            if (temp == null) {
                break;//到达链表末尾
            }
            System.out.println(temp.toString());
            temp = temp.next;
        }
    }


}


//创建节点类
class Node {
    public int id;
    public String name;
    public Node next;

    public Node(int id, String name) {
        this.id = id;
        this.name = name;
    }

    @Override
    public String toString() {
        return "Node{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", next=" + next +
                '}';
    }
}
```

### 3.2 双向链表

管理单向链表的缺点分析:

- 单向链表，查找的方向只能是一个方向，而双向链表可以向前或者向后查找；
- 单向链表不能自我删除，需要靠辅助结点，而双向链表，则可以自我删除，所以前面我们单链表删除
  时结点，总是找到temp,temp 是待删除结点的前一个节点。

双向链表实现的问题：

1. 添加问题：添加可能出现两种情况，可能从中间插入，也可能从末尾插入，需要分情况操作；
2. 删除问题：删除也可能出现两种情况，可能在中间删除，也可能从末尾删除，因为如果不分情况，末尾结点temp的next为空，你将temp.next.pre置为temp.next会报空指针异常。

具体实现：（修改方式与但链表基本一致）

```java
public class MyDoubleLinkListTest {
    public static void main(String[] args) {
        MyDoubleLinkList myDoubleLinkList=new MyDoubleLinkList();
        DoubleNode node1=new DoubleNode(1,"张三");
        DoubleNode node2=new DoubleNode(2,"李四");
        DoubleNode node3=new DoubleNode(3,"王五");
        myDoubleLinkList.add(node1);
        myDoubleLinkList.add(node3);
        myDoubleLinkList.add(node2);
        System.out.println("添加后：");
        myDoubleLinkList.show();
        myDoubleLinkList.peek(4);
        System.out.println("删除后：");
        myDoubleLinkList.show();
        myDoubleLinkList.peek(2);
        System.out.println("删除后：");
        myDoubleLinkList.show();
        myDoubleLinkList.peek(3);
        System.out.println("删除后：");
        myDoubleLinkList.show();
    }
}

class MyDoubleLinkList {
    private DoubleNode headNode=new DoubleNode(0,"");//头结点

    public DoubleNode getHeadNode() {
        return headNode;
    }

    //添加节点
    public void add(DoubleNode node){
        DoubleNode temp=headNode;
        boolean flag=true;//判断是不是在链表末端添加
        while(temp.next!=null){
            if(temp.next.id>node.id){
                flag=false;
                break;
            }else if(temp.next.id==node.id){
                System.out.println("该结点已存在，无法添加");
                return;
            }
            temp=temp.next;
        }
        if(flag==false){
            node.next=temp.next;
            node.pre=temp;
            node.next.pre=node;
            temp.next=node;
        }else{
            temp.next=node;
            node.pre=temp;
        }

        System.out.println("添加成功");
    }

    //删除节点
    public void peek(int id){
        if(headNode==null||headNode.next==null){
            System.out.println("链表为空，无法删除");
        }
        DoubleNode temp=headNode.next;
        while(temp.next!=null){
            if(temp.id==id){
                temp.pre.next=temp.next;
                temp.next.pre=temp.pre;
                return;
            }
            temp=temp.next;
        }
        if(temp.id==id){
            temp.pre.next=null;
            return;
        }
        System.out.println("该节点不存在，无法删除");
    }

    //遍历
    public void show(){
        if(headNode==null||headNode.next==null){
            System.out.println("链表为空，无法显示");
        }
        DoubleNode temp=headNode;
        while (temp.next!=null){
            System.out.println(temp.next.toString());
            temp=temp.next;
        }
    }
}

class DoubleNode{
    public int id;
    public String name;
    public DoubleNode pre;
    public DoubleNode next;

    public DoubleNode(int id,String name){
        this.id=id;
        this.name=name;
    }

    @Override
    public String toString() {
        return "DoubleNode{" +
                "id=" + id +
                ", name='" + name + '\'' +
                '}';
    }
}
```

### 3.3 单向环形链表

**Josephu(约瑟夫)问题**

实现可能出现的问题：

1. 对于toString方法的重写：不能重写next，否则会报java.lang.StackOverflowError异常，原因是当结点指向自身时会因为实例间的相互引用陷入死循环导致栈溢出。
2. 为什么需要找出first结点前结点：因为当要取出单链表其中一个结点时必须让前一个结点指向后一个结点。

```java
public class MyCircleLinkListTest {

    public static void main(String[] args) {
        MyCircleLinkList myCircleLinkList = new MyCircleLinkList();
        myCircleLinkList.add(125);
        myCircleLinkList.show();
        myCircleLinkList.countBoy(10,20,125);
    }

}


class MyCircleLinkList {
    private Boy first = null;

    //添加num个
    public void add(int num) {
        if (num < 1) {
            return;
        }
        Boy temp = null;
        for (int i = 1; i <= num; i++) {
            Boy boy = new Boy(i);
            if (i == 1) {
                first = boy;
                first.setNext(first);
                temp = first;
            } else {
                temp.setNext(boy);
                boy.setNext(first);
                temp = boy;
            }

        }
    }

    //遍历
    public void show() {
        Boy temp = first;
        while (true) {
            System.out.println(temp.toString());
            temp = temp.getNext();
            if (temp == first) {
                break;
            }
        }
    }

    //
    public void countBoy(int startNo, int moveTime, int num) {
        if (first == null || startNo > num || moveTime < 0) {
            System.out.println("参数存在问题，无法执行");
            return;
        }
        Boy temp = first;//保存当前结点前一位
        Boy cur = first;//保存当前节点

        //找到first结点前位置
        while(temp.getNext()!=first){
            temp=temp.getNext();
        }

        //找出开始结点前一位位置
        for (int i = 0; i < startNo - 1; i++) {
            temp = temp.getNext();
        }
        cur = temp.getNext();
        while (true) {
            //找出移动moveTime-1次后结点位置
            //从自身开始报数，所以减一
            for (int i = 0; i < moveTime - 1; i++) {
                temp = temp.getNext();
            }
            cur = temp.getNext();
            if(temp==cur){
                break;
            }
            System.out.println("出圈："+cur.toString());
            temp.setNext(cur.getNext());
        }
        System.out.println("最后一个结点：" + cur.toString());
    }

}

class Boy {
    private int no;
    private Boy next;

    public int getNo() {
        return no;
    }

    public void setNo(int no) {
        this.no = no;
    }

    public Boy getNext() {
        return next;
    }

    public void setNext(Boy next) {
        this.next = next;
    }

    public Boy(int no) {
        this.no = no;
    }

    @Override
    public String toString() {
        return "Boy{" +
                "no=" + no +
                '}';
    }
}
```

## 4、栈

（stack)

**基本概念**

- 栈是一个先入后出(FILO-First In Last Out)的有序列表。
- 栈(stack)是限制线性表中元素的插入和删除只能在线性表的同一端进行的一种特殊线性表。允许插入和删除的
  一端，为变化的一端，称为栈顶(Top)，另一端为固定的一端，称为栈底(Bottom)。
- 根据栈的定义可知，最先放入栈中元素在栈底，最后放入的元素在栈顶，而删除元素刚好相反，最后放入的元
  素最先删除，最先放入的元素最后删除。

![image-20200424091956701](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200424091956701.png)

**应用场景**

- 子程序的调用：在跳往子程序前，会先将下个指令的地址存到堆栈中，直到子程序执行完后再将地址取出，以
  回到原来的程序中。
- 处理递归调用：和子程序的调用类似，只是除了储存下一个指令的地址外，也将参数、区域变量等数据存入堆
  栈中。
-  表达式的转换[中缀表达式转后缀表达式]与求值(实际解决)。
- 二叉树的遍历。
- 图形的深度优先(depth 一first)搜索法。

**数组模拟栈的实现**

1. 使用数组模拟。
2. 定义一个top表示栈顶，初始化为-1。

```java
//用数组模拟栈
public class ArrayStackTest {

    public static void main(String[] args) {
        ArrayStack stack = new ArrayStack(10);
        stack.push(2);
        stack.push(3);
        stack.push(4);
        stack.push(5);
        stack.show();
        stack.pop();
        stack.pop();
        stack.show();
    }


}

class ArrayStack {
    private int maxSize;//定义栈的大小
    private int top = -1;//top表示栈顶，初始化为-1
    private int[] stack;//用数组模拟栈

    public ArrayStack(int maxSize) {
        this.maxSize = maxSize;
        stack=new int[maxSize];
    }

    //栈是否为满
    public boolean isFull() {
        return top == maxSize - 1;
    }

    //栈是否为空
    public boolean isEmpty() {
        return top == -1;
    }

    //入栈
    public void push(int num) {
        if (isFull()) {
            System.out.println("栈满，无法入栈");
            return;
        }
        stack[++top] = num;
    }

    //出栈
    public int pop() {
        if (isEmpty()) {
            throw new RuntimeException("栈空，无法出栈");
        }
        return stack[top--];
    }

    //显示栈的情况[遍历栈]， 遍历时，需要从栈顶开始显示数据
    public void show() {
        if (isEmpty()) {
            System.out.println("栈空，无法遍历");
        }
        System.out.println("栈内元素为：");
        for (int i = top; i >= 0; i--) {
            System.out.println(stack[i]);
        }
    }


}
```

**链表模拟栈的实现**

- 使用单链表不需要担心栈满的问题。
- 当只有头结点时，此时栈便为空。
- 入栈：在单链表后添加一个新的结点。
- 出栈：让末尾结点前一个结点指向null。

```java
public class LinkListStackTest {
    public static void main(String[] args) {
        LinkListStack stack = new LinkListStack();
        stack.push(2);
        stack.push(3);
        stack.push(4);
        stack.show();
        System.out.println(stack.pop() + "出栈");
        System.out.println(stack.pop() + "出栈");

        stack.show();
    }
}


class LinkListStack {

    //结点类
    class Node {
        private int value;
        private Node next;

        public Node(int value) {
            this.value = value;
        }

        @Override
        public String toString() {
            return "Node{" +
                    "value=" + value +
                    '}';
        }
    }

    //定义头节点
    private Node head = new Node(0);

    public Node getHead() {
        return head;
    }

    //判断栈是否为空
    public boolean isEmpty() {
        return head.next == null;
    }

    //入栈
    public void push(int num) {
        Node temp = head;
        Node node = new Node(num);
        while (temp.next != null) {
            temp = temp.next;
        }
        temp.next = node;
    }

    //出栈
    public int pop() {
        if (isEmpty()) {
            throw new RuntimeException("栈为空，无法入栈");
        }
        Node temp = head;
        //找到要删除的前一个结点也就是末尾结点的前一个结点
        while (temp.next.next != null) {
            temp = temp.next;
        }
        int value = temp.next.value;
        temp.next = null;
        return value;
    }

    //栈的结点数（不包括head结点）
    public int getLength() {
        Node temp = head;
        int len = 0;
        while (temp.next != null) {
            len++;
            temp = temp.next;
        }
        return len;
    }

    //遍历栈，自栈顶向下输出
    public void show() {
        if (isEmpty()) {
            System.out.println("栈为空，无法输出");
            return;
        }
        Node temp = head;
        int length = getLength();
        int[] numStack = new int[length];
        for (int i = 0; i < length; i++) {
            numStack[i] = temp.next.value;
            temp = temp.next;
        }
        System.out.println("栈内元素：");
        for (int i = length - 1; i >= 0; i--) {
            System.out.println(numStack[i]);
        }
    }

}
```

**使用栈实现简单计算器**

1. 通过建立索引值遍历表达式

2. 如果扫描到数字，直接入栈

3. 如果扫描到符号：

   1. 当前操作符优先级小于或等于栈内操作符时，弹出数字栈中的两个数字以及符号栈中一个符号，计算结果入栈，再判断当前操作符优先级是否小于或等于栈内操作符，循环直至当前操作符优先级高于栈内操作符。
   2. 将操作符入栈。

   原因：我们的想法是判断表达式的计算是否会被后面影响，不确定的情况下我们就先入栈，等到确定下一个操作符的出现不会影响前面的计算时（即下一个操作符优先级小于当前操作符），我们就将前面的出栈并计算。比如1*2-1，当出现减号时就代表前面可以计算。

4. 当表达式扫描完成，就顺序弹出数字栈中两个数字及符号栈一个操作符并计算，计算结果放入数字栈，循环直至只剩下数字栈中一个数字，即为表达式结果。

   原因：当表达式扫描完毕后，扫描完成的两个栈已经可以直接计算，就相当于第一遍是将从左到右可以直接计算的都计算完毕，而剩下的如1-2*2是扫描中无法计算的，就通过从右到左的计算方式，先计算2与2的乘积，再计算1-4。

5. 弹出最后一个值并输出。

（其实很多人应该是不理解第三步为什么要做循环，如果看的是尚硅谷视频应该会发现老师并没有做，依然似乎没问题，但其实如果不做循环，从左到右计算不彻底就可能出现一些问题，比如：1-2*2+100。从左到右彻底的话最后数字栈中是-3、100，但如果不彻底就会出现104 、1，看上去没问题，但计算结果后者却是-103，因为它先计算了100+4，再计算1-104。）

```java
import java.util.Stack;

public class SimpleCalculatorTest {
    public static void main(String[] args) {
        String expression = "3-2*2+100";
        System.out.println(SimpleCalculator(expression));
    }

    public static int SimpleCalculator(String expression) {
        //数字栈
        Stack<Integer> numberStack = new Stack<>();
        //符号栈
        Stack<Character> symbolStack = new Stack<>();
        //符号数组
        char[] array = {'+', '-', '*', '/'};
        //指示器
        int index = 0;
        //判断是否到底
        while (index != expression.length()) {
            char temp = expression.charAt(index);
            //判断是否为数字
            if (temp > 47 && temp < 58) {
                int num = 0;
                //解决多位数问题
                while (index < expression.length() && expression.charAt(index) > 47 && expression.charAt(index) < 58) {
                    temp = expression.charAt(index);
                    num = num * 10 + temp - '0';
                    index++;
                }
                numberStack.add(num);
            }
            //当为操作符时
            if (temp == '+' || temp == '-' || temp == '*' || temp == '/') {
                while (!symbolStack.isEmpty() && priority(temp) <= priority(symbolStack.peek())) {
                    int a = numberStack.pop();
                    int b = numberStack.pop();
                    char c = symbolStack.pop();
                    numberStack.add(calculator(a, b, c));
                }
                symbolStack.add(temp);
                index++;
            }

        }
        while (!symbolStack.isEmpty()) {
            int a = numberStack.pop();
            int b = numberStack.pop();
            char c = symbolStack.pop();
            numberStack.add(calculator(a, b, c));
        }
        return numberStack.pop();
    }

    public static int priority(char ch) {
        if (ch == '*' || ch == '/') {
            return 1;
        }
        if (ch == '+' || ch == '-') {
            return 0;
        }
        throw new RuntimeException("符号不正确");
    }

    public static int calculator(int a, int b, char c) {
        switch (c) {
            case '+':
                return a + b;
            case '-':
                return b - a;
            case '*':
                return a * b;
            case '/':
                return b / a;
        }
        throw new RuntimeException("符号不正确");
    }

}
```

**逆波兰表达式（后缀表达式）**

- 后缀表达式运算

1. 初始化一个栈stack；
2. 如果遍历到数字，直接入栈；
3. 如果遍历到符号，将两个数字出栈并计算，计算结果入栈；
4. 循环2-3两步；
5. 返回唯一的栈顶元素。

注意：第三步计算顺序：次顶元素（运算符）栈顶元素。

- 中缀表达式转后缀表达式步骤

1. 初始化两个栈：运算符栈s1 和储存中间结果的栈s2；

2. 从左至右扫描中缀表达式；

3. 遇到操作数时，将其压s2；

4. 遇到运算符时，比较其与s1 栈顶运算符的优先级：

   1. 如果s1 为空，或栈顶运算符为左括号“(”，则直接将此运算符入栈；

   2. 否则，若优先级比栈顶运算符的高，也将运算符压入s1；
   3. 否则，将s1 栈顶的运算符弹出并压入到s2 中，再次转到(4-1)与s1 中新的栈顶运算符相比较；

5. 遇到括号时：

   1. 如果是左括号“(”，则直接压入s1
   2. 如果是右括号“)”，则依次弹出s1 栈顶的运算符，并压入s2，直到遇到左括号为止，此时将这一对括号丢弃

6. 重复步骤2 至5，直到表达式的最右边

7. 将s1 中剩余的运算符依次弹出并压入s2

8. 依次弹出s2 中的元素并输出，结果的逆序即为中缀表达式对应的后缀表达式。

(对于这一套标准步骤，我们可以发现其实s2在整个过程中是没有出栈操作的，所以我们可以用字符串/数组来代替s2，这样实现的话也可以省去第八步得逆序过程)

其实概括出来就是：

1. 每当遇到数字就直接入s2
2. 每当遇到运算符，与s1栈顶运算符比较优先级，优先级高则入s1栈，否则则将s1栈顶运算符入s2，继续比较直至s1栈空或者运算符优先级大于栈顶运算符,此时将运算符入s1栈。
3. 每当遇到括号，左括号入栈，右括号出现则将左括号与右括号之间内容压入s2。
4. 很显然，所有的步骤都是为了计算时优先级高的计算步骤能够先得到计算。

代码：

（其实主要是理清楚步骤以及原理，代码直接看可能繁琐，但自己理清楚步骤后就可以将大问题化成一步步的小问题，这样写起来代码其实就不是很复杂了）

```java
import java.util.Stack;

//逆波兰表达式完成计算器
public class ReversePolishNotationTest {
    public static void main(String[] args) {
        String expression = "100+2+3*20+12";
        String infixExpression = toInfixExpression(expression);
        System.out.println(infixExpression);
        System.out.println(reversePolishNotation(infixExpression));
    }

    //中缀表达式转后缀表达式
    private static String toInfixExpression(String expression) {
        int index = 0;
        Stack<String> s1 = new Stack<>();
        String s2 = "";
        while (index < expression.length()) {
            char temp = expression.charAt(index);
            //遍历到数字直接入s2，并index++
            if (temp > 47 && temp < 58) {
                String num = "";
                while (index < expression.length() && expression.charAt(index) > 47 && expression.charAt(index) < 58) {
                    num += expression.charAt(index);
                    index++;
                }
                s2 += num + " ";
            } else if (temp == '+' || temp == '-' || temp == '*' || temp == '/' || temp == '(') {
                //遍历到运算符判断优先级
                //这里之所以加入(处理是因为左括号也会入栈，优先级比较可能出现运算符与括号比较
                String symbol = temp + "";
                boolean flag = true;
                while (!s1.isEmpty() && !symbol.equals("(") && priority(symbol) <= priority(s1.peek())) {
                    s2 = s2 + s1.pop() + " ";
                    flag = false;
                }
                s1.add(symbol);
                index++;
            } else if (temp == ')') {
                //对于右括号，我们只需要将栈中左括号以上运算符出s1栈到s2中即可
                if (!s1.peek().equals("(")) {
                    s2 += s1.pop() + " ";
                }
                //注意左括号本身也需要出栈
                s1.pop();
                index++;
            } else {
                throw new RuntimeException("请输入规范的字符串");
            }
        }
        while (!s1.isEmpty()) {
            s2 += s1.pop() + " ";
        }
        return s2;
    }

    //后缀表达式运算
    public static int reversePolishNotation(String expression) {
        String[] elements = expression.split(" ");
        Stack<Integer> stack = new Stack<>();
        for (String element : elements) {
            if (element.equals("+") || element.equals("-") || element.equals("*") || element.equals("/")) {
                int a = stack.pop();
                int b = stack.pop();
                int result = calculator(a, b, element);
                stack.add(result);
            } else if (element.matches("\\d+")) {
                stack.add(Integer.parseInt(element));
            } else {
                throw new RuntimeException("字符串不符合规范");
            }
        }
        return stack.peek();
    }

    private static int calculator(int a, int b, String element) {
        switch (element) {
            case "+":
                return a + b;
            case "-":
                return b - a;
            case "*":
                return a * b;
            case "/":
                return b / a;
        }
        throw new RuntimeException("操作符有误");
    }

    public static int priority(String ch) {

        if (ch.equals("*") || ch.equals("/")) {
            return 1;
        }
        if (ch.equals("+") || ch.equals("-")) {
            return 0;
        }
        //因为如果遇见左括号需要直接将运算符入栈，所以将左括号优先级设为-1
        if (ch.equals("(")) return -1;
        throw new RuntimeException("符号不正确");
    }
    
}

```

## 5、递归

- 简单的说: 递归就是方法自己调用自己，每次调用时传入不同的变量，递归有助于编程者解决复杂的问，同时可以让代码变得简洁。

![image-20200427082051067](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200427082051067.png)

```java
public class RecursionTest {
    public static void main(String[] args) {
        //通过打印问题，回顾递归调用机制
        //test(4);
        int res = factorial(3);
        System.out.println("res=" + res);
    }

    //打印问题
    public static void test(int n) {
        if (n > 2) {
            test(n - 1);
        } //else {
        System.out.println("n=" + n);
// }
    }

    //阶乘问题
    public static int factorial(int n) {
        if (n == 1) {
            return 1;
        } else {
            return factorial(n - 1) * n; // 1 * 2 * 3
        }
    }
}

```

- 递归需要遵守的重要规则：
  1. 执行一个方法时，就创建一个新的受保护的独立空间(栈空间)；
  2.  方法的局部变量是独立的，不会相互影响, 比如n 变量；
  3.  如果方法中使用的是引用类型变量(比如数组)，就会共享该引用类型的数据；
  4. 递归必须向退出递归的条件逼近，否则就是无限递归,出现StackOverflowError；
  5. 当一个方法执行完毕，或者遇到return，就会返回，遵守谁调用，就将结果返回给谁，同时当方法执行完毕或者返回时，该方法也就执行完毕。

迷宫问题：

```java
//迷宫问题，求初始点到终点的一条通路（不一定是最小通路！）
//墙体设为1，路径设为0
/*
    整体思路：
    1、每当我们经过一个点，将点标记为2，代表已走过；
    2、对于该点，我们依照下右上左的方向向后查找；
    3、如果该点所有路径都走不通，就返回false，回到上一个点处向后查找；
    4、当到达终点，即终点标记为2时，一层一层向上推出递归。
    递归思路：
    1、终止条件：终点处被遍历到或者已遍历完所有可遍历点；
    2、每层递归该做的事：判断该点是否是终点，标记该点已走过，下右上左策略判断是否可走；
    3、返回参数：返回该点四个方向是否有可到终点的通路。
 */
public class MiGong {

    static int m = 3, n = 3;//终点位置

    public static void main(String[] args) {
        int[][] map = new int[6][6];//迷宫初始化
        int i = 1, j = 0;//起点位置
        //设置墙体
        map[1][2] = 1;
        map[0][2] = 1;
        map[4][4] = 1;
        map[5][1] = 1;
        System.out.println(findWay(map, i, j));
        for (int i1 = 0; i1 < map.length; i1++) {
            for (int i2 = 0; i2 < map[0].length; i2++) {
                System.out.print(map[i1][i2] + " ");
            }
            System.out.println();
        }
    }

    private static boolean findWay(int[][] map, int i, int j) {
        //当到达（6，5）时，表示已经找到通路
        if (map[m][n] == 2) {
            return true;
        }
        if (map[i][j] == 0) {
            map[i][j] = 2;
            if (i + 1 < map.length && findWay(map, i + 1, j)) {
                return true;
            } else if (j + 1 < map[0].length && findWay(map, i, j + 1)) {
                return true;
            } else if (i - 1 >= 0 && findWay(map, i - 1, j)) {
                return true;
            } else if (j - 1 >= 0 && findWay(map, i, j - 1)) {
                return true;
            } else {
                return false;
            }
        } else {
            map[i][j] = 0;//走不通后标记回0；
            return false;
        }
    }

}
```

**八皇后问题**

二维数组解法：

```java
/*
    1、递归结束条件：当遍历完所有（即遍历完从第一行第八列开始的所有可能）自动结束
    2、每层递归流程：首先判断这一行是否为第八行，如果是，直接输出并返回，如果不是，在这一行寻找可以放置皇后的位置
 */
public class EightEmpress {

    static int k=1;

    public static void main(String[] args) {
        int[][] board = new int[8][8];//初始化棋盘
        int num = 0;
        findWay(board, num);
    }

    private static void findWay(int[][] board, int num) {
        //已摆放完毕
        if (num >= 8) {
            System.out.println("摆放方法"+k+":");
            k++;
            for (int i = 0; i < 8; i++) {
                for (int j = 0; j < 8; j++) {
                    System.out.print(board[i][j]+" ");
                }
                System.out.println();
            }
            return;
        }else{
            for (int i = 0; i < 8; i++) {
                if(placeAble(board,num,i)){
                    board[num][i]=1;
                    findWay(board,num+1);
                    board[num][i]=0;
                }
            }
        }


    }

    //判断是否可以放置棋子
    private static boolean placeAble(int[][] board,int x, int y) {
        for (int i = 0; i < x; i++) {
            //纵向是否存在棋子
            if(board[i][y]==1){
                return false;
            }
            /*
                斜向是否存在棋子
                斜向表示：要么与该点和相等（左上->右下），要么与该点差相等（右上->左下）
                即：i+j==x+y||i-j==x-y   j=x+y-i||j=y-x+i
             */
            if((x+y-i>=0&&x+y-i<8&&board[i][x+y-i]==1)||(y-x+i>=0&&y-x+i<8&&board[i][y-x+i]==1)){
                return false;
            }
        }
        return true;
    }

}
```

一维数组解法：

```java
//用一维数组解决八皇后问题
/*
    思路分析：用数组小标来表示行号,用数组的值表示列数
 */
public class EightEmpress2 {

    static int num = 1;

    public static void main(String[] args) {
        int[] col = new int[8];//初始化棋盘
        int row = 0;
        findWay(col, row);
    }

    private static void findWay(int[] col, int row) {
        if (row >= 8) {
            System.out.println("方法" + num + ":");
            num++;
            print(col);
            return;
        } else {
            for (int i = 0; i < 8; i++) {
                if (check(col, row, i)) {
                    col[row] = i;
                    findWay(col, row + 1);
                    /*
                        注意，这儿与二维数组不同，不需要让col[row]=0,
                        因为向后遍历赋值后col[row]会改变不需要
                        当然，你加上去也不会使结果受到影响
                     */
                }
            }
        }

    }


    //判断是否可以放置
    private static boolean check(int[] col, int row, int column) {
        for (int i = 0; i < row; i++) {
            if (row + column == i + col[i] || row - column == i - col[i] || col[i] == column) {
                return false;
            }
        }
        return true;
    }

    //输出
    private static void print(int[] col) {
        for (int i = 0; i < col.length; i++) {
            for (int i1 = 0; i1 < col.length; i1++) {
                if (i1 + 1 == col[i]) {
                    System.out.print(1 + " ");
                } else {
                    System.out.print(0 + " ");
                }
            }
            System.out.println();
        }
    }

}

```

## 6、排序算法

- 内部排序：指将需要处理的所有数据都加载到内部存储器(内存)中进行排序。
- 外部排序法：数据量过大，无法全部加载到内存中，需要借助外部存储(文件等)进行排序。

![](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\尚硅谷_韩顺平_图解Java数据结构和算法-已解锁.jpg)

![](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\排序时间复杂度.png)

### 6.1 时间复杂度

1. 时间频度：一个算法花费的时间与算法中语句的执行次数成正比例，哪个算法中语句执行次数多，它花费时间就多。一个算法中的语句执行次数称为语句频度或时间频度。记为T(n)。
2. 时间复杂度：一般情况下，算法中的基本操作语句的重复执行次数是问题规模n 的某个函数，用T(n)表示，若有某个辅助函数f(n)，使得当n 趋近于无穷大时，T(n) / f(n) 的极限值为不等于零的常数，则称f(n)是T(n)的同数量级函数。记作T(n)=Ｏ( f(n) )，称Ｏ( f(n) ) 为算法的渐进时间复杂度，简称时间复杂度。

- T(n) 不同，但时间复杂度可能相同。如：T(n)=n²+7n+6 与T(n)=3n²+2n+2 它们的T(n) 不同，但时间复杂度相同，都为O(n²)。
- 计算时间复杂度的方法：
  - 用常数1 代替运行时间中的所有加法常数T(n)=n²+7n+6 => T(n)=n²+7n+1
  - 修改后的运行次数函数中，只保留最高阶项T(n)=n²+7n+1 => T(n) = n²
  - 去除最高阶项的系数T(n) = n² => T(n) = n² => O(n²)
- 常见时间复杂度：
  1. 常数阶O(1)
  2. 对数阶O(log2n)
  3. 线性阶O(n)
  4. 线性对数阶O(nlog2n)
  5. 平方阶O(n^2)
  6. 立方阶O(n^3)
  7. k 次方阶O(n^k)
  8. 指数阶O(2^n)
- Ο(1)＜Ο(log2n)＜Ο(n)＜Ο(nlog2n)＜Ο(n^2)＜Ο(n^3)＜ Ο(n^k) ＜Ο(2^n)
- 平均时间复杂度和最坏时间复杂度
  1. 平均时间复杂度是指所有可能的输入实例均以等概率出现的情况下，该算法的运行时间。
  2. 最坏情况下的时间复杂度称最坏时间复杂度。一般讨论的时间复杂度均是最坏情况下的时间复杂度。这样做的原因是：最坏情况下的时间复杂度是算法在任何输入实例上运行时间的界限，这就保证了算法的运行时间不会比最坏情况更长。

### 6.2 冒泡排序（Bubble Sort）

- 这个算法的名字由来是因为越小的元素会经由交换慢慢“浮”到数列的顶端（升序或降序排列），就如同碳酸饮料中二氧化碳的气泡最终会上浮到顶端一样，故名“冒泡排序”。
- 它重复地走访过要排序的元素列，依次比较两个相邻的元素，如果顺序（如从大到小、首字母从Z到A）错误就把他们交换过来。走访元素的工作是重复地进行直到没有相邻元素需要交换，也就是说该元素列已经排序完成。
- 过程：
  1. 设立两个指针，指向开始的两个元素，如果两者逆序，就交换两者位置。
  2. 两个指针同时向后移动，如果两个相邻元素逆序，就交换位置。
  3. 通过一遍循环，我们就可以确认固定最后一个元素。
  4. 重复上述步骤，直至所有元素被固定。
- 时间复杂度：
  1. 最坏时间复杂度（数组完全逆序）：O(n^2) 
  2. 最好时间复杂度（数组完全顺序）：O(n) 
  3. 平均时间复杂度：O(n^2)
- 稳定性：稳定。因为两个相等元素不会交换位置。
- 优化：
  1. 我们不必真的使用两个指针，因为指针是相邻的，后者位置为前者+1
  2. 如果在一次内部循环中，没有出现任何一次交换，就证明数组已经是顺序的，不需要继续交换，可以直接跳出外层循环。
- 代码实现：

```java
//冒泡排序
public class  {
    public static void main(String[] args) {
//        int[] array = {1, 5, 8, 7, 6, 2};
//        int[] array = {1, 2, 3, 4, 5, 6, 7, 9};
        int[] array = {6, 5, 4, 2, 1, 0};
        int[] sortedArray = bubbleSort(array);
        for (int i = 0; i < array.length; i++) {
            System.out.print(sortedArray[i] + " ");
        }
    }

    private static int[] bubbleSort(int[] array) {
        for (int i = 0; i < array.length - 1; i++) {  //array.length - 1是因为当后面n-1个元素固定后第一个元素自然就固定好了
            boolean flag = true;//判断当前循环是否出现过交换
            for (int j = 0; j < array.length - i - 1; j++) { //array.length - i - 1是因为最后i个元素已经固定好，其次我们比较的是两个数，所以最后还得-1
                if (array[j] > array[j + 1]) {
                    int temp = array[j];
                    array[j] = array[j + 1];
                    array[j + 1] = temp;
                    flag=false;
                }
            }
            if (flag == true) {
                break;//如果没有出现过交换，代表整个数组已经是顺序的，直接跳出循环
            }
        }
        return array;
    }
}

```

### 6.3 选择排序（Selection Sort）

- 选择排序的原理就是每次循环都找出最小（大）的数放在最前面。
- 流程：第一次从待排序的数据元素中选出最小（或最大）的一个元素，存放在序列的起始位置，然后再从剩余的未排序元素中寻找到最小（大）元素，然后放到已排序的序列的末尾。以此类推，直到全部待排序的数据元素的个数为零。
- 时间复杂度：O(n^2)
- 稳定性：不稳定。比如序列5 8 5 2 9，我们知道第一遍选择第1个元素5会和2交换，那么原序列中两个5的相对前后顺序就被破坏了，所以选择排序是一个不稳定的排序算法。
- 选择排序相较于冒泡排序而言，选择排序的判断次数是固定的，而且必须遍历完所有可能性，而冒泡排序可以检测是否顺序中断排序，但选择排序的交换次数相比冒泡排序可以减少很多，而且交换所需要消耗的时间是比判断的时间是更多的。

```java
//选择排序
public class SelectionSortTest {
    public static void main(String[] args) {
        int[] array = {1, 5, 8, 7, 6, 2};
//        int[] array = {1, 2, 3, 4, 5, 6, 7, 9};
//        int[] array = {6, 5, 4, 2, 1, 0};
        int[] sortedArray = selectionSort(array);
        for (int i = 0; i < array.length; i++) {
            System.out.print(sortedArray[i] + " ");
        }
    }

    private static int[] selectionSort(int[] array) {
        int min, temp, key ;//min存储当前循环最小值，key存储最小值的下标
        for (int i = 0; i < array.length-1; i++) {
            min = array[i];
            key=i;
            for (int j = i + 1; j < array.length; j++) {
                if (array[j] < min) {
                    min = array[j];
                    key = j;
                }
            }
            if(key!=i){
                temp = array[i];
                array[i] = array[key];
                array[key] = temp;
            }
        }
        return array;
    }
}

```

### 6.4 插入排序（Insertion Sort）

- 插入排序（Insertion Sorting）的基本思想是：把n 个待排序的元素看成为一个有序表和一个无序表，开始时有序表中只包含一个元素，无序表中包含有n-1 个元素，排序过程中每次从无序表中取出第一个元素，把它的排序码依次与有序表元素的排序码进行比较，将它插入到有序表中的适当位置，使之成为新的有序表。
- 示意图:

![](https://upload-images.jianshu.io/upload_images/11016594-150b6c462e3ed809.png)

- 稳定性：稳定。插入排序如果碰到相等元素不会插入到相等元素的前面。
- 时间复杂度：O（n^2）

```java
//插入排序
public class InsertionSortTest {
    public static void main(String[] args) {
//        int[] array = {1, 5, 8, 7, 6, 2};
//        int[] array = {1, 2, 3, 4, 5, 6, 7, 9};
        int[] array = {6, 5, 4, 2, 1, 0};
        int[] sortedArray = InsertionSort(array);
        for (int i = 0; i < array.length; i++) {
            System.out.print(sortedArray[i] + " ");
        }
    }

    private static int[] InsertionSort(int[] array) {
        for (int i = 1; i < array.length; i++) {
            if (array[i] < array[i - 1]) {//如果第i个大于第i-1个,表明这个数字顺序是不需要调整的
                int temp=array[i];//保存当前第i个位置的值
                int j;
                for (j = i; j >0&&array[j-1]>temp ; j--) {
                    array[j]=array[j-1];//将所有大于temp的值向后移
                }
                array[j]=temp;//插入temp
            }
        }
        return array;
    }
}
```

### 6.5 希尔排序(Shell's Sort)

- 希尔排序是希尔（Donald Shell）于1959 年提出的一种排序算法。希尔排序也是一种插入排序，它是简单插入排序经过改进之后的一个更高效的版本，也称为缩小增量排序。
- 希尔排序是把记录按下标的一定增量分组，对每组使用直接插入排序算法排序；随着增量逐渐减少，每组包含的关键词越来越多，当增量减至1 时，整个文件恰被分成一组，算法便终止。

![](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\xierpaixu.jpg)

![](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\xier1.jpg)

- 希尔排序是基于插入排序的一种算法， 在此算法基础之上增加了一个新的特性，提高了效率。希尔排序的时间的时间复杂度为O（n^3/2)希尔排序时间复杂度的下界是n*log2n。希尔排序没有快速排序算法快 O(n(logn))，因此中等大小规模表现良好，对规模非常大的数据排序不是最优选择。

```java
public class ShellsSortTest {
    public static void main(String[] args) {
//        int[] array = {1, 5, 5, 7, 6, 2};
//        int[] array = {1, 2, 3, 4, 5, 6, 7, 9};
//        int[] array={8,9,1,7,2,3,5,4,6,0};
        int[] array = {6, 5, 4, 2, 1, 0};
        int[] sortedArray = ShellsSort(array);
        for (int i = 0; i < array.length; i++) {
            System.out.print(sortedArray[i] + " ");
        }
    }

    public static int[] ShellsSort(int[] array){
        int temp;
        // 增量gap, 并逐步的缩小增量
        for (int gap=array.length/2; gap >0 ; gap/=2) {
            // 从第gap 个元素，逐个对其所在的数组进行直接插入排序
            for (int i = gap; i < array.length; i++) {
                int j=i;
                temp=array[i];
                if(temp<array[j-gap]){
                    while (j-gap>=0&&array[j-gap]>temp){
                        array[j]=array[j-gap];
                        j-=gap;
                    }
                }
                array[j]=temp;
            }
        }
        return array;
    }
}
```

### 6.6 快速排序(Quicksort)

- 快速排序（Quicksort）是对冒泡排序的一种改进。
- 基本思想：通过一趟排序将要排序的数据分割成独立的两部分，其中一部分的所有数据都比另外一部分的所有数据都要小，然后再按此方法对这两部分数据分别进行快速排序，整个排序过程可以递归进行，以此达到整个数据变成有序序列。
- 基本流程：
  1. 首先创建left、right两个指针，和一个基准数（不同方法选择的基准数位置不同）
  2. 将比基准数小的放在基准数左边，比基准数大的放在基准数右边（不要求顺序）
  3. 通过递归向左右执行1、2两步，直到left和right刚进入一层递归就已经相等，完成排序。

**挖坑法划分：**https://blog.csdn.net/morewindows/article/details/6684558（参考博客）

```java
//快速排序
public class QuickSortTest {
    public static void main(String[] args) {
        int[] array = {1, 5, 5,7, 6, 2};
//        int[] array = {8, 2, 3, 4, 5, 6, 7, 9};
//        int[] array = {6, 5, 4, 2, 1, 0};
//        int[] array = {5, 6, 3, 1, 8, 7, 99};
        int[] sortedArray = quickSort(array, 0, array.length - 1);
        for (int i = 0; i < array.length; i++) {
            System.out.print(sortedArray[i] + " ");
        }
    }

    /*
        挖坑法进行一次划分：
        1、通过将基准值作为坑，先向右找到小于基准值的数，将这个数填入坑中，而将这个数原来位置作为新坑
        2、然后，再向左找到小于基准值的数，将这个数填入新坑中，而将这个数原来位置作为新坑
        3、不断重复1、2两步，直至left==right，将基准值填入最后的坑
     */
    private static int[] quickSort(int[] array, int l, int r) {
        if (l < r) {
            int left = l;//左指针确保left左边的数都小于基准值
            int right = r;//右指针确保right右边的数都大于基准值
            int temp = array[left];//保存基准值，并挖坑
            while (left < right) {
                //从右向左找到第一个小于基准数的数
                while (left < right && array[right] >= temp) {
                    right--;
                }
                if (left < right) {
                    array[left++] = array[right];//填坑，并将array[right]作为新的坑
                }
                //从左向右找到第一个大于基准数的数
                while (left < right && array[left] <= temp) {
                    left++;
                }
                if (left < right) {
                    array[right--] = array[left];//填坑，并将array[left]作为新的坑
                }
            }
            array[right] = temp;//当left和right相等时，填入基准值
            quickSort(array, l, right - 1);//向左重复
            quickSort(array, left + 1, r);//向右重复
        }
        return array;
    }
}

```

**交换法：**逻辑更简单

```java
public class QuickSortTest2 {

    public static void main(String[] args) {
        int[] arr = {1, 3, 5,7, 6, 2};
//        int[] arr = {1, 3, 5,5,7, 6, 2,2};
//        int[] arr = {-9,78,0,23,-567,70, -1,900, 4561};
        quickSort(arr,0,arr.length-1);
        for (int i = 0; i < arr.length; i++) {
            System.out.print(arr[i]+" ");
        }
    }

    //交换法
    public static int[] quickSort(int arr[],int start,int end) {
        int pivot = arr[start];
        int i = start;
        int j = end;
        while (i<j) {
            while ((i<j)&&(arr[j]>pivot)) {
                j--;
            }
            while ((i<j)&&(arr[i]<pivot)) {
                i++;
            }
            if ((arr[i]==arr[j])&&(i<j)) {
                i++;
            } else {
                int temp = arr[i];
                arr[i] = arr[j];
                arr[j] = temp;
            }
        }
        if (i-1>start) arr=quickSort(arr,start,i-1);
        if (j+1<end) arr=quickSort(arr,j+1,end);
        return (arr);
    }
}
```

### 6.7 归并排序（MERGE-SORT）

- 归并排序（MERGE-SORT）是建立在归并操作上的一种有效的排序算法,该算法是采用分治法（Divide and Conquer）的一个非常典型的应用。将已有序的子序列合并，得到完全有序的序列；即先使每个子序列有序，再使子序列段间有序。若将两个有序表合并成一个有序表，称为二路归并。归并排序是一种稳定的排序方法。
- 归并排序是一种稳定的排序方法。
- 时间复杂度：O(nlogn)

图解：

![](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\guibing1.jpg)

![guibing2](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\guibing2.jpg)

```java
//归并排序
public class MergeSortTest {
    public static void main(String[] args) {
//        int[] array = {1, 5, 7, 6, 2};
//        int[] array = {6, 5, 4, 2, 1, 0};
//        int[] array = {5, 6, 3, 1, 8, 7, 99};
        int[] array = {8, 4, 5, 7, 1, 3, 6, 2};
        int[] temp = new int[array.length];
        mergeSort(array, 0, array.length - 1, temp);
        for (int i = 0; i < array.length; i++) {
            System.out.print(array[i] + " ");
        }
    }

    private static void mergeSort(int[] array, int left, int right, int[] temp) {
        if (left < right) {
            int mid = (left + right) / 2;
            mergeSort(array, left, mid, temp);
            mergeSort(array, mid + 1, right, temp);
            merge(array, left, right, temp);
        }
    }

    private static void merge(int[] array, int left, int right, int[] temp) {
        int mid = (left + right) / 2;
        int i = left;
        int j = mid + 1;
        int index = 0;

        while (i <= mid && j <= right) {
            if (array[i] <= array[j]) {
                temp[index++] = array[i++];
            } else {
                temp[index++] = array[j++];
            }
        }

        while (i <= mid) {
            temp[index++] = array[i++];
        }

        while (j <= right) {
            temp[index++] = array[j++];
        }

        index = 0;
        while (left <= right) {
            array[left++] = temp[index++];
        }

    }


}

```

### 6.8 基数排序（Radix Sort）

- 基数排序（radix sort）属于“分配式排序”（distribution sort），又称“桶子法”（bucket sort）或bin sort，顾名思义，它是透过键值的部份资讯，将要排序的元素分配)至某些“桶”中，藉以达到排序的作用，基数排序法是属于稳定性的排序，其时间复杂度为O (nlog(r)m)，其中r为所采取的基数，而m为堆数，在某些时候，基数排序法的效率高于其它的稳定性排序法。
- 基数排序是一种典型的以空间交换时间的算法实现。
- 基数排序一般不对包含负数的数组进行排序，如果需要对负数排序，必须再改进算法。

图解：

![](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\jishu1.jpg)

![jishu2](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\jishu2.jpg)

![jishu3](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\jishu3.jpg)

```java
//基数排序
public class RadixSortTest {
    public static void main(String[] args) {
//        int[] arr = {1, 3, 5, 7, 6, 2};
//        int[] arr = {1, 3, 5,5,7, 6, 2,2};
//        int[] arr = {-9,78,0,23,-567,70, -1,900, 4561};
        int[] arr = {53, 3, 542, 748, 14, 214,1000};
        radixSort(arr);
       
            System.out.print(arr[i] + " ");
        }
    }

    //注意，基数排序是不需要递归的
    private static void radixSort(int[] array) {
        int[][] bucket = new int[10][array.length];//使用最坏情况，arr.length防止溢出
        int[] count = new int[10];//记录每个桶中元素数量

        //找到最大值
        int max = array[0];
        for (int i = 1; i < array.length; i++) {
            if (array[i] > max) {
                max = array[i];
            }
        }

        //遍历出最大值的位数
        int max_len = 0;
        while (max != 0) {
            max/=10;
            max_len++;
        }

        for (int i = 0, n = 1; i < max_len; i++, n *= 10) {//通过位数确定排序次数
            for (int j = 0; j < array.length; j++) {//将每个数放入对应的桶
                int temp = array[j] / n % (10);
                bucket[temp][count[temp]++] = array[j];
            }

            //将每次排序好的按桶顺序放回数组
            int index=0;
            for (int i1 = 0; i1 < 10; i1++) {
                for (int j = 0; j < count[i1]; j++) {
                    array[index++] = bucket[i1][j];
                }
                count[i1] = 0;//把count数组清零
           }

        }

    }
}

```

## 7、查找

- 分类：
  1. 顺序（线性）查找
  2. 二分查找/折半查找
  3. 插值查找
  4. 斐波那契查找

### 7.1 线性查找

- 线性查找的数组可以是无序的

```java
//线性查找
public class SequenceSearchTest {

    public static void main(String[] args) {
        int[] array={1,2,6,4,5,9};
        int value=6;
        int key=sequenceSearch(array,value);
        if(key!=-1){
            System.out.println(value+"位于数组的第"+(key+1)+"位置");
        }else {
            System.out.println("未找到该元素");
        }
    }

    private static int sequenceSearch(int[] array, int value) {
        for (int i = 0; i < array.length; i++) {
            if(array[i]==value){
                return i;
            }
        }
        return -1;
    }


}
```

### 7.2 二分查找

- 二分查找必须确保数组本身是顺序的。

**递归实现：**

```java
//二分查找
public class BinarySearch {
    public static void main(String[] args) {
        int[] array = {1, 2, 4, 5, 6, 9};
        int value = 6;
        int key = binarySearch(array, 0, array.length - 1, value);
        if (key != -1) {
            System.out.println(value + "位于数组的第" + (key + 1) + "位置");
        } else {
            System.out.println("未找到该元素");
        }
    }

    private static int binarySearch(int[] array, int left, int right, int value) {
        if (left > right) {
            return -1;
        }
        int mid = (left + right) / 2;
        if (value > array[mid]) {
            return binarySearch(array, mid + 1, right, value);
        } else if (value < array[mid]) {
            return binarySearch(array, left, mid - 1, value);
        } else {
            return mid;
        }
    }

}

```

### 7.3 插值查找

- 插值查找相比二分查找可以说是更人性化，就好比你直接通过翻字典查单词，当你查abandon时，你用二分就是每次都在字典中间找，但显然，我们知道a开头的单词在字典很前面的部分，插值查找就是利用这种特性，所以插值查找就更像一个真人去查单词；
- 对于数据量较大，关键字分布比较均匀的查找表来说，采用插值查找, 速度较快；
- 关键字分布不均匀的情况下，该方法不一定比折半查找要好。

```java
//插值查找
public class InterpolationSearchTest {
    public static void main(String[] args) {
        int[] array = new int[100];
        for (int i = 0; i < array.length; i++) {
            array[i] = i + 1;
        }
        int value = 1;
        int key = interpolationSearch(array, 0, array.length - 1, value);
        if(key!=-1){
            System.out.println(value+"位于数组的第"+(key+1)+"位置");
        }else {
            System.out.println("未找到该元素");
        }
    }

    private static int interpolationSearch(int[] array, int left, int right, int value) {
        System.out.println("查找中...");
        //这儿的判断是不可省的，否则可能会数组越界，因为 (value - array[left]) / (array[right] - array[left])不能大于或小于零
        if (value < array[0] || value > array[array.length - 1] || left > right) {
            return -1;
        }
        int mid = left + (right - left) * (value - array[left]) / (array[right] - array[left]);
        if (value > array[mid]) {
            return interpolationSearch(array, mid + 1, right, value);
        } else if (value < array[mid]) {
            return interpolationSearch(array, left, mid - 1, value);
        } else {
            return mid;
        }
    }


}
```

### 7.4 斐波那契查找

（也称黄金分割法）

- 斐波那契搜索就是在二分查找的基础上根据斐波那契数列进行分割的。在斐波那契数列找一个等于略大于查找表中元素个数的数F[n]，将原查找表扩展为长度为F[n] (如果要补充元素，则补充重复最后一个元素，直到满足F[n]个元素)，完成后进行斐波那契分割，即F[n]个元素分割为前半部分F[n-1]个元素，后半部分F[n-2]个元素，找出要查找的元素在那一部分并递归，直到找到。
- 在最坏情况下，斐波那契查找的时间复杂度还是O(log2n)，且其期望复杂度也为O(log2n)，但是与折半查找相比，斐波那契查找的优点是它只涉及加法和减法运算，而不用除法，而除法比加减法要占用更多的时间，因此，斐波那契查找的运行时间理论上比折半查找小，但是还是得视具体情况而定。

```java
import java.util.Arrays;

public class FibonacciSearch {
    public static int maxSize = 20;

    public static void main(String[] args) {
        int[] arr = {1, 8, 10, 89, 1000, 1234};
        System.out.println("index=" + fibSearch(arr, 1));// 0
    }

    //因为后面我们mid=low+F(k-1)-1，需要使用到斐波那契数列，因此我们需要先获取到一个斐波那契数列
    //非递归方法得到一个斐波那契数列
    public static int[] fib() {
        int[] f = new int[maxSize];
        f[0] = 1;
        f[1] = 1;
        for (int i = 2; i < maxSize; i++) {
            f[i] = f[i - 1] + f[i - 2];
        }
        return f;
    }

    //编写斐波那契查找算法
    //使用非递归的方式编写算法

    /**
     * @param a   数组
     * @param key 我们需要查找的关键码(值)
     * @return 返回对应的下标，如果没有-1
     */
    public static int fibSearch(int[] a, int key) {
        int low = 0;
        int high = a.length - 1;
        int k = 0; //表示斐波那契分割数值的下标
        int mid = 0; //存放mid 值
        int f[] = fib(); //获取到斐波那契数列
        //获取到斐波那契分割数值的下标
        while (high > f[k] - 1) {
            k++;
        }
        //因为f[k] 值可能大于a 的长度，因此我们需要使用Arrays 类，构造一个新的数组，并指向temp[]
        //不足的部分会使用0 填充
        int[] temp = Arrays.copyOf(a, f[k]);
        //实际上需求使用a 数组最后的数填充temp
        //举例:
        //temp = {1,8, 10, 89, 1000, 1234, 0, 0} => {1,8, 10, 89, 1000, 1234, 1234, 1234,}
        for (int i = high + 1; i < temp.length; i++) {
            temp[i] = a[high];
        }
        // 使用while 来循环处理，找到我们的数key
        while (low <= high) { // 只要这个条件满足，就可以找
            mid = low + f[k - 1] - 1;
            if (key < temp[mid]) { //我们应该继续向数组的前面查找(左边)
                high = mid - 1;
                //为甚是k--
                //说明
                //1. 全部元素= 前面的元素+ 后边元素
                //2. f[k] = f[k-1] + f[k-2]
                //因为前面有f[k-1]个元素,所以可以继续拆分f[k-1] = f[k-2] + f[k-3]
                //即在f[k-1] 的前面继续查找k--
                //即下次循环mid = f[k-1-1]-1
                k--;
            } else if (key > temp[mid]) { // 我们应该继续向数组的后面查找(右边)
                low = mid + 1;
                //为什么是k -=2
                //说明
                //1. 全部元素= 前面的元素+ 后边元素
                //2. f[k] = f[k-1] + f[k-2]
                //3. 因为后面我们有f[k-2] 所以可以继续拆分f[k-1] = f[k-3] + f[k-4]
                //4. 即在f[k-2] 的前面进行查找k -=2
                //5. 即下次循环mid = f[k - 1 - 2] - 1
                k -= 2;
            } else { //找到
                //需要确定，返回的是哪个下标
                if (mid <= high) {
                    return mid;
                } else {
                    return high;
                }
            }
        }
        return -1;
    }
}
```

## 8、哈希表（散列）

- 散列表（Hash table，也叫哈希表），是根据关键码值(Key value)而直接进行访问的数据结构。也就是说，它通过把关键码值映射到表中一个位置来访问记录，以加快查找的速度。这个映射函数叫做散列函数，存放记录的数组叫做散列表。

- 例题：

  有一个公司,当有新的员工来报道时，要求将该员工的信息加入(id,性别,年龄,住址..)，当输入该员工的id 时,要求查找到该员工的所有信息。
  要求: 

  1. 不使用数据库,,速度越快越好=>哈希表(散列)；
  2. 添加时，保证按照id 从低到高插入；
  3. 使用链表来实现哈希表, 该链表不带表头[即: 链表的第一个结点就存放雇员信息]。

- 图解：

![](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\hashtable0.jpg)

```java
//哈希表测试
public class HashTableTest {
    public static void main(String[] args) {
        HashTable hashTable = new HashTable(3);
        hashTable.add(1, "张三");
        hashTable.add(5, "电七");
        hashTable.add(2, "李四");
        hashTable.add(6, "钱八");
        hashTable.add(9, "孙十");
        hashTable.add(3, "王五");
        hashTable.add(4, "赵六");
        hashTable.show();
        hashTable.findById(6);
        hashTable.deleteById(3);
        hashTable.deleteById(6);
        hashTable.deleteById(9);
        System.out.println("删除后：");
        hashTable.show();
    }
}

//哈希表
class HashTable {
    private int size;
    private EmployLinkList[] employLinkListArray;

    //初始化
    public HashTable(int size) {
        this.size = size;
        employLinkListArray = new EmployLinkList[size];
        for (int i = 0; i < size; i++) {
            employLinkListArray[i] = new EmployLinkList();
        }
    }

    //添加
    public void add(int id, String name) {
        Employ employ = new Employ(id, name);
        int index = hashFun(id);
        employLinkListArray[index].add(employ);
    }

    //遍历
    public void show() {
        for (int i = 0; i < size; i++) {
            System.out.println("第" + (i + 1) + "条链表：");
            employLinkListArray[i].show();
        }
    }

    //查找
    public Employ findById(int id) {
        int index = hashFun(id);
        Employ temp = employLinkListArray[index].findById(id);
        if (temp == null) {
            System.out.println("查找结果为空");
        } else {
            System.out.println("查找结果：" + temp.toString());
        }
        return temp;
    }

    //删除
    public void deleteById(int id){
        int index = hashFun(id);
        employLinkListArray[index].deleteById(id);
    }


    //散列函数——简单取模法
    public int hashFun(int id) {
        return id % size;
    }


}

//员工链表
class EmployLinkList {
    private Employ head = null;//不使用单独的表头结点

    //添加员工结点（按顺序插入）
    public void add(Employ employ) {
        if (head == null) {
            head = employ;
        } else {
            Employ temp = head;
            //如果插入结点id小于头结点id,将插入结点指向原本头节点，并让插入节点成为新的头结点
            if (temp.id > employ.id) {
                employ.next = temp;
                head = employ;
            } else {
                //遍历，如果遍历至末尾结点仍id仍小于插入结点，则插入到末尾
                //否则，插入到链表中间
                while (temp.next != null && temp.next.id < employ.id) {
                    temp = temp.next;
                }
                if (temp.next == null) {
                    temp.next = employ;
                } else {
                    employ.next = temp.next;
                    temp.next = employ;
                }
            }
        }
    }

    //遍历员工
    public void show() {
        Employ temp = head;
        if (temp == null) {
            System.out.println("=> 链表为空");
        }
        while (temp != null) {
            System.out.println("=> " + temp.toString());
            temp = temp.next;
        }
    }

    //查找员工
    public Employ findById(int id) {
        Employ temp = head;
        while (temp != null) {
            if (temp.id == id) {
                break;
            }
            temp = temp.next;
        }
        return temp;
    }

    //删除员工
    public void deleteById(int id){
        Employ temp = head;
        if(head.id==id){
            head=head.next;
        }else{
            while (temp.next!=null&&temp.next.id!=id){
                temp=temp.next;
            }
            if(temp.next==null){
                System.out.println("该结点不存在，无法删除");
            }else{
                temp.next=temp.next.next;
            }
        }
    }
}

//员工结点类
class Employ {
    int id;
    String name;
    String adress;
    Employ next;

    public Employ(int id, String name) {
        this.id = id;
        this.name = name;
    }

    @Override
    public String toString() {
        return "Employ{" +
                "id=" + id +
                ", name='" + name + '\'' +
                '}';
    }
}

```

## 9、树

### 9.1 二叉树

```java
//二叉树
public class BinaryTreeTest {
    public static void main(String[] args) {
        Node node1 = new Node(1, "A");
        Node node2 = new Node(2, "B");
        Node node3 = new Node(3, "C");
        Node node4 = new Node(4, "D");
        Node node5 = new Node(5, "E");
        Node node6 = new Node(6, "F");
        Node node7 = new Node(7, "G");

        node1.setLeft(node2);
        node1.setRight(node3);
        node2.setLeft(node4);
        node2.setRight(node5);
        node3.setLeft(node6);
        node3.setRight(node7);

        BinaryTree binaryTree = new BinaryTree(node1);

        System.out.println("前序遍历：");
        binaryTree.preTraverse();
        System.out.println("\n中序遍历：");
        binaryTree.midTraverse();
        System.out.println("\n后序遍历：");
        binaryTree.lastTraverse();

        System.out.println("前序遍历查找：");
        binaryTree.preTraverseSearch(9);
        System.out.println("中序遍历查找：");
        binaryTree.preTraverseSearch(3);
        System.out.println("后序遍历查找：");
        binaryTree.lastTraverseSearch(6);

        System.out.println("删除节点后：");
        binaryTree.deleteById(3);
        binaryTree.preTraverse();
    }
}

class BinaryTree {
    private Node root;

    public BinaryTree(Node root) {
        this.root = root;
    }

    //前序遍历
    public void preTraverse() {
        if (root == null) {
            System.out.println("根节点为空，无法查找");
            return;
        }
        root.preTraverse();
    }

    //中序遍历
    public void midTraverse() {
        if (root == null) {
            System.out.println("根节点为空，无法查找");
            return;
        }
        root.midTraverse();
    }

    //后序遍历
    public void lastTraverse() {
        if (root == null) {
            System.out.println("根节点为空，无法查找");
            return;
        }
        root.lastTraverse();
    }

    //前序遍历查找
    public Node preTraverseSearch(int id) {
        Node temp = root.preTraverseSearch(id);
        if (temp == null) {
            System.out.println("未查找到该值");
        } else {
            System.out.println("节点" + id + "的值为" + temp.getValue());
        }
        return temp;
    }

    //中序遍历查找
    public Node midTraverseSearch(int id) {
        Node temp = root.midTraverseSearch(id);
        if (temp == null) {
            System.out.println("未查找到该值");
        } else {
            System.out.println("节点" + id + "的值为" + temp.getValue());
        }
        return temp;
    }

    //后序遍历查找
    public Node lastTraverseSearch(int id) {
        Node temp = root.lastTraverseSearch(id);
        if (temp == null) {
            System.out.println("未查找到该值");
        } else {
            System.out.println("节点" + id + "的值为" + temp.getValue());
        }
        return temp;
    }

    //删除节点
    public void deleteById(int id) {
        if (root == null) {
            System.out.println("根节点为空，无法删除");
        } else if (root.getId() == id) {
            root = null;
        } else {
            root.deleteById(id);
        }
    }

}

class Node {
    private int id;
    private String value;
    private Node left;
    private Node right;

    public Node(int id, String value) {
        this.id = id;
        this.value = value;
    }

    //删除节点
    //如果是叶子节点，则删除该节点
    //如果是非叶子节点，则删除该子树
    public void deleteById(int id) {
        if (this.left != null && this.left.id == id) {
            this.left = null;
            return;
        }
        if (this.right != null && this.right.id == id) {
            this.right = null;
            return;
        }
        if (this.left != null) {
            this.left.deleteById(id);
        }
        if (this.right != null) {
            this.right.deleteById(id);
        }

    }

    //前序遍历查找
    public Node preTraverseSearch(int id) {
        if (this.id == id) {
            return this;
        }
        Node temp = null;
        if (this.left != null) {
            temp = this.left.preTraverseSearch(id);
        }
        if (temp != null) {
            return temp;
        }
        if (this.right != null) {
            temp = this.right.preTraverseSearch(id);
        }
        return temp;
    }

    //中序遍历查找
    public Node midTraverseSearch(int id) {
        Node temp = null;
        if (this.left != null) {
            temp = this.left.midTraverseSearch(id);
        }
        if (temp != null) {
            return temp;
        }
        if (this.id == id) {
            return this;
        }
        if (this.right != null) {
            temp = this.right.midTraverseSearch(id);
        }
        return temp;
    }

    //后续遍历查找
    public Node lastTraverseSearch(int id) {
        Node temp = null;
        if (this.left != null) {
            temp = this.left.lastTraverseSearch(id);
        }
        if (temp != null) {
            return temp;
        }
        if (this.right != null) {
            temp = this.right.lastTraverseSearch(id);
        }
        if (temp != null) {
            return temp;
        }
        if (this.id == id) {
            return this;
        }
        return temp;
    }


    //前序遍历
    public void preTraverse() {
        //System.out.println(value);
        System.out.print(id + " ");
        if (this.left != null) {
            left.preTraverse();
        }
        if (this.right != null) {
            right.preTraverse();
        }

    }

    //中序遍历
    public void midTraverse() {
        if (this.left != null) {
            left.midTraverse();
        }
        //System.out.println(value);
        System.out.print(id + " ");
        if (this.right != null) {
            right.midTraverse();
        }
    }

    //后序遍历
    public void lastTraverse() {
        if (this.left != null) {
            left.lastTraverse();
        }
        if (this.right != null) {
            right.lastTraverse();
        }
        //System.out.println(value);
        System.out.print(id + " ");
    }

    public void setValue(String value) {
        this.value = value;
    }

    public String getValue() {
        return value;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public Node getLeft() {
        return left;
    }

    public Node getRight() {
        return right;
    }

    public void setLeft(Node left) {
        this.left = left;
    }

    public void setRight(Node right) {
        this.right = right;
    }
}
```

### 9.2 顺序存储二叉树

- 二叉树顺序存储是二叉树的一种存储方式。
- 将二叉树存储在一个数组中，通过存储元素的下标反映元素之间的父子关系。
- 树的编号：从树根起，自上层至下层，每层自左至右地给所有结点编号
- 特点：
  1. 顺序二叉树通常只考虑完全二叉树
  2.  第n 个元素的左子节点为2 * n + 1
  3. 第n 个元素的右子节点为2 * n + 2
  4. 第n 个元素的父节点为(n-1) / 2
  5. n : 表示二叉树中的第几个元素和数组下标(按0 开始编号如图所示)

代码：

```java
//顺序存储二叉树
public class SequenceTreeTest {
    public static void main(String[] args) {
        int[] arr={1,2,3,4,5,6,7};
        SequenceTree sequenceTree=new SequenceTree(arr);
        sequenceTree.preOrder(0);
    }


}

class SequenceTree{
    private int[] array;

    public SequenceTree(int[] array) {
        this.array = array;
    }

    //先序遍历
    public void preOrder(int index){
        if(array.length==0||array==null){
            System.out.println("无法遍历");
            return;
        }
        if(index<array.length){
            System.out.println(array[index]);
        }
        if((2*index+1)<array.length){
            preOrder(2*index+1);
        }
        if((2*index+2)<array.length){
            preOrder(2*index+2);
        }
    }

}
```

### 9.3 线索化二叉树

- n 个结点的二叉链表中含有n+1 【公式2n-(n-1)=n+1】个空指针域。利用二叉链表中的空指针域，存放指向
  该结点在某种遍历次序下的前驱和后继结点的指针（这种附加的指针称为"线索"）。
- 这种加上了线索的二叉链表称为线索链表，相应的二叉树称为线索二叉树(Threaded BinaryTree)。根据线索性质的不同，线索二叉树可分为前序线索二叉树、中序线索二叉树和后序线索二叉树三种。
- 一个结点的前一个结点，称为前驱结点，
   一个结点的后一个结点，称为后继结点。
- 注意：
  - 当线索化二叉树后，Node 节点的属性left 和right ，有如下情况:
    1. left 指向的是左子树，也可能是指向的前驱节点；
    2.  right  指向的是右子树，也可能是指向后继节点。

思路(中序遍历为例）:

1. 首先，我们需要通过中序遍历遍历每个节点，即先写出二叉树的中序遍历；
2. 我们需要一个pre值记录当前节点的前一个节点，以方便设置当前节点的前驱结点。
3. 是否有前驱节点判断：当前节点左子树为空，pre节点不为空。
4. 是否有后继节点判断：当前节点右子树为空，pre节点不为空。
5. 前驱节点：

```java
node.setLeft(pre);
```

6. 后继结点：

```java
pre.setRight(node);//注意，后继结点是对pre进行操作，当前节点是无法知道自己的后继结点的，因为后继节点不一定就是node的右子节点。
```

注意：在继续向右递归前记得将当前节点赋值给pre。

代码：

```java
public class ThreadedBinaryTreeTest {
    public static void main(String[] args) {
        TNode node1 = new TNode(1, "A");
        TNode node2 = new TNode(2, "B");
        TNode node3 = new TNode(3, "C");
        TNode node4 = new TNode(4, "D");
        TNode node5 = new TNode(5, "E");
        TNode node6 = new TNode(6, "F");
        TNode node7 = new TNode(7, "G");

        node1.setLeft(node2);
        node1.setRight(node3);
        node2.setLeft(node4);
        node2.setRight(node5);
        node3.setLeft(node6);
        node3.setRight(node7);

        ThreadedBinaryTree threadedBinaryTree = new ThreadedBinaryTree(node1);
        threadedBinaryTree.midTraverseThreaded(node1);

        System.out.println(node4);
        System.out.println(node6.getLeft().getId());
        System.out.println(node6.getRight().getId());
    }
}

class ThreadedBinaryTree {
    private TNode root;
    private TNode pre;

    public ThreadedBinaryTree(TNode root) {
        this.root = root;
    }

    //以中序遍历来线索化二叉树
    public void midTraverseThreaded(TNode node) {
        if (node == null) {
            return;
        }
        midTraverseThreaded(node.getLeft());
        if (node.getLeft() == null) {
            node.setLeftType(1);
            node.setLeft(pre);
        }
        if (pre != null && pre.getRight() == null) {
            pre.setRight(node);
            pre.setRightType(1);
        }
        pre = node;
        midTraverseThreaded(node.getRight());


    }
}

class TNode {
    private int id;
    private String value;
    private TNode left;
    private TNode right;
    //leftType为0时表示指向的是左子树，为1时表示指向的是遍历的前驱结点；
    //rightType同理。
    private int leftType;
    private int rightType;

    public TNode(int id, String value) {
        this.id = id;
        this.value = value;
    }

    public int getId() {
        return id;
    }

    public String getValue() {
        return value;
    }

    public TNode getLeft() {
        return left;
    }

    public TNode getRight() {
        return right;
    }

    public int getLeftType() {
        return leftType;
    }

    public int getRightType() {
        return rightType;
    }

    public void setId(int id) {
        this.id = id;
    }

    public void setValue(String value) {
        this.value = value;
    }

    public void setLeft(TNode left) {
        this.left = left;
    }

    public void setRight(TNode right) {
        this.right = right;
    }

    public void setLeftType(int leftType) {
        this.leftType = leftType;
    }

    public void setRightType(int rightType) {
        this.rightType = rightType;
    }

    @Override
    public String toString() {
        return "TNode{" +
                "id=" + id +
                ", value='" + value + '\'' +
                '}';
    }

}
```

线索遍历：

```java
//非递归遍历
public void midTraverse(){
    if(root==null){
        System.out.println("二叉树为空");
    }
    TNode node=root;
    while (node!=null){
        while(node.getLeftType()==0){//注意此处不能使用node.getLeft()!=null为条件，否则整体会进入死循环
            node=node.getLeft();
        }
        System.out.println(node);
        while(node.getRightType()==1){
            node=node.getRight();
            System.out.println(node);
        }
        node=node.getRight();
    }
}
```

### 9.4 堆排序

- 堆排序是利用堆这种数据结构而设计的一种排序算法，堆排序是一种选择排序，它的最坏，最好，平均时间复
  杂度均为O(nlogn)，它也是不稳定排序。
- 堆是具有以下性质的完全二叉树：每个结点的值都大于或等于其左右孩子结点的值，称为大顶堆, 注意: 没有要求结点的左孩子的值和右孩子的值的大小关系。
- 每个结点的值都小于或等于其左右孩子结点的值，称为小顶堆。
- 一般升序采用大顶堆，降序采用小顶堆。
- 基本思想：

  1. 先将数组构造成一个大顶堆：从第二个节点开始遍历，如果该节点大于父节点的值，则交换两点的值，并继续向上遍历，直至节点值小于父节点，对下一个节点进行判断。
  2. 将头节点与末尾节点的值交换，对前size-1个元素操作：判断此时头节点是否大于该节点的两个子节点中的最大值，不大于则交换位置，并对交换后的位置继续判断，直至该节点为叶子节点或者两个子节点都小于该节点。

- 参考：https://blog.csdn.net/u010452388/article/details/81283998

```java
//堆排序
public class HeapSortTest {

    public static void main(String[] args) {
        int[] array = {1,3,5,9,7,4,2,6,11,58,12,24};//待排序数组即顺序存储二叉树
        int size = array.length;//构建堆长度
        heapSort(array);
        for (int i = 0; i < size; i++) {
            System.out.print(array[i]+" ");
        }
    }

    //堆排序
    public static void heapSort(int[] arr) {
        //构造大顶堆
        heapInsert(arr);
        int size = arr.length;
        while (size > 1) {
            //固定最大值
            swap(arr, 0, size - 1);
            size--;
            //构造大顶堆
            heapify(arr, 0, size);
        }

    }

    //构造大顶堆
    private static void heapInsert(int[] array) {
        int lagestNum;
        int index;//当前索引
        int parentIndex;//父节点索引
        for (int i = 1; i < array.length; i++) {
            index = i;
            parentIndex = (index - 1) / 2;
            while (array[index] > array[parentIndex]) {
                swap(array, index, parentIndex);
                index = parentIndex;
                parentIndex = (index - 1) / 2;
            }
        }
    }

    //将剩余元素转换为大根堆
    public static void heapify(int[] arr, int index, int size){
        int left = 2 * index + 1;
        int right = 2 * index + 2;
        while (left < size) {
            int largestIndex;
            //判断孩子中较大的值的索引（要确保右孩子在size范围之内）
            if (arr[left] < arr[right] && right < size) {
                largestIndex = right;
            } else {
                largestIndex = left;
            }
            //比较父结点的值与孩子中较大的值，并确定最大值的索引
            if (arr[index] > arr[largestIndex]) {
                largestIndex = index;
            }
            //如果父结点索引是最大值的索引，那已经是大根堆了，则退出循环
            if (index == largestIndex) {
                break;
            }
            //父结点不是最大值，与孩子中较大的值交换
            swap(arr, largestIndex, index);
            //将索引指向孩子中较大的值的索引
            index = largestIndex;
            //重新计算交换之后的孩子的索引
            left = 2 * index + 1;
            right = 2 * index + 2;
        }

    }

    private static void swap(int[] array, int index, int parentIndex) {
        int temp;
        temp = array[index];
        array[index] = array[parentIndex];
        array[parentIndex] = temp;
    }
}
```

### 9.5 哈夫曼树

- 给定n个权值作为n个**叶子结点**，构造一棵二叉树，若该树的带权路径长度(wpl)达到最小，称这样的二叉树为
  最优二叉树，也称为哈夫曼树(Huffman Tree), 还有的书翻译为霍夫曼树。

- 赫夫曼树是带权路径长度最短的树，权值较大的结点离根较近。

- 路径和路径长度：在一棵树中，从一个结点往下可以达到的孩子或孙子结点之间的通路，称为路径。通路中分支的数目称为路径长度。若规定根结点的层数为1，则从根结点到第L 层结点的路径长度为L-1。

- 结点的权及带权路径长度：若将树中结点赋给一个有着某种含义的数值，则这个数值称为该结点的权。结
  点的带权路径长度为：从根结点到该结点之间的路径长度与该结点的权的乘积。

- 树的带权路径长度：树的带权路径长度规定为所有叶子结点的带权路径长度之和，记为WPL(weighted path
  length) ,权值越大的结点离根结点越近的二叉树才是最优二叉树。

- 基本思想：

  1. 从小到大进行排序, 将每一个数据，每个数据都是一个节点， 每个节点可以看成是一颗最简单的二叉树。
  2. 取出根节点权值最小的两颗二叉树。
  3. 组成一颗新的二叉树, 该新的二叉树的根节点的权值是前面两颗二叉树根节点权值的和。
  4. 再将这颗新的二叉树，以根节点的权值大小再次排序， 不断重复1-2-3-4 的步骤，直到数列中，所有的数
     据都被处理，就得到一颗赫夫曼树。

- 图解：

  ​		{13, 7, 8, 3, 29, 6, 1}

![](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\huffmantree1.jpg)

````java
import java.util.ArrayList;
import java.util.Collections;

//哈夫曼树实现
public class HuffmanTreeTest {
    public static void main(String[] args) {
        int[] array = {13, 7, 8, 3, 29, 6, 1};
        ArrayList<HNode> list = new ArrayList<>();
        for (int i = 0; i < array.length; i++) {
            list.add(new HNode(array[i]));
        }
        HNode tree = huffmanTree(list);
        tree.preOrder();
    }

    private static HNode huffmanTree(ArrayList<HNode> list) {
        while (list.size() > 1) {
            Collections.sort(list);
            HNode left = list.get(0);
            HNode right = list.get(1);
            HNode parentNode = new HNode(left.getValue() + right.getValue());
            parentNode.setLeft(left);
            parentNode.setRight(right);
            list.remove(0);
            list.remove(0);
            list.add(parentNode);
        }
        return list.get(0);
    }
}

class HNode implements Comparable<HNode> {
    private int value;
    private HNode left;
    private HNode right;

    public HNode(int value) {
        this.value = value;
    }

    //前序遍历
    public void preOrder() {
        System.out.print(this.value+" ");
        if (left != null) {
            left.preOrder();
        }
        if (right != null) {
            right.preOrder();
        }
    }


    @Override
    public int compareTo(HNode o) {
        return this.value - o.value;
    }

    public int getValue() {
        return value;
    }

    public void setValue(int value) {
        this.value = value;
    }

    public HNode getLeft() {
        return left;
    }

    public void setLeft(HNode left) {
        this.left = left;
    }

    public HNode getRight() {
        return right;
    }

    public void setRight(HNode right) {
        this.right = right;
    }

    @Override
    public String toString() {
        return "HNode{" +
                "value=" + value +
                ", left=" + left +
                ", right=" + right +
                '}';
    }
}
````



