#### 单链表中环的检测问题

##### 问题描述

> 给出一个结点个数为 $n$ 的单向链表，怎么判断这个链表中是否存在环呢？如果有环，环的起点位置怎么找？



##### 思路分析

​        单向链表中有环的情况，我们画出一个示意图
![有环的单链表][]

​        很容易理解，没有环的单链表是可以沿着链表头访问到链表尾的，`next` 为空的结点是链表的尾结点。而有环的单链表是只有链表头，没有链表尾的，所以 “遍历链表中所有结点” 这个操作是做不到的，遍历最终会一直在环里 ”兜圈子“。单链表中没有环的判断条件是沿着链表的方向最终可以找到一个 `next` 为空的结点，下面我们主要讨论链表中有环时应该如何判断。

顺着链表向前遍历，进入环之后，每走一圈都会回到环的起点，根据这样的特点，有了方法 $1$ 的解法：

> 1. ​        用一个数组来记录遍历链表时经过的所有结点，每到一个结点，就去数组中查找这个结点是不是跟数组中某个已经记录的结点重复，第一个出现重复的结点就是环的起点。
>
>     ​        这个方法需要记录链表中的所有结点，所以空间复杂度是 $O(n)$ 的；当遍历到链表中的第 $k$ 个结点时，要与数组中记录的 $k - 1$ 个结点比较，这样的比较会进行 $n$ 次，所以时间复杂度是 $O(n^2)$ 的。

*******************************************************************************

方法 $1$ 的时间复杂度比较高， $O(n^2)$ 一般是不能接受的。我们可以对这个方法做一个优化，查找操作一般都是可以通过哈希的方法优化为 $O(1)$ 时间复杂度的。这里，我们可以使用哈希表来替换方法 $1$ 中的数组，方法 $2$ 是这样的：

> 2. ​        用一个哈希表（比如`C++`中的[`unordered_set`][unordered_set]）来记录遍历链表时经过的所有结点，每到一个结点，就在哈希表中查找这个结点是不是跟表中某个已经记录的结点重复，第一个出现重复的结点就是环的起点。
>
>     ​        这个方法仍然需要记录链表中的所有结点，所以空间复杂度依然是 $O(n)$ 的；哈希表的查找操作平均的时间复杂度是 $O(1)$ 的，这样的查找会进行 $n$ 次，所以时间复杂度是 $O(n)$ 的。

方法 $2$ 已经是一个空间复杂度和时间复杂度都能接受的算法了。

*******************************************************************************

我们分析一下这个问题复杂度的理论极限：

时间复杂度的极限是 $O(n)$ ，因为单链表中是否存在环的问题，只能通过检测同一个结点是否被多次访问来判断，第一个出现重复的结点是 ”第 $n + 1$ 个“ 结点，链表的访问是步进式的，无法跳跃，所以至少需要连续访问 $n + 1$ 个结点，那么 时间复杂度至少是 $O(n)$ 的。

空间复杂度的极限是 $O(1)$，判断是否重复访问一个结点至少需要存储这个结点第一次被访问的记录，空间复杂度至少是 $O(1)$ 的。

*******************************************************************************

所以，最优的算法应该是时间复杂度为 $O(n)$ ，空间复杂度为 $O(1)$ 的算法。有一个叫做 “快慢指针法” 的方法可以在 $O(n)$ 时间复杂度和 $O(1)$ 空间复杂度下判断单链表中是否存在环，并且找到环的起点。快慢指针法利用了一个物理原理：在环上以不同速率匀速运动的物体一定会在环上某点相遇。快慢指针法是这样描述的：

> 3. ​        用两个指针 $f$ （快指针）和 $s$ （慢指针）分别从链表头出发， $f$ 每次前进 $2$ 个结点， $s$ 每次前进 $1$ 个结点， 即 `f = f_current_node->next->next, s = s_current_node->next`，如果链表中存在环，这两个指针一定会在环上某点相遇，即两个指针指向同一个结点。

