# Binary Tree main.cpp

```cpp
#include <stdio.h>
#include <stdlib.h>

#include "function.h"
#include "queue.cpp"

//前序遍历就是深度优先遍历 pre order == DFS

void CreateTree(BiTree &tree)
{
    BiTree pnew;    // 申请用来指向新申请的树节点 apply a tree node.
    tree = NULL; // 是指向树根的，代表树 point to the tree root, which present tree.
    char c;
    ptag_t phead = NULL, ptail = NULL, list_pnew = NULL, pcur;
    //abcdefghij
    printf("please enter the element that can generate the Bitree:\n");
    while(scanf("%c", &c))
    {
        if(c == '\n')
        {
            break;  //读取到换行就结束 stop accept element when reading the ''\n''
        }
        // 现在需要把结构体内的变量都赋值 NULL，此时可以用一个比较简单的办法-calloc()
        // Now, we need to set NULL to all the variables which is belonging to BiTree strucure. To relieve our complicated work, we can use calloc instead of malloc.
        pnew = (BiTree)calloc(1, sizeof(BiTNode));
        pnew->c=c;
        list_pnew = (ptag_t)calloc(1, sizeof(tag_t));   //为队列节点申请空间 apply nodes for the queue.
        list_pnew->p = pnew;
        // 如果是树的第一个结点
        if(NULL==tree)
        {
            tree = pnew;    // tree指向树的根节点
            phead = list_pnew;  // 指向第一个结点即是队列头，也是队列尾
            ptail = list_pnew;
            pcur = list_pnew; // pcur要进入树的父亲元素
        }
        else 
        {
            // 让元素先入队列
            ptail->pnext = list_pnew;
            ptail=list_pnew;
            // 接下来把b结点放入树中
            if(NULL == pcur->p->lchild)
            {
                pcur->p->lchild = pnew; // pcur->p 左孩子为空就放入左孩子
            }
            else if(NULL == pcur->p->rchild)
            {
                pcur->p->rchild = pnew; // pcur->p 右孩子为空就放入右孩子
                pcur = pcur->pnext; // 当前结点的左右孩子都有了，pcur就指向下一个
            }
        }
    }
}

// 前序遍历，也是深度优先遍历
void PreOrder(BiTree p)
{
    if(p!=NULL)
    {
        printf("%c", p->c); //打印根节点 print root node
        PreOrder(p->lchild);    // 打印左子树 Print left tree
        PreOrder(p->rchild);    // 打印右子树 print right tree
    }
}

void InOrder(BiTree p)
{
    if(p!=NULL)
    {
        InOrder(p->lchild);    // 打印左子树 Print left tree
        printf("%c", p->c); //打印根节点 print root node
        InOrder(p->rchild);    // 打印右子树 print right tree
    }
}

void PostOrder(BiTree p)
{
    if(p!=NULL)
    {
        PostOrder(p->lchild);    // 打印左子树 Print left tree
        PostOrder(p->rchild);    // 打印右子树 print right tree
        printf("%c", p->c); //打印根节点 print root node
    }
}

// layer traversal 层次遍历 广度优先遍历
void LevelOrder(BiTree T)
{
    LinkQueue Q;
    InitQueue(Q);
    BiTree p; //存储出队的结点
    EnQueue(Q, T);  // 把根入队
    while(!IsEmpty(Q))
    {
        DeQueue(Q, p);
        putchar(p->c);  // 等价于printf("%c", c);
        if(p->lchild)
        {
            EnQueue(Q, p->lchild);  // 左孩子不为空，就入队左孩子
        }
        if(p->rchild)
        {
            EnQueue(Q, p->rchild);  // 有孩子不为空，就入队右孩子
        }

    }
}

int main()
{
    BiTree tree;
    CreateTree(tree);

    printf("====== 🌴Pre Order: ======\n");
    PreOrder(tree);
    printf("\n");

    printf("====== In 🌴Order: ======\n");
    InOrder(tree);
    printf("\n");

    printf("====== Post order:🌴 ======\n");
    PostOrder(tree);
    printf("\n");

    printf("====== Level order:🌴 ======\n");
    LevelOrder(tree);

    printf("\n Tree test🌳.\n");

    return 0;
}
```

# function.cpp (contain the queue data structure)

```cpp
#include "function.h"

void InitQueue(LinkQueue &Q)
{
    Q.front = Q.rear = (LinkNode*)malloc(sizeof(LinkNode));
    Q.front->next = NULL;
}

// enter the queue
void EnQueue(LinkQueue &Q, ElemType ele)
{
    LinkNode *pnew = (LinkNode*)malloc(sizeof(LinkNode));
    pnew->data = ele;
    pnew->next = NULL;  // set the node next NULL
    Q.rear->next = pnew;    // queue tail insert the element. 从尾部放入元素
    Q.rear = pnew;  // rear 要指向新的尾部 rear need to point to the new tail node element.

}

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

# auxiliary code header function

## function.h

```cpp
#ifndef INC_1_TREE_FUNCTION_H
#define INC_1_TREE_FUNCTION_H

#include <stdio.h>
#include <stdlib.h>

typedef char BiElemType;
typedef struct BiTNode{
    BiElemType c;   //c是存储 data 数据 c using to store data
    struct BiTNode *lchild;
    struct BiTNode *rchild;
}BiTNode,*BiTree;

// tag structure used to axuaily queue.
typedef struct tag{
    BiTree p;   // tree address数的某一个结点的地址值
    struct tag *pnext;
}tag_t, *ptag_t;

// 队列的结构体 Aux Queue List structure
typedef BiTree ElemType;
typedef struct LinkNode{
    ElemType data;
    struct LinkNode *next;
}LinkNode;

typedef struct{
    LinkNode *front, *rear;
}LinkQueue; // 先进先出 First in First out FIFO

void InitQueue(LinkQueue &Q);
bool IsEmpty(LinkQueue Q);
void EnQueue(LinkQueue &Q, ElemType x);
bool DeQueue(LinkQueue &Q, ElemType &x);


#endif
```