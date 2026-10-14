# 线性表 Linear Table

# I. 线性表的基本概念

### 1. 线性表的定义

线性表是由n (n≥0)个相同数据类型元素组成的有限序列。其中 n为表长；线性表可以为空表（当n=0时），若用L表示一个线性表，则其一般形式可以为

$L = (a_1,a_2,...，a_i,a_{i+1},...,a_n)$

a1是表头元素，an为表尾元素。除第一个元素外，每个元素有且仅<font color="#2DC26B">有一个直接</font>前驱；除去最偶一个元素外，每个元素有且只有一个直接后继。

以上为线性表的逻辑特征，这种一对一的线性逻辑关系正式线性表的特点。


### 2.线性表的特点

- 表中的元素个数有限
- 表中的元素具有逻辑上的顺序性，存在明确的先后次序
- 表中的元素均为数据元素，每个元素在线性表中是一个不可分割的单位
- 表中的元素的数据类型都相同，表明每个元素占有相同大小的存储空间
- 表中的元素具有抽象性

> 线性表是一种用来表明元素之间，存在一对一相邻关系的逻辑结构。顺序表和链表是两种存储结构，是对线性表的具体物理实现方式。这一点一定要区分清楚。就像后面树也有顺序存储和链式存储实现的树
> 

### 3. 线性表的基本操作

| Operator name | Operator name | Operation annotation |
| --- | --- | --- |
| InitList(&L) | 初始化表 | 构建一个空线性表，并完成相应的准备工作（初始化工作） |
| Length(L) | 求表的长度 | 返回线性表 L 的长度，即表中的元素个数 |
| PrintList(L) | 打印线性表 | 将线性表 L 中存在的元素打印出来 |
| LocateElem(L, ele) | 按值查找 | 在表 L 中查找值为ele 的元素（在表 L 中查找具有指定关键字值的元素),并返回其位置 |
| GetElem(L, i) | 按位查找 | 获取表 L 中第i 个位置的元素值 |
| ListInsert(&L, i, ele) | 插入元素 | 在表 L 中的指定位置i 中插入一个元素ele |
| ListDelete(&L, i, &ele) | 删除元素 | 在表 L 中的指定位置i 中删除一个元素ele |
| Empty(L) | 判空操作 | 判断线性表是否为空🈳，若L 表为空则返回true, 不空返回 false |
| DestroyList(&L) | 销毁线性表 | 销毁线性表，并通过内部的 free(L)释放占用的内存空间 |

> [!NOTE]
>    注意，如果你对 C++中的“&”引用操作符不熟悉的话，在考试中请不要随意写&，或干脆不写他。以防造成扣分。毕竟考试中的算法题考察的算法的逻辑和思想，不要求实现出来。

# II 顺序表

### 1. 顺序表的定义

线性表的顺序存储也称为顺序表。顺序表是用顺序存储的方式，实现线性表的顺序存储。也就是把逻辑上相邻的元素存储在物理位置上也相邻的存储单元中，元素之间的关系由存储单元的邻接关系来实现。

顺序表的特点

- 逻辑上相邻的元素在物理位置上也相邻
- 表中元素的逻辑顺序和物理存储顺序完全一致

> 线性表中元素的位序从 1 开始，而数组中元素的下标从 0 开始，要注意这两者之间的转换⇄
> 

### 2. 顺序表的具体实现

#### I. 定义一个顺序表（structure 结构）

一维数组既可以静态分配，也可以用动态分配的方式来完成顺序表的建立。

- 在静态分配时，数组的大小和空间在编译🧬时已经被确定，一旦空间被占满就无法再插入新元素。强制插入新元素可能会导致元素溢出，进而引发程序发生崩溃（异常abort trap）
- 动态分配时，存储数组的空间是在程序执行过程中，通过c语言的 malloc()函数进行动态申请。根据用户想输入数据的所需长度，自动的进行内存容量的申请。具体可以先为用户划分出一块固定长度的空间进行数据存储，如果不够的话，再一次申请一次内存空间，并让已经申请的内存空间通过一个指针指向后面新生成的内存空间，从而组成一个新的整体。

![dynmaic allocate.png](%E7%BA%BF%E6%80%A7%E8%A1%A8%20Linear%20Table/dynmaic_allocate.png)

这里先给出静态分配的顺序表的结构。

这里我们先通过宏定义确定顺序表的最大空间MaxSize，也就是顺序表的长度。再用 C/C++特有的typedef 关键字为数据类型起别名。方便后期数据类型的统一管理。具体代码如下：

