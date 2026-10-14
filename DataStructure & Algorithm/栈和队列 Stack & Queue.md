# 栈和队列

# I. 栈 Stack

## a. 栈的定义

栈(Stack)是一种仅允许在一端进行插入和删除操作的线性表，也可以说它是一种输入和输出受限的线性表；栈的插入（入栈）和删除（出栈）操作只能在表的一端进行。

+ 栈顶(Top)：允许进行插入和删除操作的一端
+ 栈底(Bottom)：固定不变、不允许进行插入和删除操作的另一端
+ 空栈：不包含任何元素的栈
另外，栈是线性表的一种，因此栈也有两种基本的存储方式——顺序存储和链式存储。

> [!tip]
> 
> 栈的逻辑结构与普通的线性表相同，数据的运算，插入和删除操作有区别

## b. 栈的基本操作

| Operator name    | Operator name | Operation annotation            |
| ---------------- | ------------- | ------------------------------- |
| InitStack(&S)    | 初始化栈          | 为栈初始化内存分配空间                     |
| StackEmpty(S)    | 判断栈是否为空       | 判断栈是否为空，如果栈为空，则返回True,否则返回False |
| Push(&S, x)      | 入栈操作          | 若栈未满🈵，则x 成为新的栈顶元素              |
| Pop(&S, &x)      | 出栈操作          | 若栈非空🈳，则弹出栈顶元素，并通过 x 返回该值       |
| GetPop(S, &x)    | 获取栈顶元素        | 读取栈顶元素但不出栈，若栈非空，通过 x 返回栈顶元素     |
| DestroyStack(&S) | 删除栈           | 释放栈所占用的内存空间                     |

## C. 栈的数学性质

### 统计出栈序列的总数——用卡特兰数进行统计
当n个不同的元素按照固定的次序入栈时，可能的出栈序列总数为： 1/n+1 Cn 2n, 该序列被称为***卡特兰数*** 。 下面⬇️这个例子🌰是 5 个不同的元素可能产生的出栈顺序的举例：
$$
\frac{1}{1+5}C_{10}^{5} = \frac{10 \times 9 \times 8 \times 7 \times 6}{6 \times 5 \times 4 \times 3 \times 2 \times 1} = 42
$$

> [!warning]
> 这里要小心，2026 年 408 真题在这里进行了考察，有可能今年在选择题上再次“偷袭”
> 

## D. 栈的顺序存储结构
### 1. 顺序栈的实现（顺序栈的初始化）

顺序栈的初始化操作和顺序存储的线性表差不多。采用顺序存储的栈称为**顺序栈**。顺序栈是由一组地址连续的存储单元存放从栈底到栈顶的数据元素，并用一个栈顶指针``top`` (整型)指示当前栈顶元素的位置。
*下面这行代码描述的是：顺序栈的结构体定义，这里定义的顺序栈大小是静态的，不会动态改变。（栈的大小已经在程序里面通过 define 宏定义定义好）*

```cpp
	#define MaxSize 50 //定义栈的长度
typedef int ElemType;
typedef struct{
	ElemType data[MaxSize]; //array 用数组实现的顺序栈，用于存放栈中的元素
	int top; //point to the top of the stack varible.  // 定义栈顶指针
}SequenceStack;

typedef SequenceStack SqStack;

void InitStack(SequenceStack &S)
{
	S.top = -1; // let the stack empty.
}

```

### 2. 顺序栈的基本操作

#### i. 出入栈的操作

首先，因为 ``Stack`` 栈作为一种输入和输出只能在一段的线性表，因此必须要明确栈的出栈和入栈元素的次序要求，即``后进先出LIFO``的原则 (Last in First out)。
为了实现这个原则，我们需要明确栈在数据存储中拥有三种基本形态——(a)空栈，(b)栈内元素出栈和入栈 (c) 满栈

