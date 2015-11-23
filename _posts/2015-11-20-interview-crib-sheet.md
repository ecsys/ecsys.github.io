---
layout: post
title: Interview Crib Sheet
permalink: /interview-crib-sheet.html
category: Tech
tag: interview, crib sheet, cheat sheet, sorting, algorithm, data structure
---

Last modified: Sept 2015

## Sorting Algorithms

| Algorithm 	|     Average Case |     Best Case |     Worst Case |
|---------------|------------------|---------------|----------------|
| Bubble Sort | n^2 | n | n^2 |
| Insertion Sort | n^2 | n | n^2 |
| Selection Sort | n^2 | n^2 | n^2 |
| Merge Sort | n\*logn | n\*logn | n\*logn |
| Quick Sort | n\*logn | n\*logn | n^2 |

Implementation of MergeSort - Java

```java
public static LinkedList<Integer> mergeSort(LinkedList<Integer> list){
	if (list.size() <= 1) return list;

	LinkedList<Integer> left = new LinkedList<Integer>();
	LinkedList<Integer> right = new LinkedList<Integer>();

	int middle = list.size() / 2;
	for (int i = 0; i < middle; i++){
		left.add(list.get(i));
	}
	for (int i = middle; i < list.size(); i++){
		right.add(list.get(i));
	}

	left = mergeSort(left);
	right = mergeSort(right);

	return merge(left, right);
}

public static LinkedList<Integer> merge(LinkedList<Integer> left, LinkedList<Integer> right){
	LinkedList<Integer> result = new LinkedList<Integer>();
	while (!left.isEmpty() && !right.isEmpty()){
		if (left.getFirst() <= right.getFirst()){
			result.add(left.getFirst());
			left.removeFirst();
		} else {
			result.add(right.getFirst());
			right.removeFirst();
		}
	}
	while (!left.isEmpty()){
		result.add(left.getFirst());
		left.removeFirst();
	}
	while (!right.isEmpty()){
		result.add(right.getFirst());
		right.removeFirst();
	}
	return result;
}
```

Implementation of QuickSort - Java

``` java
public static LinkedList<Integer> QuickSort(LinkedList<Integer> A, int low, int hi){
	if (hi > low) {
		int pivotLoc = partition(A, low, hi);
		QuickSort(A, low, pivotLoc - 1);
		QuickSort(A, pivotLoc + 1, hi);
	}
	return A;
}

public static int partition(LinkedList<Integer> A, int low, int hi){
	int pivot = A.get(hi);
	int cur = low;
	int temp = 0;
	for (int i = low; i < hi; i++){
		if (A.get(i) < pivot) {
			temp = A.get(cur);
			A.set(cur, A.get(i));
			A.set(i, temp);
			cur++;
		}
	}
	temp = A.get(cur);
	A.set(cur, pivot);
	A.set(hi, temp);
	return cur;
}
```
*****

## Tree Structure

### Binary Search Tree

TODO:
Definition and basic operations, e.g. insertion, deletion, searching...

**Red-Black Tree**(Self-balancing binary search tree)

|      |Average Case|Worst Case|
|------|------------|----------|
|Space |O(n)        |O(n)      |
|Search|O(log n)    |O(log n)  |
|Insert|O(log n)    |O(log n)  |
|Delete|O(log n)    |O(log n)  |

- Property
	1. Leaves have to be NIL
	2. Root and leaves are all black
	3. Every red node must have to black nodes
	4. Every path from a given node to any of its descendant leaves must have the same number of black nodes

Note: Property 3 and 4 make sure that max_height<2\*min_height, thus roughly balanced

### Tree Traversal

#### **BFS**

```java
public static void BFS(Node r){
	Queue<Node> temp = new LinkedList<Node>();
	Node cur;
	if (r != null) temp.add(r);
	while (!temp.isEmpty()){
		cur = (Node)temp.poll();
		if(cur.left!=null) temp.add(cur.left);
		if(cur.right!=null) temp.add(cur.right);
		System.out.println(cur.value + " ");
	}
}
```

#### **DFS**

Pre-order