```cpp
// SequenceList table insert
// 静态顺序表结构定义
#define MaxSize 50
typedef int ElemType;   // 引入别名来更好的统一数据类型结构 easy to change the Data structure type
typedef struct{
    ElemType data[MaxSize];    // 定义顺序表数组长度
    int length; //sequence table length 定义顺序表表长度
}SequenceList;
```

#### II. 顺序表插入元素

首先在插入任何元素前，先根据表长和要插入的索引序号进行判空操作。防止因表满或误输入导致崩溃。在插入过程中利用 for 循环♻️，依次从表的后面开始遍历，让索引不断指向表头，同时在搜索元素的位置的过程中不断向后交换元素的位置，为插入的元素留出一个空位🈳。至到找到那个索引位置，循环结束。并把要插入的元素插到空位上，即 pos-1的那个位置上，并增加表长。

```cpp
bool ListInsert(SequenceList &L, int pos, ElemType element) 
{
    // judge the valuable 'pos' is vaild and legal
    if(pos < 1 || pos > L.length+1)
    {
        return false;
    }

    //if the storage is full, cannot excuate the insert operator
    if(L.length == MaxSize)
    {
        return false;
        // insert fail
    }

    /*
    Shift the subsequent elements backward sequentially to create space and insert 
    the elements to be inserted.
    Move the subsequent elements backward one by one to create space and insert the 
    elements to be inserted
    */
    for(int j=L.length; j>=pos; j--)
    {
        L.data[j] = L.data[j-1];
    }
    L.data[pos-1] = element;    // set the element which is need to insert
    L.length++; // lengthen the sequenceTable List
    return true;    //insert success
}

```

#### IV. 打印顺序表

这个很简单，依次根据顺序表的索引 index 向后遍历即可

```cpp
// Print the sequence List
void PrintList(SequenceList L)
{
    int i = 0;
    for(i = 0; i < L.length; i++)
    {
        printf("%3d", L.data[i]);   // print the termial on the same line
    }
    printf("\n");
}
```

#### V. 删除顺序表中的元素

这个就是把顺序表的插入元素的操作反过来。

```cpp
// delete the element in the sequence list
// pos is the position of the element to be deleted
// element is the element to be deleted
bool ListDelete(SequenceList &L, int pos, ElemType &element)
{
    // judge the valuable 'pos' is vaild and legal
    if(pos < 1 || pos > L.length)
    {
        return false;   //if the program go here, the function will be terminated
    }

    element = L.data[pos-1]; // save the element to be deleted
    int j;
    
    // solution 1:
    // for(j=pos-1; j<L.length-1; j++)   //j.length-1 is the last element of the sequence list,
    // {                               // not the j.length, because j.length is not the last element after delete the element
    //     L.data[j] = L.data[j+1];
    // }

    // solution 2:
    for(j=pos; j<L.length; j++)   //j.length-1 is the last element of the sequence list,
    {                               // not the j.length, because j.length is not the last element after delete the element
        L.data[j-1] = L.data[j];
    }
    L.length--; //shorten the sequence length.

    return true;
}
```

#### VI. 对顺序表进行按值查找

这个也很简单。如果对时间复杂度不作要求的话，枚举法一次遍历元素是进行按值查找最好的办法！

这里先获取要查找的值，然后根据表长获去索引。从第一个元素依次向后遍历，知道找到和表中值一模一样的，并返回索引。否则 return 0 退出表是没有找到，表中没有这个值。

```cpp
// Finding the element position, and recongise the relate position, rather than return the value 0
int LocateElem(SequenceList L, ElemType element)
{
    int i;
    for(i=0; i<L.length; i++)
    {
        if(element == L.data[i])
        {
            return i+1; //because the variable i is the array's index, i+1 is the sequence List index
        }
    }
    return 0; //cannot find this element value in the sequence list
}
```

# III 链表

尽管顺序表支持随机存取，但是其插入和删除需要移动大量元素，效率太低。如果我们可以利用内存的堆区 Heap，向他要一块不连续的离散的空间，并利用指针指向他们的地址，那样将事半功倍。

### 1. 单链表的定义

线性表的链式存储也称为单链表。它通过一系列任意的存储单元（不要求地址连续）来存储线性表中的元素。为了保持数据间的线性关系，每个链表在存放自己元素数据的前提下还需要存放指向下一个结点的指针，其中 data 为数据域，用于存放数据元素；next 为指针域，用于存放其后继结点的指针。