通过一个简单的例子🌰来阐述出入栈的操作：
假设有分别有 5 个元素A、B、C、D、E依次入``S栈``，其中在 ABC 依次入栈后，C 会先弹出栈，然后 DE 再依次入栈。待元素入栈完成后，利用 top指针依次自顶向下指向栈内剩余的元素，完成栈的出栈操作。这个动作将一直持续到``S 栈``栈空栈的状态。
其中如图所示：栈顶指针top作用——它标识了栈中有效元素的边界。

##### b. 判断栈空

判断栈是否为空🈳的基本原理：
通过Stack栈的top指针指向的元素序列，来判断当前栈顶指针top是否位于栈底-1 的位置。若``-1 == S.top `` 意为栈空，当前栈内没有任何元素，否则栈内存在元素

因为这里的栈我们假装栈的空间是可以动态申请的，空间可以无限大，所以没有设置栈满的条件。链式存储的栈可以"无限"的进行入栈操作，注意⚠️这里的”无限“是相对于顺序存储的栈而言，实际上链式存储的栈的空间分配，也是需要根据操作系统 OS 可以为进程分配的最大内存容量而定的。并非真正的无限。

```cpp
bool StackEmpty(SequenceStack S)
{

	if(-1 == S.top)    // 当前栈为空
	{
		return true;
	}
	else
	{
		return false;   // 当前栈不空，栈内有元素
	}

}
```

##### c. 入栈操作

在元素进行入栈操作前，为了防止栈溢出，需要在入栈前对栈内剩余空间进行检测，确保栈内还有足够的空间可以分配给入栈的元素。
如果满足入栈的条件，则将保存入栈元素的变量``ele`` 通过``S.data[++S.yop]`` 加入到栈中。
这里有一个很关键的操作，不要搞混

> [!info]
>  关于a++ 和 ++a 的操作
>  a++ 表示先不变 a的元素值，然后再对他进行+1 操作。
>  例如 a  = 4时，若存在数组array[a++] 时，代表 array[4]，并不是array[5]。但已经执行过a++, 再次访问 a时会得到 a = 5的结果
>  ++a 表示先执行 a + 1运算，也就是先改变 a的元素值，再表示更新后 a的元素值;
>  例如  例如 a  = 4时，若存在数组array[++a] 时，代表 array[5]。已经执行过++a, 再次访问 a 时会得到 a = 5 的结果

所以运用一下特性，我们可以用

```cpp
S.data[++S.top] = ele;
```

来取代下面这样的写法：

```cpp
S.top = S.top+1;
S.data[S.top] = ele;
```

具体代码如下：

```cpp
bool Push(SequenceStack &S, ElemType ele)
{

	// judge the stack is full or empty  先执行判空操作，判断栈内是否有剩余空间
	if(S.top == MaxSize-1)	
	{
		return false;
	}
	S.data[++S.top] = ele; // == S.top = S.top+1,S.data[S.top] = x 利用top 栈顶指针进行入栈操作
	
		return true;   // 入栈成功返回 true

}
```

##### d. 出栈操作

关于出栈最重要的是——判断栈是否为空。
判断栈空非常重要，如果栈里没有任何元素的话，强制弹出元素会触发异常情况。
如果栈不为空的话，通过降低``S.top--`` 指针来依次弹出栈内元素。

```cpp

bool Pop(SequenceStack S, ElemType &m)
{
	if(StackEmpty(S))
	{
		return false;	
	}
	
	m = S.data[S.top--]; // out of the stack. pop the element
	
	return true;
}
```

##### e. 读取栈顶元素

读取栈顶元素并非弹出⏏️栈顶元素，两者具有本质区别！
设定读取栈顶函数GetTop有两个参数，一个用于接收需要进行读取栈顶元素的顺序栈，另一个用于接收读取到的栈顶元素。
同样，先判断栈是否为空，如果为空栈，则返回 false 退出函数运行。如果栈不为空，则通过栈顶指针``S.top`` 获取栈顶元素，即``S.data[]`` 中的元素。