首先，快慢指针法的空间复杂度是 $O(1)$ 的，只需要快慢指针这两个临时变量，时间复杂度跟两个指针什么时间相遇有关系，我们参考下面这张图做一下推理。

![快慢指针的行走轨迹][]

我们假设从链表头到环的起点的距离是 $L$ （即图中 $0 \Rightarrow 1 \Rightarrow 2 \Rightarrow 3$ 的路径长度， 为 $3$）， 环的长度是 $R$ （即图中 $3 \Rightarrow 4 \Rightarrow 5 \Rightarrow 6 \Rightarrow 3$ 的路径长度， 为 $4$）， 环的起点到相遇点的距离是 $M$ （即图中 $3 \Rightarrow 4$ 的路径长度， 为 $1$），$L \ge 0~,~ R \ge M \ge 0$ 。

因为快指针是慢指针速度的 $2$ 倍，我们假设慢指针前进了 $S$ 步，那么快指针前进了 $F = 2S$ 步。如果慢指针把链表中的结点走完一遍，即 $S = L + R$，那么快指针走了 $F = 2L + 2R$ ，所以慢指针在环上走了一圈，快指针在环上**至少**走了两圈。慢指针从第一次走到环的起点到走完环的一圈，快指针在这段时间里绕环走了两圈，这期间一定与慢指针相遇过一次。这个很容易理解，慢指针第一次走到环的起点时，快指针已经在环里了（因为 $L \ge 0$），假设这时快指针到慢指针的距离是 $D~,~~0 \le D \lt R$ （$D = 0$ 表示快慢指针刚好已经在环的起点上相遇了，说明 $L = kR, k \in N$），如果快慢指针没有相遇过，那么说明快指针一直跟在慢指针的后面，而慢指针只走了一圈，那么快指针最多走了一圈加 $D$ 的距离，是小于两圈的 （因为 $D \lt R$），与快指针走了两圈是矛盾的，所以两个指针一定会在慢指针走完整个链表的一遍之前相遇，而且之后慢指针每绕环走一圈都会在这个相遇点与快指针相遇一次。

根据上面的分析可以知道，在慢指针把链表中的结点访问一遍之前，就可以根据快慢指针是否相遇来判断链表中是否存在环。那么快慢指针法最多会访问 $3n$ 个结点，所以时间复杂度是 $O(n)$ 的。

我们目前只是用快慢指针法判断了链表中是否存在环，环的起点还没有找到（注意：相遇点不一定就是环的起点）。环的起点可以通过如下方法找到：

> 4. ​        假设链表的头结点为 `HEAD`，快慢指针的相遇点为 `MP`，用两个指针分别从 `HEAD` 和 `MP` 出发，两个指针每次都前进 $1$ 个结点，这两个指针最终会同时指向环上的某个结点，这个结点就是环的起点。

我们证明一下为什么这种方式可以找到环的起点。从上面的推理中我们知道 $F = 2S$，在第一次相遇时，慢指针走了 $S = L + M$ ，那么快指针走了 $F = 2S = 2L + 2M$，而且 $F$ 还可以表示为 $F = L + M + kR, k \in N^+$ ，即快指针走完慢指针走过的路之后又在环上绕了几圈，回到了相遇点。所以可以得到
$$
F = 2L + 2M = L + M + kR \\
\Downarrow \\
L + M = kR \\
\Downarrow \\
L = (k - 1)R + (R - M) \\
~\\
k \in N^+
$$