![linkList.png|364](%E7%BA%BF%E6%80%A7%E8%A1%A8%20Linear%20Table/linkList.png)

单链表的结构定义

```cpp
#define MAXSIZE 1000

// Create the LinkedList struct
typedef int ElemType;
typedef struct LNode{
    ElemType data;  //Data field
    struct LNode *next;     // next element pointer field
}LNode, *LinkList;
```

## 2. 链表的实现

### A. 链表插入操作的实现

![linkedlisttail.png](%E7%BA%BF%E6%80%A7%E8%A1%A8%20Linear%20Table/linkedlisttail.png)

### 1.1 头插法

带有头结点的链表依次插入元素先对于尾插法要简单一些。

首先先创建一个链表的头结点，用 malloc 为它在内存中开辟一块空间。大小为一个结点（LNode*）。并为它清空地址取NULL，方便链接🔗后面要输入数据的元素。

注意⚠️，千万别忘了，用 malloc 函数创建的变量默认类型为空🈳。即void*，这里我们要创建和定义链表相同的类型，要用LinkList。

然后单独创建一个变量x，用来接收用户输入的数据，因为链表我们不需要为它定义长度，所以不需要考虑输入最大的边界情况，结点的地址分配在内存中也是离散的，不连续的。

然后我们设定一个让链表停止🛑接收数据的标志位数字，这里定义为9999。当用户敲下 9999 时，退出链表插入元素的循环♻️。

为了可以一次性插入多个数据到链表，这里我们用一个 while 循环（不要用 for 循环，因为链表没有索引，并非像顺序表一样通过递增或递减来进行下一步的操作），并把X≠ 9999作为跳出循环的条件。

进入循环后，我们要 用 malloc 创建一个指向新结点的指针 s，s的数据域也就是s→data 此时要接收 x刚刚用户输入的数据。并将 L→next 赋给s→next,因为这里 L→next 指向的是结点的末尾，现在 s指针要带着新元素插入链表，自然 s要继承来自L→ next指向末尾的结点。 然后让 L→next 链接上s，完成链表的插入。

接着用 scanf 接收用户输入的下一个数据，并循环这个操作。

> [!warning]
> 要特别注意，这里的 L→next = s 与 s → next = L → next 的顺序尽可能不要搞反，因为即便 s指针在接收元素后没有指向下一个节点，在内存的分配中可能会指向一个未知的值。为了保证接下来插入的元素地址可以连接上，尽量用所展示的代码顺序， 除非你定义了s→next = NULL的选项 在接下来的尾插法中，顺序的次序就变得非常重要！错误的顺序可能会导致下一个结点指向结点自身（回环）。下面的尾插法还会仔细探讨这个事情。
> 


```cpp
void list_head_insert(LinkList &L){
    L = (LinkList)malloc(sizeof(LNode));    //apply for head node memory space
    L->next = NULL;
    ElemType x;
    scanf("%d", &x);
    LNode *s;

    // s = (LinkList)malloc(sizeof(LNode));
    // s->data = x;
    // s->next = NULL; //‼️s node next must be NULL.
    // L->next = s;    //head node next -> point to the first element. link to the s contain data element
 
    while(x != 9999)
    {
        s = (LinkList)malloc(sizeof(LNode));
        s->data = x;
        s->next = L->next;  //s next point to the first node which is the orginal linklist
        L->next = s;    // head node's next, 
        scanf("%d", &x);
    }
}
```

![image.png](%E7%BA%BF%E6%80%A7%E8%A1%A8%20Linear%20Table/image.png)

#### 1.2 尾插法

尾插法和头插法最大的区别就在于：通过r指针向右移动，让新插入的元素结点 s可以依次向右链接🔗起来。这样遍历链表的时候得到的序列就和你的输入序列是一致的。

前三部初始化链表L、接收新输入x，创建新结点s都与头插法差异不大。下一步创建用于顺序链接结点的 r指针是尾插法得以实现的关键。

首先对s = r = NULL 做初始化，然后先用s指针接收输入的值s→data = x, 并将s结点赋给 r → next，即r → next = s，然后将s指针的值全部放到 r指针中，即r = s。接着再接受下一个输入的值。

注意当链表结束时，需要将r指针的 next 指向 NULL，即 r→next = NULL。

> [!warning]
> 注意⚠️：r→next = s 与 r=s两部在这里绝对不能颠倒🙃，否则会导致指针反复指向自身，无法插入结点。
> 