> [!warning]
>   要特别注意，读取栈顶元素 并不是 弹栈（弹出栈顶元素⏏️）两者具有本质区别，不要搞混。
> 

```cpp

bool GetTop(SequenceStack S, ElemType &m)
{

	if(StackEmpty(S))
	{
		return false;
	}
	m = S.data[S.top];
	
	return true;

}
```


#### ii. 共享栈 (考的不多，可能出选择题)

共享栈是利用栈底位置相对固定的特性，让两个顺序栈同时共享同一段一维数组空间，并将两个栈的栈
底分别置于数组两段，栈顶向中间延伸。
共享栈可以高效地利用存储空间，两个栈的空间可以动态调整，仅当整个数组被占满时，才发生栈溢出。其存储数据的时间复杂度均为 ``O(1)``, 对存取效率无影响。

contents from Claude
[[DataStructure & Algorithm/SharedStack]]

## F. 栈的链式存储结构（与链表很像）


## G. 关于栈 Stack 数据结构可以考察的方向
### 1. 判断出栈顺序的合法性

### 2. 统计n个栈内元素可能存在多少种出栈顺序

### 3. 考察顺序存储的栈和链式存储的栈的代码细节


---
# II. 队列 Queue

> 尽管队列的代码题应该不会出很难，但是关于元素的进出序列顺序，甚至是以后的树🌳的层序遍历, 广度优先遍历BST都需要用到队列，乃至有可能是双端队列。这里一定要重视！

## a. 队列的定义

队列(Queue) 简称队，与栈都是一种输入输出受限的线性表。与栈不同的是：队列允许在一端插入(入队)、在另一端删除(出队)。队列的操作特性为先进先出(FIFO)。这种操作类似于操作系统 OS 进程调度算法的——先来先服务调度算法

队列分为两个部分
+ 队头 (Front) 允许元素删除的一端，也称为队首
+ 队尾 (Rear) 允许元素插入的一端
因为队列也属于一种线性结构，线性表存在空表，同样队列也可以是空队列。空队列内部不包含任何元素。
## b. 队列的操作

| Operator name   | Operator name | Operation annotation                |
| --------------- | ------------- | ----------------------------------- |
| InitQueue(&Q)   | 初始化队列         | 为队列初始化内存分配空间                        |
| QueueEmpty(Q)   | 判断队列是否为空      | 判断队列是否为空，如果Q 队列为空，则返回True,否则返回False |
| DeQueue(&Q, x)  | 入队操作          | 若队列未满🈵，则将新元素x 插入队尾                 |
| EnQueue(&Q, &x) | 出队操作          | 若队列非空🈳，则删除队首元素，并将队首元素值赋值给 x        |
| GetHead(Q, &x)  | 获取栈顶元素        | 读取队首(首)元素但不出队，若队列非空，则将队首元素值赋值给 x    |

> [!warning]
>  因为栈和队列都是输入输出受限的线性表，因此并非所有线性表的操作都适合。
>  例如🌰： 不允许直接访问或修改栈或队列中间的元素。 这是由队列和栈的逻辑特性决定的
> 

## c. 队列的顺序存储结构

### 1. 最普通的队列——顺序存储实现的队列

用顺序存储实现的队列实际上与顺序表的实现相差无几。队列的顺序是指在内存中分配出一块地址连续的存储空间，存储空间内的每个单元用来存放队列元素。并附设两个指针：
+ 队首指针``prior`` 用来指向队首元素
+ 队尾指针``rear`` 用来指向队尾元素的下一个位置

队列存储的顺序实现结构体代码：

```cpp
#define MaxSize 50
typedef struct{
	ElemType data[MaxSize];  // 用数组存放队列的元素
	int front,rear;    // 队首指针和队尾指针
}SqQueue;
```

***《数据结构》严蔚敏 C 语言版*** 中的结构体代码：