```
preorder(node)
	if node == null then return
	visit(node)
	preorder(node.left) 
	preorder(node.right)
```

![preorder](http://upload.wikimedia.org/wikipedia/commons/thumb/d/d4/Sorted_binary_tree_preorder.svg/220px-Sorted_binary_tree_preorder.svg.png)

Visit order: F-B-A-D-C-E-G-I-H

In-order

```
inorder(node)
	if node == null then return
	inorder(node.left)
	visit(node)
	inorder(node.right)
```

![inorder](http://upload.wikimedia.org/wikipedia/commons/thumb/7/77/Sorted_binary_tree_inorder.svg/220px-Sorted_binary_tree_inorder.svg.png)

Visit order: A-B-C-D-E-F-G-H-I

Post-order

```
postorder(node)
	if node == null then return
	postorder(node.left)
	postorder(node.right)
	visit(node)
```

![postorder](http://upload.wikimedia.org/wikipedia/commons/thumb/9/9d/Sorted_binary_tree_postorder.svg/220px-Sorted_binary_tree_postorder.svg.png)

Visit order: A-C-E-D-B-H-I-G-F

Code example of pre-order:

```java
public static void DFS(Node r){
	r.visited = true;
	System.out.print(r.value + " ");
	if (r.left != null) DFS(r.left);
	if (r.right != null) DFS(r.right);
}
```

### Other Tree Algorithms

**Binary Tree Reverse**

- Solution 1 (without recursion)

```
Stact reverse = new Stack()
reverse.push(root)
while(!stack.isEmpty()){
	TreeNode cur = stack.pop()
	switch the left and right child of cur
	if(cur.left!=null)
		reverse.push(cur.left)
	if(cur.right!=null)
		reverse.push(cur.right)
}
```

- Solution 2 (Python)

```python
reverse(self, root)
	# when the reverse method is first called, the default value of root is this Node
	if not root:
		root = TreeNode(self.value)
	if self.right:
		newLeft = root.addLeft(self.right.value)
		self.right.reverse(newLeft)
	if self.left:
		newRight = root.addRight(self.left.value)
		self.left.reverse(newRight)
	return root
```

**Minimum Spanning Tree(MST)**

- Given an undirected weighted graph G = (V,E)
- Want to find a subset of E with the minimum total weight that connects all the nodes into a tree
- **Kruskal's Algorithm** (easy to code with O(e\*log(e)))
	Idea

	- The edge e* with the smallest weight has to be in the MST!
	- After an edge is chosen, the two nodes at the ends can be merged into one set

	Pseudo code

```
making each node a set
sort the edges in increasing order of weight
repeat until there is only one set left
	take minimum weight edge e*
	if e* connects two sets
		connect them and merge the sets
		add e* to result
	otherwise, ignore e*
```

- **Prim's Algorithm** (O(|E|+|V|log|V|) with Fibonacci heap and adjacency list)
	Idea

	- maintain a set S that starts out with a single node s
	- find the smallest weighted edge e* = (u, v) that connects u ∈ S and v !∈ S
	- add e* to the MST, add v to S
	- repeat until S = V

	Pseudo code

```
initialize S to {s}, D_v to cost(s, v) for every v
repeat until S = V
	find v !∈ S with smallest D_v (use a priority queue)
	add v to S, add D_v to the total weight of the MST
	for each edge(v ,w)
		update D_w to min(D_w, cost(v, w))
```

*****

## Graphs

### Definitions

1. Connected graph: there is a path between every pair of nodes (include 0 or 1 node)
2. Bipartite graph: nodes can be separated into two groups S and T such that edges exist between S and T only
3. Directed acyclic graph: a directed graph with no directed cycles

### Representation of Graphs

<table>
    <tr>
        <th></th>
        <th>Idea</th>
        <th>Pros</th>
        <th>Cons</th>
    </tr>
    <tr>
		<td>Adjacent Matrix</td>
        <td>Store information is a |V|x|V| 2D matrix.<br>
		    If there is an edge, put 1; otherwise, put 0.</td>
        <td>
			<li>Easy implementation</li>
			<li>Delete an edge: O(1)</li>
			<li>Query an edge: O(1)</li>
		</td>
        <td>
			<li>Insert an vertex: O(v^2)</li>
			<li>Memory space: O(v^2)</li>
		</td>
    </tr>
    <tr>
		<td>Adjacency list</td>
        <td>Have an array of LinkedLists.<br>
			An entry in array[i] means that node is adjacent to node i.<br>
			<img src="http://faculty.cs.niu.edu/~freedman/340/340notes/gifImages/340graph6.gif" alt="Adjacency list" style="width:200px;height:150px;">
		</td>
        <td>
			<li>space(V+E)</li>
			<li>easy to add a vertex</li>
		</td>
        <td>
			<li>query an edge costs O(V)</li>
			<li>harder implementation</li>
		</td>
    </tr>
    <tr>
		<td>Object and pointers</td>
        <td>
			Similar to adjacency list but information of edges(pointers) is stored in the objects of nodes.
		</td>
        <td></td>
        <td></td>
    </tr>

</table>

### Graph Algorithms 

**Topological sort**

- Define problem
	- input: a DAG, G = (V, E)
	- output: an ordering of nodes such that for each edge u->v, u comes before v
		- the topological ordering is not unique
- O(n^2 + m) solution
	- any node without an incoming edge can be the first element
	- after having the first node, removing outgoing edges from it, repeat
- O(n + m) solution
	- per-compute the number of incoming edges deg(v) for each node v
	- put all nodes with 0 deg() into a queue Q
	- repeat until Q becomes empty
		- take v from Q
		- for each edge v->u
			- decrement deg(u)
			- if deg(u)==0, push to Q

**Eulerian Circuit**

- Define problem
	- Given an undirected graph G, we want to find a sequence of nodes that visits every edge exactly once and comes back to the starting point
	- Note: G has to be connected and each vertex has to have an even degree

- Solving idea
	- pick any node G and walk randomly
	- when you come back to a visited node or get stuck, you must have a cycle
		- remove edges in the cycle and process in each connected component
		- glue the cycles together to finish
	

**Shortest path algorithm**

- **Dijkstra** (O(E+V logV))
	
	- Idea

		- Assign distance of 0 to the initial node and infinity to other nodes
		- Mark initial node as current node and other nodes as unvisited nodes
		- For Node.cur consider all unvisited neighbours
			- calculate tentative distance
			- ensure for all its neighbours has the smallest tentative distance
		- Mark the current node visited
		- If the destination is visited, stop
		- Mark the node with smallest tentative distance as cur and loop
	
	- Pseduo code

```
function Dijkstra(Graph, source):
	dist[source] ← 0						// Initialization
	for each vertex v in Graph:           
		if v ≠ source
			dist[v] ← infinity            	// Unknown distance from source to v
            prev[v] ← undefined           	// Predecessor of v
		end if
		Q.add_with_priority(v, dist[v])
	end for 

	while Q is not empty:                 	// The main loop
		u ← Q.extract_min()					// Remove and return best vertex
		for each neighbor v of u:
			alt = dist[u] + length(u, v) 
			if alt < dist[v]
				dist[v] ← alt
				prev[v] ← u
				Q.decrease_priority(v, alt)
			end if
		end for
	end while
	return prev[]
```
	
- **A* Search** O(b^d) for both time and space (b:breadth d:depth)

	- Idea

		- Let g(x) represent past cost
		- h(x) represents heuristic estimate
		- f(x) = g(x) + h(x)
		- Optional addition constraint: h(x) <= d(x,y) + h(y)
		- Starting with the initial node
		- Put starting nodes into a priority queue
		- The lower the f(x), the higher the priority, remove the lowest f(x) from the queue
		- Update f(x), g(x) and h(x) of the neighbours of the popped node, push updated info to the queue
		- Loop the algorithm until we pop the goal node from the queue

	- Pseduo code

```
initialize the open list
initialize the closed list
put the starting node on the open list (you can leave its f at zero)

while the open list is not empty
    find the node with the least f on the open list, call it "q"
    pop q off the open list
    generate q's neighbours and set their parents to q
    for each neighbour
        if neighbour is the goal, stop the search
        neighbour.g = q.g + distance between neighbour and q
        neighbour.h = distance from goal to neighbour
        neighbour.f = neighbour.g + neighbour.h

        if a node with the same position as neighbour is in the OPEN list \
            which has a lower f than neighbour, skip this neighbour
        if a node with the same position as neighbour is in the CLOSED list \ 
            which has a lower f than neighbour, skip this neighbour
        otherwise, add the node to the open list
    end
    push q on the closed list
end
```

*****

## Dynamic Programming

Method for solving complex problems by breaking them down into simpler sub-problems.

**1-Dimensional DP example**

- Define problem
	- Given n, find the number of different ways to write n as the sum of 1, 3, 4
	- For n = 5, the answer is 6
		- 5 = 1 + 1 + 1 + 1 + 1  
 			= 1 + 1 + 3  
 			= 1 + 3 + 1  
 			= 3 + 1 + 1  
 			= 1 + 4  
			= 4 + 1  
- Solution
	- Let $D_n$ be the number of ways to write n as the sum of 1, 3, 4
	- Suppose one possible solution, n = $x_1 + x_2 + x_3 +...+ x_m$($x$ is one of 1, 3, 4)
		- if $x_m=1$, the rest of the terms must sum to n-1
		- thus, the number of sums that end with $x_m=1$ is equal to $D_{n-1}$ (also think about $x_m=3, 4$)
		- then we have $D_n = D_{n-1} + D_{n-3} + D_{n-4}$
		
```
set D[0] = D[1] = D[2] = 1, D[n<0] = 0, D[3] = 2
for(i = 4; i<=n; i++)
	D[i] = D[i-1] + D[i-3] + D[i-4];
```

**2-Dimensional DP example**

- Define problem
	- given two strings x and y, find the longest common sub-sequence(LCS) and print its length
	- x = ABCBDAB  
	  y = BDCABC  
	  answer : BCAB, (length is 4)
	- Note: unlike substrings, subsequences are not required to occupy consecutive positions within the original sequences
- Solution
	- let $D_{ij}$ be the length of the LCS of $x_1 ... x_i$ and $y_1 ... y_j$
	- if $x_i = y_j$, they both contribute to the LCS
		- $D_{ij} = D{i-1,j-1} + 1$
	- otherwise, either $x_i$ or $y_j$ does not contribute to the LCS, so one can be dropped
		- $D_{ij} = max(D_{i-1,j},D_{i,j-1})$
	- find and solve the base cases: $D_{i0} = D_{0j} = 0$

```java
for(i = 0; i <= n; i++) D[i][0] = 0;
for(j = 0; j <= m; j++) D[0][j] = 0; 
for(i = 1; i <= n; i++) { 
	for(j = 1; j <= m; j++) { 
		if(x[i] == y[j]) 
			D[i][j] = D[i-1][j-1] + 1; 
		else 
			D[i][j] = max(D[i-1][j], D[i][j-1]); 
	}
}
```



*****

## Operating System

### Processes and Threads

- Both processes and threads have life cycles
	- create->ready->running->wait->suspend->close
- Process
	- A process has a self-contained execution environment, e.g. memory space.
	- A process has at least one threads
	- A process can have multiple threads
		- Threads within a process share the same address space (code segment, data segment, open files, etc..)
- Thread
	- Easier to create than process and more efficient I/O
	- Having its own allocated registers and separate stack for procedure calls
	- Most common usage for threads: large server applications
	- Deadlock
		- T1 accesses R1  
		  T2 accesses R2  
		  T1 requires R2
		  T2 requires R1
		- Solutions
			- Make resources can be accessed by only one threads at a time
			- Set a maximum waiting time

*****

## Database

### SQL join

- Inner join
	- SELECT * FROM A INNER JOIN B ON AA = BB
	- Inner join only returns matched result, NULL tuples will be discarded
	- A inner join B = B inner join A
- Outer join
	- Left outer join
		- SELECT * FROM A LEFT OUTER JOIN B ON AA = BB
			- return all AA elements in table A, it will display NULL in BB column if it have a match
		- SELECT * FROM A LEFT OUTER JOIN B ON AA = BB WHERE BB is NULL
			- return elements in AA and not in BB
	- Right outer join
		- similar to Left outer join but having all elements in the right table
	- Full outer join
		- SELECT * FROM A FULL JOIN B ON AA = BB
		- having all elements in AA and BB
- Cross join
	- SELECT * FROM A CROSS JOIN B
	- for each element in A, match every tuple in B with it
	- this is a Cartesian product, the size of cross join is N\*M

![SQLJOIN](http://cdn.powerxing.com/imgs/sql-join.png)


*****

## Network

### TCP/IP

- Comparison of TCP and UDP
	- TCP is connection oriented protocol (UDP is connectionless)
	- TCP is ordered, packages arrives in the sending order (UDP has no order)
	- TCP can read multiple packages per read call (UDP can read only one per read call)

- TCP connection establishment
	1. SYN: Client sends a SYN to the server, setting the segment's sequence number to a random value A.
	2. SYN-ACK: Server replies with a SYN-ACK. The acknowledgment number is set to be A+1, and the server chooses another random number, B, as the sequence number.
	3. ACK: Client sends an ACK back to the server. The sequence number is set to A+1, and the acknowledgement number is set to B+1.

- HTTP
	- HTTP Request
		- Request line 
			- Request Method	
				- GET method stores information in the URL, usually used when requesting information from server
				- POST method stores message in the request body, usually used when sending message to sever apps
			- Request-URL
			- HTTP-Version
		- Header
			- Connection: Keep-Alive - This keeps the TCP connection between client and server
		- Body

*****

## Java

### Java Standard Library

**Hashtable vs HashMap vs HashSet**

- Hashtable
	- Hashtable<Integer, String> cityTable = new Hashtable<Integer, String>();
	- Do not allow null for neither key nor value
	- Synchronized. Only one thread can access at one time.
- HashMap
	- HashMap<Integer, String> productMap = new HashMap<Integer,String>();
	- Implement MAP interface
	- Allows null for both key and value
	- keySet() returns a set of keys; values() returns a collection of values
	- Unsynchronized
- HashSet
	- HashSet<String> stateSet = new HashSet<String>();
	- Implement SET interface
	- Do not allow duplicate values
	- Provide add() method other than put() method
	- Provide contains() method to check whether an object is already contained
	- Should be used when needs to maintain a unique list
	- Unsynchronized

**ArrayList vs LinkedList vs Vector**

- ArrayList
	- Implemented with dynamically resizing array
	- Fast at random accessing
	- 1.5 times of the size when expanding
- LinkedList
	- Implemented with double linked list
	- Have space overhead for storing pointers
	- Fast at inserting new object at random place
- Vector
	- Almost the same as ArrayList but synshronized
	- Rarely used in practice

https://blog.udemy.com/java-interview-questions/

*****

## Python

*****

## Other Classical Coding Examples

### Eight Queen Puzzle Using Backtracking

```java
public static int findNextPos(int[] result, int row, boolean backTracking) {
	int start_col = 0;
	boolean ok = true;
	if (backTracking) {
		start_col = result[row] + 1;
	}
	for (int col = start_col; col < result.length; col++) {
		ok = true;
		for (int r = 0; r < row; r++) {
			if (result[r] == col) {
				ok = false;
				break;
			}
			if (result[r] - col == r - row || result[r] - col == row - r){
				ok = false;
				break;
			}
		}
		if (ok)
			return col;
	}
	return -1;
}

public static int[] eightQueen(int size) {
	int[] result = new int[size];
	for (int i = 0; i < result.length; i++) {
		result[i] = 0;
	}
	int temp = 0;
	for (int i = 0; i < size; i++){
		temp = findNextPos(result, i, false);
		if (temp > -1) {
			result[i] = temp;
			if (i == size - 1) return result;
		} else {
			while (true) {
				i--;
				if (i < 0) return null;
				temp = findNextPos(result, i, true);
				if (temp > -1) {
					result[i] = temp;
					break;
				}
			}
		}
	}
	return null;
}	
```
