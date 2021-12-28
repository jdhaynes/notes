# Stacks and Queues
Algorithms, Part I (Princeton, Coursera), Week 2

## Stacks
* Stacks are a LIFO (last in, first out) data structure.
* Two main operations - `push` (insert) and `pop` (remove).

### Linked-List Implementation
* Use a class for each item in the stack, with a pointer to the next object in the stack.


#### Java Code
```java

public class StackLinkedList {
    private Node first = null;
    private class Node {
        String item;
        Node next;
    }

    public boolean isEmpty() {
        return first == null;
    }
    public void push(String item) {
        Node oldfirst = first;
        first = new Node();
        first.item = item;
        first.next = oldfirst;
    }
    public String pop() {
        String item = first.item;
        first = first.next;
        return item;
    }
}
```

#### Analysis
| Operation | Best-Case Time | Worst-Case Time |
|-|-|-|
| `push` | $O(1)$ |$O(1)$ |
| `pop` | $O(1)$ | $O(1)$ |

* The constant factors in the $O(1)$ terms may be high due to dynamic memory allocations for each new object.
* Memory allocations are expensive.

### Static Array Implementation
* Uses an array `s[]` to store a $N$ items on the stack.
* `push` adds new item at `s[N]`
* `pop` removes item at `s[N-1]`
* Requires client to specify size of array - not ideal.
* Stack overflow if size exceeds array allocation.

#### Java Code
```java
public class StackStaticArray {
    private String[] s;
    private int N = 0;

    public StackStaticArray(int capacity) {
        s = new String[capacity];
    }

    public boolean isEmpty() {
        return N == 0;
    }

    public void push(String item) {
        s[N++] = item;
    }

    public String pop() {
        String item = s[--N];
        s[N] = null;
        return item;
    }
}
```

#### Analysis
| Operation | Best-Case Time | Worst-Case Time |
|-|-|-|
| `push` | $O(1)$ |$O(1)$ |
| `pop` | $O(1)$ | $O(1)$ |

Good performance, but not ideal for the client due to the constraints of a static size array.

### Dynamic Array Implementation
* Use an array which automatically resizes as the number of objects increases or decreases.
* Avoids overhead of dynamic memory allocation for linked lists.
* Whenever the array is resized, create a new array and copy the existing items other.

Chosing how often the array resizes impacts the performance of operations. For example, increasing/decreasing the size of the array by 1 on every `push` or `pop` is too expensive as it takes time proportional to $N^2$ for $N$ inserts, as $1+2+\cdots+N\sim N^2$ (finite arithmatic series - see mathematics notes).

A better solution is to resize less frequently by creating a new array twice the size when the array is full.

#### Increasing Array Size
If array is full, create a new array twice the size.

##### Java Code
```java
 public ResizingArrayStackOfStrings() {
    s = new String[1];
 }
 public void push(String item) {
    if (N == s.length) resize(2 * s.length);
    s[N++] = item;
 }
 private void resize(int capacity) {
    String[] copy = new String[capacity];
    for (int i = 0; i < N; i++)
        copy[i] = s[i];
    s = copy;
 }
```

##### Analysis
| Operation | Best-Case Time | Worst-Case Time |
|-|-|-|-|
| `push` | $O(1)$ | $O(N)$ | $N$ |

This algorithm is a good use of amortized cost to calculate the time for $N$ `push` operations. The worst-case time for a single `push` operation is $O(N)$, but only when resizing. All other operations run at $O(N)$. Therefore, the amortized number of array access for $N$ `push` operation is approximately $3N$, which is $O(N)$. As there are $N$ operations, the amortized cost for each operation is $O(1)$.

$$
\begin{aligned}
&=N+(2+4+8+\cdots+ N)\\
&=N+(2N-2)\\
&\sim 3N
\end{aligned}
$$


See mathmetics notes for derivation of formula for finite artithmetic series for powers of 2.


**Amortized analysis:** analysing the average cost of over a series of worst-case operations rather than the worst-case time for a single operation. Some operations may be much more costly than others, so averaging out the overall cost over a series offers a more practical view of the cost.

#### Decreasing Array Size
The `pop` operation is responsible for shrinking the array. 