```cpp
#define MaxSize 100
typedef struct{
	QElemType *base;    // 存储空间的基地址
	int front;   //头指针 （队首指针）
	int rear;    //尾指针 （队尾指针）
}SqQueue;
```

队列的初始状态：``Q.front = Q.rear = 0`` 
入队操作: 当队列未满时，先将元素存入队尾，再将队尾指针加 1
出队操作: 当队列非空时，先取出队首元素，再将队首指针+1

小提示：

> [!tip]
>   队列初始时，空队列满足 ``Q.front == Q.rear == 0`` 该条件可作为队列判空依据。

#### 关于顺序实现的队列的缺陷——假溢出问题

试想一下能否用 ``Q.rear == MaxSize`` 作为队列列满条件？不可以🙅‍♀️

<center></center>![[Pasted image 20260817002348.png|440]]

假设一个顺序实现的队列数组的最大空间为 6，当队列此时处于上图情况时不可能再继续插入新的元素。否则会发生溢出现象；也就是因数组越界而导致程序的非法操作错误。但此时并非真正的溢出情况，在``J5`` 后面还有相当很多空间没有被使用。队列仍然有实际可用的空间。这种现象称为：假溢出。

假溢出现象是因为队列的”队尾入队、队头出队“ 这种限制导致的。这时候用循环队列是再好不过的选择
### 2. 循环队列♻️

循环队列将顺序存储空间在逻辑结构上组织为一个环状。通过取模运算`%` 当队尾指针到达数组末尾时，自动回落到队列的开始位置（也就是队头的位置，起始位置）。

![[Circular_Buffer_Animation.gif|462]]

***基于循环队列结构的循环缓冲机制——Circular buffer. origin from Wikipedia***

| 操作Operator | 条件 judge condition                        |
| ---------- | ----------------------------------------- |
| 初始状态       | `Q.front = Q.rear = 0`                    |
| 队首指针(进)+1  | `Q.front = (Q.front + 1) % MaxSize`       |
| 队尾指针(进)+1  | `Q.rear = (Q.rear + 1) % MaxSize`         |
| 队列长度       | `( Q.rear + MaxSize - Q.front) % MaxSize` |
| 出入队操作      | 指针均按照顺序方向移动                               |
注意⚠️：这里有一个小细节：在循环队列中，不能以头、尾指针的值是否相同来判断队列空间是否“满”还是“空”。以下是两种区别队列空和满的办法：
+ 少用一个元素空间，即队列的空间大小为 n时，有 n-1 个元素时，就认为这个队列是队满的。判断空条件不变，当队列空时，仍然可以使用头尾指针相同的方式来进行判断。
	+ 队满的条件 `(Q.rear + 1) % MAXSIZE == Q.front`
+ 另外也可以设置一个标志位来区别队列此时为空还是满的
#### a) 循环队列的操作

1. 循环队列的初始化
*为了降低理解的难度，这里有限提供和展示内存静态分配的队列代码*
*算法步骤：*
首先为队列分配一个大小为 MaxSize 的数组空间 并设置指向队列头的指针 front 和 队尾指针rear。 
并为他们进行初始化操作，将头指针和尾指针都设置为0，意为队列空。

*算法实现：*

```cpp
#define MaxSize 6
typedef int ElemType;
typedef struct {
    ElemType data[MaxSize]; //MaxSize - 1
    int front, rear;
}CircularQueue;

typedef CircularQueue CirQueue;

void InitQueue(CircularQueue &Q)
{
    Q.front = Q.rear = 0;   //initalize the circularQueue, set the queue head and its tail point to 0
}
```

2. 判断队列空
*算法步骤：*
很简单，就是刚才初始化队列的重复，通过两个指针是否相等判断，队列中是否有元素存在。
但不要小瞧这个步骤，这个步骤为避免队列溢出打下了牢固基础！
*算法实现：*

```cpp
// Judge the queue is empty.
bool IsEmpty(CircularQueue Q)
{
    return Q.rear == Q.front;
}
```

3. 入队