```cpp
// LinkList tail insert the element
void list_tail_insert(LNode* &L)
{
    L = (LinkList)malloc(sizeof(LNode));
    L->next = NULL;
    ElemType x;
    scanf("%d", &x);
    LNode *s, *r = L;   // s variable is seeking for the new node, r point to the end of the linklist
    while(x != 9999){
        s = (LinkList)malloc(sizeof(LNode));
        s->data = x;
        r->next = s;    // new node to the r end pointer
        r = s;  // r point to the new tail region
        scanf("%d", &x);
    }
    r->next = NULL; //set the tail's point next is NULL.
    //tail insert
}
```

> [!tip]
>  注意⚠️： 链式存储实现线性表时，尽管分配的内存空间是离散的，但节点内的存储单元地址一定连续的
### B. 链表的删除操作

链表的删除的实质就是链表的元素之间的“断链”操作。

很简单，如果一个元素要脱离链表，只需要删除这个元素与其他元素的地址关联即可。这里需要用到链表的按位查找get_elem，通过按位查找找到需要断开链表的元素。

⚠️注意，因为链表的指针是指向的下一个元素，这里实际上需要查找的是第i-1位次上的元素。找到i-1位次上的元素后，再通过那个元素的下一个指针找到对应的 q。

首先先把 p→next，即指向被删元素的地址付给被删元素q，q也需要把他自己指向的下一个地址给 p,方便 p跳过被删元素q，连接到🔗下一个元素。等删除关于q的地址关联后，用free函数讲q释放。

```cpp
// delete the element which position on i.
// delete the element will no change the L list. you haven't to add alise.
bool list_delete(LinkList L, int i)
{
    LinkList p = get_Elem(L,i-1);   // fetch the delete node in front of the node. 
    // CAUTION
    if(NULL == p)
    {
        return false;
    }
    LinkList q = p->next;    // fetch the delete node address (pointer)
    // Warning
    if(NULL == q)   // if linkedlist only have 5 nodes, delete the No.6 node will occur an error. this block is to solve this issue
    {
        return false;
    }
    p->next = q->next; //   cut link
    free(q);    //rele

    return true;
}
```

### C. 链表的按值查找与按位查找

第一种Locate_Glam按值查找很简单，类似于枚举算法（实际上链表的结构比较特殊，没办法实现随机查找，只能一个个按照查，当然了，二分查找就跟这个结构没什么关系了）。

首先获取链表，并设置一个需要在链表中查找的一个值。之后利用循环♻️，利用 L→data 指针依次遍历链表中的每个元素，看哪个元素可以与需要查找的元素searchValue 对应上。如果发现存在可以对应上的元素，即输出这个元素的值，否则返回🔙NULL 代表没有找到。

get_Elem 按位查找呢，这个就是在链表中，按照提供的元素“位次”（相当于找链表中的第几个元素），返回这个位次对应的值。注意啊⚠️，这里不是按照元素的地址返回值（并不是单纯的值，是一个结构体）。

其实大体逻辑和上面的差不太多，区别是这个需要额外一个变量i 用于返回值对应的次序。

首先判断用户输入的次序是否合法，不允许❎用户输入小于 0 的次序（因为那个不存在）。然后通过设定的变量 i=0，通过 i和searchPos的比较得出 i还需要向后移动多少元素才可以到达用户给出的位置，和这个位置他所对应的值。如果输入不合法或搜索一圈没有，则返回 NULL，如果找到，就返回这个元素的结构体（包含他的值和指针）。

<aside>
<img src="https://app.notion.com/icons/info-alternate_green.svg" alt="https://app.notion.com/icons/info-alternate_green.svg" width="40px" />

这里一定要注意啊：为了保证链表循环的条件，方便链表的指针可以依次访问以下元素，一定要写上L=L→next; 方便指针指向下一个元素。

如果不写的话，会造成死循环，程序无法继续下去

</aside>

```cpp
// linkLocateGlem
LinkList locate_Glem(LinkList L, ElemType searchValue)
{
    while(L)
    {
        if(L->data == searchValue)  // if can find that value in the List, return the value node's address.
        {
            return L;
        }
        L = L->next;
    }
    return NULL;
}

// you can also use the LNode*
LinkList get_Elem(LinkList L, int searchPos)
{
    int i = 0;
    if(searchPos < 0)
    {
        return NULL;
    }
    while (L && i < searchPos) {    // L cannot be NULL, 
        L = L->next;
        i++;
    }
    return L;
}
```