$R - M$ 是有实际意义的，表示的是慢指针在环上没走完的那部分（即图中 $4 \Rightarrow 5 \Rightarrow 6 \Rightarrow 3$ 的路径长度， 为 $3$）。那么如果用两个指针 $A$  和 $B$ 分别从 `HEAD` 和 `MP` 出发，两个指针每次都前进 $1$ 个结点，那么 $B$ 从 `MP` 出发，走 $R - M$ 步之后会到达环的起点，然后每绕环一圈都会回到环的起点，而 $A$ 从 `HEAD` 出发走 $L$ 步之后也会到达环的起点，根据 $(1)$ 中的公式 $L = (k - 1)R + (R - M)$ 可以知道，$A$ 和 $B$ 会在环的起点相遇。

*******************************************************************************

快慢指针法到这里就分析完了，相对于哈希表的方法，快慢指针法有空间复杂度上的优势。下面给出了哈希方法和快慢指针法的实现代码，还附加了测试的代码。测试可以发现当链表长度比较大时，哈希方法的运行时间比快慢指针法长很多（甚至会内存不足）。

[unordered_set]: https://zh.cppreference.com/w/cpp/container/unordered_set "C++ unordered_set"
[有环的单链表]: ../pictures/singly-linked-list-with-loop.PNG  "有环的单链表"
[快慢指针的行走轨迹]: ../pictures/numbered-trace.PNG "快慢指针的行走轨迹"

#### 参考代码

```c++
#include <iostream>
#include <unordered_set>
using namespace std;

// 单向链表结点的定义
class Node {
  public:
    // Data data; 我们省略了链表结构里数据字段的定义
    Node *next;
    explicit Node(Node *_next = nullptr): next(_next){}
    // more code
};

// 函数定义：链表中没有环返回nullptr，链表中有环则返回环的起点

Node *detect_loop_using_hash(Node *head) {
    if (head == nullptr) {
        return nullptr;
    }

    Node *current_node = head;
    unordered_set<Node *> set;

    while (current_node->next != nullptr) {
        if (set.find(current_node) != set.end()) { // 找到了环的起点
            return current_node;
        }
        set.insert(current_node);
        current_node = current_node->next;
    }

    return nullptr;
}

Node *detect_loop(Node *head) {
    if (head == nullptr) {
        return nullptr;
    }

    Node *slow = head, *fast = head;
    while (true) {
        if (fast->next == nullptr || fast->next->next == nullptr) { // 找到了链表尾，链表无环
            return nullptr;
        }

        slow = slow->next;
        fast = fast->next->next;

        if (slow == fast) { // 找到了相遇点
            break;
        }
    }

    // 找环的起点
    Node *meeting_point = slow;
    while (head != meeting_point) {
        head = head->next;
        meeting_point = meeting_point->next;
    }

    return meeting_point;
}

/****************************** test code ******************************/

// 根据链表长度和环的起点id创建一个单向链表
// list_length: unsigned int => 表示链表中结点的个数
// loop_start: [0, list_length - 1] => 创建的链表有环，环的起点是链表的第 loop_start + 1 个结点
// loop_start: [list_length, ..] => 创建的链表无环
Node *create_singly_linked_list(unsigned int list_length, unsigned int loop_start) {
    if (list_length == 0) {
        return nullptr;
    }

    Node *head = new Node();
    Node *current_node = head;
    Node *loop_start_node;

    for (unsigned int i = 0; i < list_length; i++) {
        Node *node = new Node();
        if (i == loop_start) {
            loop_start_node = node;
            cout << "loop start node address: " << loop_start_node << endl;
        }
        current_node->next = node;
        current_node = current_node->next;
    }

    if (loop_start < list_length) {
        current_node->next = loop_start_node; // 创建一个环
    } else {
        cout << "no loop" << endl;
    }

    return head->next;
}

int main() {
    unsigned int n, m;
    cin >> n >> m;
    Node *head = create_singly_linked_list(n, m);
    Node *loop_start_node;
    loop_start_node = detect_loop_using_hash(head);
    cout << "detect loop using hash:  " << loop_start_node << endl;
    loop_start_node = detect_loop(head);
    cout << "detect loop:             " << loop_start_node << endl;

    return 0;
}

/****************************** test code ******************************/
```