*算法步骤：*
首先进行判满操作，判断此时队列中还能不嫩插入其他数据元素。
如果可以插入，则让队列的 rear 指针先将数据元素x 接收过来，并利用`Q.rear = (Q.rear+1)%MaxSize;`取模操作， 来为队尾指针向后移动一个单位（相当于队尾指针+1），同时也可以判断此时队尾指针是否可以继续进行后移操作。

*算法实现：*

```cpp
bool EnQueue(CircularQueue &Q, ElemType x)
{
    if((Q.rear+1)%MaxSize == Q.front)   //judge whether the Circular queue is Full, the element cannot enter when its Full.
    {
        return false;
    }
    Q.data[Q.rear] = x; // insert the element.
    Q.rear = (Q.rear+1)%MaxSize;    //rear+1, if bigger than the array bigest index, back to the Queue head.
    return true;
}
```

4. 出队

*算法步骤：*
首先判断队列是否为空，如果不为空，则调用队列的队头指针 front，并将它指向的元素存入变量`element`中。
然后再通过`Q.front = (Q.front+1)%MaxSize;` 将队头指针前移。

*算法实现：*

```cpp
// DeQueue
bool DeQueue(CircularQueue &Q, ElemType &element)
{
    if(Q.rear == Q.front)   // Queue is empty, cannot DeQueue
    {
        return false;
    }
    element = Q.data[Q.front];
    Q.front = (Q.front+1)%MaxSize;
    return true;
}
```

5. 求队列长度

*算法步骤：*
这里需要重点说一下，这里有比较的多的细节
因为这里的队列是一种循环队列。会存在队首front指针和队尾rear指针相距过近的情况，很难通过单一的rear - front 后指针 减去 前指针（也就是大减小）的方式判断队列的大小以及所存元素个数。此时 务必对他们加上`MaxSize` 并对整体结果用 `MaxSize` 进行取余操作。方可得出队列的长度。

*算法实现：*

```cpp
int QueueLength(CircularQueue Q)
{
	return (Q.rear - Q.front + MaxSize) % MaxSize;
}
```

#### 循环♻️队列的优势

> [!NOTE]
>  相对于顺序存储的队列来说，循环队列通过取余操作避免了“假溢出”的问题。
> 
> 

## d. 队列的链式存储 

> 这里提供的队列的链式存储的所有代码都是基于 带有头节点的链式存储的线性表实现的

先对于顺序存储的队列来说，链式队列最大的特点就是：不需要为每个节点提前分配空间（预分配空间），在 OS 给进程分配足够内存的情况下不用考虑输入元素的个数，数据元素的数量可以自由扩充。想存多少，就存多少。

### a) 链式队列的基本操作

1. 初始化

*算法步骤：*
1. 定义一个带有头节点的链式队列，并在结构体中定义它的数据域data和记录📝下一个元素节点的next指针
2. 定义链式队列的队头指针front 和队尾指针 rear.
3.  设置一个初始化函数 InitQueue, 并且利用 malloc 为它生成第一块链式存储的队列的头节点的空间，并让队头指针和队尾指针后指向头节点，表示目前队空的状态。
4. 注意，一定要将队头指针中的 next 地址域指向下一个节点的地址。***这在后面取队头元素和删除队头元素（出队）*** 都非常重要。

*算法实现：*
```cpp
typedef int ElemType;
typedef struct LinkNode{
    ElemType data;
    struct LinkNode *next;
}LinkNode;

typedef struct{
    LinkNode *front, *rear;
}LinkQueue; // 先进先出 First in First out FIFO

// 通过链表来实现队列 use Linked List to achieve the queue.
// 此链表是带头节点的链表 this instance Queue is based on the Linked List with head node.

void InitQueue(LinkQueue &Q)
{
    Q.front = Q.rear = (LinkNode*)malloc(sizeof(LinkNode));
    Q.front->next = NULL;
}
```

2. 判断队列空