The threshold at which this happens impacts performance. If `pop` halves the size of the array when full, and `push` doubles the size of the array when full, then the worst-case is push-pop-push-pop. Each operation resizes the array, so each operation takes time proportional to $N$. 

A better solution is `pop` to half the size of the array when it is one-quarter full.

##### Java Code
```java
 public String pop() {
    String item = s[--N];
    s[N] = null;
    if (N > 0 && N == s.length / 4) resize(s.length / 2);
    return item;
 }
```

##### Analysis
From an empty stack, any number of $m$ `push` or `pop` operations takes time proportional to $m$, so each operation is $O(1)$.

#### Summary of Dynamic Array Implementation Performance
| Operation | Best-Case | Worst-Case | Amortized |
|-|-|-|-|
| `initialize` | 1 | 1 | 1 |
| `push` | 1 | $N$ | 1 |
| `pop` | 1 | $N$ | 1 |
| `size` | 1 | 1 | 1 |

### Memory Usage of Linked List vs Dynamic Array Implementations
Linked list implementation: $\sim 40N$ bytes.
* Object overhead: 16 bytes
* Inner class (Node) class overhead: 8 bytes
* Reference to item (String): 8 bytes. 
* Reference to next (Node): 8 bytes

Dynamic array implementation: between $\sim 8N$ (when quarter-full) and $\sim 32N$ (when full) bytes.
* Reference to array: 8 bytes
* Array overhead: 24 bytes
* Array item overhead: 8 bytes * size. 
* Array size counter (int): 4 bytes
* Padding: 4 bytes

Note: does not include memory for values of items in the stack.

### Applications of Stacks
* Parsing in compilers
* Undo in word processor
* Java virtual machine (JVM)

#### Example: Function Calls in Compiler
* Function calls: `push` local environment and return address.
* Function returns: `pop` local environment and return address.

#### Example: Arithmetic Expression Evaluation (Dijkstra's Two-Stack)
* Evaluates inflix expressions, e.g. $(1+((2+3)*(4*5)))$
* Uses two queues - one for values, one for operations.
* Expression must be fully parenthesised. Doesn't take into account operator presedence, so three item cannot be within the same parenthesis.

The algorithm goes through each item of the expression (left-right) and performs an operation depending on what it is.
* Value - push on to the value stack.
* Operator - push on to the operator stack
* Left parentheses - ignore.
* Right parentheses - pop the top two values, and the operator. Apply operator to those two values, and push the resulting single value on to the value stack.

##### Java Code
```java
public class DijkstrasTwoStackAlgorithm {
    public static void main(String[] args) {
        Stack <String> ops = new Stack <String> ();
        Stack <Double> vals = new Stack <Double > ();
        while (!StdIn.isEmpty()) {
            String s = StdIn.readString();
            if (s.equals("("));
            else if (s.equals("+")) ops.push(s);
            else if (s.equals("*")) ops.push(s);
            else if (s.equals(")")) {
                String op = ops.pop();
                if (op.equals("+")) vals.push(vals.pop() + vals.pop());
                else if (op.equals("*")) vals.push(vals.pop() * vals.pop());
            } else vals.push(Double.parseDouble(s));
        }
        StdOut.println(vals.pop());
    }
}
```

## Queues
* First in, first out (FIFO) data structure
* Can also use either linked list or resizing array implementations

### Linked List Implementation
#### Java Code
```java
public class QueueLinkedList {
    private Node first, last;

    private class Node {
        String item;
        Node next;
    }

    public boolean isEmpty() {
        return first == null;
    }

    public void enqueue(String item) {
        Node oldlast = last;
        last = new Node();
        last.item = item;
        last.next = null;
        if (isEmpty()) first = last;
        else oldlast.next = last;
    }

    public String dequeue() {
        String item = first.item;
        first = first.next;
        if (isEmpty()) last = null;
        return item;
    }
}
```
### Array Implementation
* Resizing arrays can also be used with similar principles to stacks

## Linked List vs Array Implementations for Stacks and Queues
Linked lists guarantee $O(1)$ constant time `push` and `pop` operations, but come with extra overhead from dynamic memory allocation of creating and linking objects.

Arrays worst guarantee $O(N)$ time when peforming a resize, but $O(1)$ when not. The amortized cost is $O(1)$, but not all operations will perform at constant time.

For applications that require guaranteed operation time, linked lists will be a better choice. 