### D. 链表的修改

链表的修改，它的操作实质和链表元素的删除是差不多的。本质上就是将元素相邻节点的地址链接🔗进行变化的过程。

首先接收用户发来的链表，并指明需要在链表中的哪个位置插入元素，并填入插入元素的值。

链表的修改和删除都要执行判空操作，具体的判空过程这里不再赘述了。

首先用 malloc 开辟一块内存空间 q 用于存储用户修改的节点。并用 q → data 接收用户节点的值。

p→next 也就是用户指明在哪个位置需要进行修改的元素。因为现在新元素还没有与链表中的元素建立联系，此时p→next 指向的还是链表中下一个节点的元素

![image.png](%E7%BA%BF%E6%80%A7%E8%A1%A8%20Linear%20Table/image%201.png)

再将p → next 连接到 q的新元素上。因此这样就完成了元素的插入。

![image.png](%E7%BA%BF%E6%80%A7%E8%A1%A8%20Linear%20Table/image%202.png)

<aside>
<img src="https://app.notion.com/icons/info-alternate_red.svg" alt="https://app.notion.com/icons/info-alternate_red.svg" width="40px" />

注意，这里的插入操作顺序不能颠倒，如果顺序错了，节点可能不会被修改。甚至重复指向节点自身。

</aside>

要特别注意⚠️ q→next = p → next 和 p → next = q 绝不能颠倒。如果先执行 p → next = q  的话会导致元素指向自身，后面的地址元素就连接不上了。

```cpp
bool list_front_insert(LinkList L, int insertPos, ElemType insertValue)
{
    LinkList p = get_Elem(L, insertPos-1);
    if(NULL == p)
    {
        return false;
    }
    LinkList q;
    q = (LinkList)malloc(sizeof(LNode));
    q->data = insertValue;
    q->next = p->next;
    p->next = q;
    return true;
}
```

### E. 链表的读取

遍历链表就很简单了，我们只需要将接收到的链表 L依次向后读取，获取每个元素中L→data的值即可，直到读到L→next == NULL 为止（链表已经没有后继结点了）。

```cpp
void print_list(LinkList L)
{
    L = L->next;
    while(L!= NULL)
    {
        printf("%3d", L->data);
        L = L->next;
    }
    printf("\n");
}
```

# IV 顺序表 🆚 链表

|  | 顺序表 | 链表 |
| --- | --- | --- |
| 存储密度 | 高（可以实现连续存放） | 低，通过地址的链接。在内存中的堆(Heap)区离散存放 |
| 是否支持折半查找（二分查找） | 支持 | 不支持 |
| 是否可以进行随机访问(随机存取) | 是✅ | 否❌，必须依次访问元素 |
| 删除与插入结点的时间复杂度 | O(n) <删除最后一个元素结点为O(1)> | O(1) |
| 逻辑结构与物理结构 | 逻辑上相邻的元素在物理存储上也必须连续存放，邻接关系可以由地址体现 | 逻辑相邻的元素在物理上未必相邻，结点间的逻辑关系通过指针进行维护链接🔗 |

<aside>
<img src="https://app.notion.com/icons/info-alternate_green.svg" alt="https://app.notion.com/icons/info-alternate_green.svg" width="40px" />

提示 tips：线性表的顺序存储与链式存储两者分别有各自的优缺点，两种方式并没有绝对的优势；真实项目中会针对不同的开发场景，灵活采用不同的方式。

对于数据规模n稳定，以随机访问为主的场景，适宜采用顺序存储；对于需要高频率进行插入与删除的场景，链式存储更优。

</aside>

# V. 其他不同种类的链表

## a. 双链表

## b. 循环链表♻️

参考后面的循环队列

## c. 静态链表

# VI 实例代码

[Code page](%E7%BA%BF%E6%80%A7%E8%A1%A8%20Linear%20Table/Code%20page%203a472c7c230a808097decd73cc929a6e.md)

[线性表的算法题目](%E7%BA%BF%E6%80%A7%E8%A1%A8%20Linear%20Table/%E7%BA%BF%E6%80%A7%E8%A1%A8%E7%9A%84%E7%AE%97%E6%B3%95%E9%A2%98%E7%9B%AE%203a472c7c230a80d6b05ec6ab4a5f9610.md)

![image.png](%E7%BA%BF%E6%80%A7%E8%A1%A8%20Linear%20Table/image%203.png)