*算法步骤：*
 和顺序存储的循环队列是一样的，根据 队头指针和队尾指针相等来判断队内是否有元素，队是否为空。

*算法实现：*
```cpp
bool IsEmpty(LinkQueue Q)
{
    if(Q.front==Q.rear)
    {
        return true;
    }
    else 
    {
        return false;
    }
}
```

3. 入队

*算法步骤：*
1. 首先为新入队的节点创建一个变量pnew，并为它分配一个 LinkNode 链表结构体的空间
2. 用 pnew 的 data 数据域接收需要入队的元素 ele变量内的值，并将 pnew 的 next 指针设置为空 NULL
3. 让队尾指针 Q.rear 接收 pnew。包括地址域 next
至此，队列的入队已经完成✅

*算法实现：*
```cpp
// enter the queue
void EnQueue(LinkQueue &Q, ElemType ele)
{
    LinkNode *pnew = (LinkNode*)malloc(sizeof(LinkNode));
    pnew->data = ele;
    pnew->next = NULL;  // set the node next NULL
    Q.rear->next = pnew;    // queue tail insert the element. 从尾部放入元素
    Q.rear = pnew;  // rear 要指向新的尾部 rear need to point to the new tail node element.
}

```

4. 出队（也叫做删除队头元素）

*算法步骤：*

1. 和顺序存储的循环队列一样，首先判断当前队列是否为空
2. 先设置一个链表类型LinkNode 的变量 q 用于接收弹出队列的元素。
3. 将q 保存的弹出队列的元素赋给 ele 变量
4. 这里的 q的地址域保存着原先队头节点链接🔗的下一个节点的地址，因此务必让队头指针重新指向新的下一个节点。
5. 注意⚠️：这里我们设置了一个异常处理情况：

> [!NOTE]
>  如果发生队列里只有一个元素的情况
>  在这种情况下，强制弹出队列里的元素可能会造成弹出元素失败或内存溢出的情况。
>  为了避免这种情况的发生，我们先判断队尾指针 rear 所指的q 是否存在，如果存在的话，证明队列里只有一个元素。
>  此时很简单，只需要让队头指针和队尾指针指向同一个地址，即指向同一个节点、例如头节点即可。
> 并在最后用 free() 函数将 q释放掉即可

6. 与顺序存储不一样的是，最后不要忘记用 free() 函数将弹出的队头元素节点的删除并释放内存。

*算法实现：*
```cpp
bool DeQueue(LinkQueue &Q, ElemType &ele)
{
    if(Q.rear == Q.front)   // 队列为空
    {
        return false;
    }
    LinkNode* q = Q.front->next;    //拿到第一个结点存到 Q get the first element and store to the Q queue.
    ele = q->data; // get the dequeue data. 获取要出队的元素值
    Q.front->next = q->next;    //让第一个结点断链 cut the first node link chain
    if(Q.rear == q) // Queue left only one node, change the rear pointer after deleting operation. 链表只剩余一个结点时，被删除后，要改变rear
    {
        Q.rear = Q.front;
    }
    free(q);

    return true;
}
```

5. 打印队列元素
> 尽管这个没有在课本上多讲，我还是在这里写一下基本的队列输出，因为队列的输出并非它的默认基本操作。

*算法步骤：* 
1.  这里是利用队列的队头指针队元素节点进行一次遍历，来挨个获取每个数据节点的 data 数据域，从而获取队列中的所有元素的值。
2. 首先默认队头指针没有指向任何值、任何节点
3. 通过循环依次遍历，条件是如果队头指针的下一个节点 next 与队尾指针的 next 不相等，那么就继续循环。并在循环中直接输出队头指针所指的元素的 data 域的值，即队列元素的值
4. 注意为了保证循环继续下去，Q.front 队头指针必须+1 确保指向下一个元素。否则将造成死循环
5. 最后利用循环的“副作用”，获取到了队列里最后一个元素的值，并输出它
自此，队列的循环输出完毕

实际上应该还有其他思路，例如利用 % 取模 运算来一次访问队列数据元素的值。

*算法实现：*
```cpp
void PrintQueue(LinkQueue Q)
{
    Q.front = Q.front->next;
    while(Q.front->next != Q.rear->next)
    {
        printf("%3d", Q.front->data);
        Q.front = Q.front->next;
    }
    printf("%3d", Q.front->data);

    printf("\n end \n");
}

```

与顺序队列相比，链式队列的缺点是不能根据队首指针和队尾指针计算出队列的长度。

## e. 双端队列 (注意⚠️：选择题常客)

### 基本定义

双端队列是一种***允许在两边进行插入和删除*** 操作的线性表。双端队列的左端为前端，右端为后端。双端队列两段没有优先级，是平等的

![[Pasted image 20260822014823.png]]

在双端队列中，可能同时存在两侧分别入队和出队的情况。一般情况下，在双端队列入队时，前端插入的元素排列在后端插入元素之前（因为前端的元素插入会在队列左侧单独堆积），在双端队列出队时，无论是从前端还是后端出队，先出的元素总是比后出的元素要更靠前。

### 输出受限

![[Pasted image 20260822015614.png]]

输出受限的双端队列中，仅有一端（左侧）可以删除（弹出⏏️）和插入队列中的元素。而另一端只能完成插入元素的操作。
### 输入受限

![[Pasted image 20260822015704.png]]

输入受限的双端队列中，允许一端进行插入和删除操作，但是另一端不能完成插入，只能完成删除操作。

> [!NOTE]
>  另外，若限定双端队列从某个端点插入的元素只能从该端点删除，则说明该双端队列就变成为“两个栈底相邻接的栈”
> 

---
# 栈 VS 🆚 队列 Queue

|      | 栈 Stack                                         | 队列 Queue                                                        |
| ---- | ----------------------------------------------- | --------------------------------------------------------------- |
| 逻辑结构 | 与线性表一样，数据元素（数据项）之间存在一对一的关系                      | 和线性表一样，与线性表一样，数据元素（数据项）之间存在一对一的关系                               |
| 存储结构 | 顺序存储：存储空间预先分配，可能会导致内存空间限制或者栈满溢出的情况；另外元素数量不能自由扩充 | 顺序存储(常用于设计成循环队列♻️形式)：存储空间预先分配，可能会导致内存空间限制或者栈满溢出的情况；另外元素数量不能自由扩充 |
|      | 链式存储：动态分配，不会出现闲置或栈满溢出的情况发生。数据元素的个数可以自由扩充        | 链式存储：动态分配，不会出现闲置或栈满溢出的情况发生。数据元素的个数可以自由扩充                        |
| 运算规则 | **后进先出** , 数据的插入和删除都在表的一端(栈顶)完成                 | 先进先出，插入运算在表达一端进行（队尾），删除运算在表的另一端（队头）                             |




---

关于队列的参考资料与文献
from Internet: 🌍

```cardlink
url: https://en.wikipedia.org/wiki/Queue_(abstract_data_type)
title: "Queue (abstract data type) - Wikipedia"
host: en.wikipedia.org
favicon: https://en.wikipedia.org/static/favicon/wikipedia.ico
image: https://upload.wikimedia.org/wikipedia/commons/thumb/5/52/Data_Queue.svg/1280px-Data_Queue.svg.png?utm_source=en.wikipedia.org&utm_campaign=index&utm_content=thumbnail
```

```link_bookmark_block
link: https://www.cs.usfca.edu/~galles/visualization/Search
title: visualization
```

from the localhost disk storage:
Circular buffer: 
![[Circular_buffer.pdf]]

(abstract data type): Queue
![[Queue_(abstract_data_type).pdf]]
# III. 附录

## 数据结构可视化


```cardlink
url: https://www.cs.usfca.edu/~galles/visualization/
title: "Data Structure Visualization"
host: www.cs.usfca.edu
```
