# Minimum Cost Spanning Trees

- **Example**: Design a communication network so as to connect $n$ cities at ==**minimum total cost**==
- **Model**:
  
    - Graph $G=(N,E)$ with $n=|N|$ , $m=|E|$ , and a cost $c_e \in R$ for each $e = \{u,v\} \in E$
      
        <img src="assets/image.png" alt="image" style="zoom:10%; margin-left: 0" />
    
- **Required Properties**:
    - Each pair of cities must communicate ⇒ ==**connected subgraph**== containing ==**all the nodes**==
    - Minimum total cost ⇒ subgraph ==**with no cycles**==
- **Problem**:
    - Given an undirected $G=(N,E)$ and a cost $c_e \in \mathbb{R}$ for each $e=\{u, v\} \in E$ , find a spanning tree $G_T = (N, T)$ of minimum total cost
      
        $$\displaystyle \min_{T \in X} \sum_{e \in T} c_e, \text{ where } X \text{ is the set of all spanning trees of } G$$
        
        <img src="assets/image 1.png" alt="image" style="zoom:15%; margin-left: 0" />

  

> **Theorem**: A complete graph with $n$ nodes ( $n \geq 1$ ) has $n^{n-2}$ spanning trees



## Prim’s Algorithm

- **Idea**: Iteratively build a spanning tree
- **Method:**
    - Start from initial tree $(S,T)$ with $S = \{u\}$ and $T = \empty$ ( $u$ can be any node in $N$ )
    - At each step, add to the current partial tree $(S, T)$ an edge of minimum cost among those which connect a node in $S$ to a node in $N/\ S$
- **Example**:
  
    <img src="assets/image 2.png" alt="image" style="zoom:10%; margin-left: 0" />
    
    <img src="assets/image 3.png" alt="image" style="zoom: 33%; margin-left: 0" />
    
- **Complexity**:
  
    <img src="assets/image 4.png" alt="image" style="zoom:30%; margin-left: 0" />

  

### Exactness of Prim’s Algorithm

- **Definition**: An algorithm is ==**exact**== if it provides an optimal solution for every instance, otherwise it is ==**heuristic**==



> **Proposition**: Prim’s algorithm is exact

  

> **Property**: Let $F$ be a partial tree (spanning nodes in $S \subseteq N$) contained in an optimal tree of $G$. Consider $e = \{u,v\} \in \delta(S)$ of minimum cost, then there exists a minimum cost spanning tree of $G$ containing $e$

<img src="assets/image 5.png" alt="image" style="zoom:11%; margin-left: 0" />

  

### Greedy of Prim’s Algorithm

- **Definition**: A ==**greedy algorithm**== constructs a feasible solution iteratively by making at each step a “locally optimal” choice, without reconsidering previous choices
- **Notes**:
    - Prim’s algorithm is greedy
        - At each step a minimum cost edge is selected among those in the cut $\delta(S)$ induced by the current set of nodes $S$
    - For most discrete optimization problems greedy-type algorithms yield a feasible solution with no guarantee of optimality
- **Implementation in** $O(n^2)$:
    - **Data Structure**:
        - $k$ : number of edges selected so far
        - Subset $S \subseteq N$ of nodes incident to the selected edges
        - Subset $T \subseteq E$ of selected edges
        - $C_j = \begin{cases} \min{c_{ij} : i \in S}, & j \in S \\ +\infin , & \text{otherwise} \end{cases}$
        - $closest_j = \begin{cases} \arg\min\{c_{ij} : i \in S \}, & j \notin S \\ \text{"predecessor" of } j \text{ in the minimum spanning tree}, & j \in S \end{cases}$
          
            <img src="assets/image 6.png" alt="image" style="zoom:15%; margin-left: 0" />
        
    - **Example**:
      
        <img src="assets/image 7.png" alt="image" style="zoom: 33%; margin-left: 0" />
        
    - **Complexity**:
      
        <img src="assets/image 8.png" alt="image" style="zoom:30%; margin-left: 0" />
        
        <img src="assets/image 9.png" alt="image" style="zoom:25%; margin-left: 0" />

  

## Optimality Condition

- **Definition**: Given a spanning tree $T$, an edge $e \notin T$ is cost decreasing if when $e$ is added to $T$ it creates a cycle $C$ with $C \subseteq T \cup \{e\}$ and $\exists f \in C \backslash \{e\}$ such that $c_e<c_f$
- **Example**:
  
    <img src="assets/image 10.png" alt="image" style="zoom:20%; margin-left: 0" />

  

> **Theorem**: A tree $T$ is of minimum total cost if and only if no cost-decreasing edge exists

  

- **Optimality test**: The optimality condition allows to ==**verify**== whether a ==**spanning tree**== $T$ is ==**optimal**==: just check that each $e \in E \backslash T$ is not a cost-decreasing edge

  

# Shortest Path Problem

Given a directed graph $G=(N, A)$ with a cost $c_{ij} \in \mathbb{R}$ for each arc $(i,j) \in A$ , and two nodes $s$ and $t$ , determine a ==**minimum cost**== (shortest) ==**path from**== ==$s$== ==**to**== ==$t$==

- $c_{ij}$ represents the cost (length, travel time, …) of arc $(i,j) \in A$
- $s$ is the ==**origin**==, $t$ is the ==**destination**==
  
    <img src="assets/image 11.png" alt="image" style="zoom:11%; margin-left: 0" />

  

## Dijkstra’s Algorithm

- **Definition**: A path $\langle (i_1, i_2), (i_2, i_3),..., (i_{k-1}, i_k) \rangle$ is ==**simple**== if no node is visited more than once

  

> **Property**: If $c_{ij} \geq 0$ for all $(i,j) \in A$ , there is at least one shortest path which is simple

  

- **Assumption**: ==$c_{ij} \geq 0$== , for every $(i,j) \in A$
    - ==**Input**==: $G=(N,A)$ with non-negative arc costs, $s \in N$
    - ==**Output**==: Shortest paths from $s$ to all other nodes of $G$
- **Idea**: Consider the nodes in increasing order of length (cost) of the shortest path from $s$ to any one of the other nodes
    - “Greedy” w.r.t. paths from $s$ to $j$
    - To each node $j \in N$ , we assign a ==**label**== $L_j$ which corresponds, at the end of the algorithm, to the ==**cost**== of a ==**minimum cost path**== from $s$ to $j$
- **Example**:
  
    <img src="assets/image 12.png" alt="image" style="zoom:15%; margin-left: 0" />
    
    - Two labels are associated with each node $j \in N$ , $(L_j, pred_j)$ , where at the end:
      
        - $L_j$ = cost of a shortest path from $s$ to $j$
        - $pred_j$ = “predecessor” of $j$ in a shortest path from $s$ to $j$
        
        <img src="assets/image 13.png" alt="image" style="zoom:30%; margin-left: 0" />
        
    - A set of shortest paths from $s$ to every node $j \neq s$ can be retrieved backwards: $pred_j, \ pred_{pred_j},..., s$
      
        <img src="assets/image 14.png" alt="image" style="zoom:20%; margin-left: 0" />
    
- **Data Structure**:
    - $S \subseteq N$ : subset of nodes whose labels are permanent
    - $\bar{S} = (N \backslash S) \subseteq N$ : subset of nodes with temporary labels
    - $L_j=\begin{cases} \text{cost of a shortest path from } s \text{ to } j, & j \in S \\ \min\{L_i + c_{ij} : (i,j) \in \delta^+(S), & j \notin S\} \end{cases}$
      
        (upper bound on the cost of a shortest path)
        
    - $pred_j = \begin{cases} \text{predecessor of } j \text{ in the shortest path from } s \text{ to } j, & j \in S \\ u \text{ such that } L_u + c_{uj} = \min\{ L_i + c_{ij} : i \in S\}, & j \notin S \end{cases}$
- **Complexity**:
    - ==**Input**==: Graph $G=(N,E)$ with non-negative arc costs, $s \in N$
    - ==**Output**==: Shortest paths from $s$ to all other nodes of $G$
      
        <img src="assets/image 15.png" alt="image" style="zoom:25%; margin-left: 0" />
        
        If all labels $L_u$ are determined by appropriate updates the overall complexity is $O(n^2)$
        

  

> **Proposition**: Dijkstra’s algorithm is exact

  

- **Remark**:
    - A set of shortest paths from $s$ to all the nodes $j$ can be retrieved via the vector of predecessors
    - The union of a set of shortest paths from node $s$ to all the other nodes is an ==**arborescence rooted at**== ==$s$==
        - Such arborescences are not minimum cost spanning trees!
          
            <img src="assets/image 16.png" alt="assets" style="zoom:10%; margin-left: 0" />
        
    - Dijkstra’s algorithm does ==**not work with negative cost arcs**==

  

## Shortest Path Problem with Negative Costs

- **Observation**: If a graph $G$ contains a circuit of negative cost, the shortest path problem may not be well-defined
- **Example**:
  
    <img src="assets/image 17.png" alt="image" style="zoom:16%; margin-left: 0" />

  

### Floyd-Warshall’s Algorithm

- **Aims**:
    - Detects the presence of circuits with negative cost (identifies ill-definedness)
    - Provides a set of shortest paths between ==**all pairs of nodes**==, even when there are arcs with ==**negative cost**==
- **Input**: Directed $G=(N,A)$ with an $n \times n$ cost matrix, $C = [c_{ij}]$
- **Output**: For each pair of nodes $i,j \in N$ , the cost $d_{ij}$ of the shortest path from $i$ to $j$
- **Data Structure**: Two $n \times n$ matrices, $D$ , $P$ , whose elements correspond, at the end of the algorithm, to:
    - $d_{ij}$ = cost of a shortest path from $i$ to $j$
    - $p_{ij}$ = predecessor of $j$ in that shortest path from $i$ to $j$
      
        <img src="assets/image 18.png" alt="image" style="zoom:5%; margin-left: 0"/>
    
- **Initialization**:
  
    $d_{ij} = \begin{cases} 0 & i = j \\ c_{ij} & i \neq j \land (i,j) \in A, \quad p_{ij} = i, \ i \in N \\ + \infin & \text{otherwise} \end{cases}$
    
    <img src="assets/image 19.png" alt="image" style="zoom:12%; margin-left: 0" />
    
- **Triangular Operation**: For each pair of nodes $i, j$ with $i \neq u$ and $j \neq u$ (including case $i = j$), check whether when going from $i$ to $j$ it is more convenient to go via $u$ : if $d_{iu} + d_{uj} < d_{ij}$ then $d_{ij} \leftarrow d_{iu} + d_{uj}$
  
    <img src="assets/image 20.png" alt="image" style="zoom:8%; margin-left: 0" />
    
    <img src="assets/image 21.png" alt="image" style="zoom:30%; margin-left: 0" />
    
    <img src="assets/image 22.png" alt="image" style="zoom:40%; margin-left: 0" />
    
- **Complexity**:
  
    <img src="assets/image 23.png" alt="image" style="zoom:33%; margin-left: 0" />
    
    $O(n^3)$ where $n = |N|$
    

  

> **Proposition**: Floyd-Warshall’s algorithm is exact

  

## Optimal Paths in Directed Acyclic Graphs

- **Definition**: A ==**directed graph**== $G=(N,A)$ is ==**asyclic**== (is a DAG) if it contains no circuits
  
    <img src="assets/image 24.png" alt="image" style="zoom:12%; margin-left: 0" />
    
- **Problem**: Given a DAG $G = (N,A)$ with a cost $c_{ij} \in \mathbb{R}$ for each $(i,j) \in A$ , and nodes $s$ and $t$ , determine a ==**shortest**== (==**longest**==) ==**path**== from $s$ to $t$
- **Property (Topological order)**:
  
    - The nodes of any DAG $G$ can be ordered topologically, that is, indexed so that for each arc $(i,j) \in A$ we have $i<j$
    - The topological order can be exploited in a very efficient ==**Dynamic Programming**== algorithm to find shortest (or longest) paths
- **Topological ordering method**: $G = (N,A)$ is represented via the lists of predecessors $\delta^-(v)$ and successors $\delta^+(v)$ for each node $v$
  
    <img src="assets/image 25.png" alt="image" style="zoom:10%; margin-left: 0" />
    
    1. Assign the smallest positive integer not yet assigned to a node $v \in N$ with $\delta^-(v) = \empty$
       
        <img src="assets/image 26.png" alt="image" style="zoom:10%; margin-left: 0" />
        
    2. Delete the node $v$ with all its incident arcs
    3. Go back to 1. until there are nodes in the current subgraph
- **Example**:
  
    <img src="assets/image 27.png" alt="image" style="zoom:35%; margin-left: 0" />
    
- **Complexity**: $O(n+m)$ where $n = |N|$ and $m = |A|$ because each node/arc is considered at most once

  

### Dynamic Programming for Shortest Paths in DAGs

- Any ==**shortest path**== $\pi_t$ from 1 to $t$ with at least 2 arcs, can be subdivided into two parts: $\pi_i$ and $(i,t)$ , where $\pi_i$ is a ==**shortest subpath**== from $s$ to $i$
  
    <img src="assets/image 28.png" alt="image" style="zoom:6%; margin-left: 0" />
    
    - For each node $i = 1, ..., t$ , let $L_i$ be the ==**cost of a shortest path**== from 1 to $i$ . Then
      
        $$\displaystyle L_t = \min_{(i,t) \in \delta^-(t)} \{L_i+c_{it}\}$$
        
        where the minimum is taken over all possible predecessors $i$ of $t$
        
    - If $G$ is a DAG and nodes are topologically ordered, the ==**only possible predecessors**== of $t$ in a shortest path $\pi_t$ from 1 to $t$ are those with index ==$i \lt t$==. Thus
      
        $$\displaystyle L_t = \min_{i<t}\{L_i + c_{it}\}$$
        
    - In a $G$ with circuits, any node different from $t$ can be a predecessor of $t$ in $\pi_t$
- For DAGs whose nodes are topologically ordered, $L_{t-1},.., L_1$ satisfy the same type of recursive relations:
  
    $$L_{t-1} = \min_{i < t-1} \{L_i + c_{i,t-1}\} ; \ ... \ ; \ L_2 = \min_{i=1} \{L_i + c_{i2}\} = L_1 + c_{12}; \ L_1 = 0$$
    
    which can be solved in the reverse order:
    
    $$L_1 = 0; \ L_2 = L_1 + c_{12}; \ ... \ ; \ L_t = \min_{i<t} \{L_i + c_{it}\}$$
    
    <img src="assets/image 29.png" alt="image" style="zoom:40%; margin-left: 0" />
    
- **Complexity**:
  
    <img src="assets/image 30.png" alt="image" style="zoom: 15%; margin-left: 0" />
    
    - Topological ordering of the nodes: $O(n+m)$ with $m = |A|$
    - Each node/arc is considered exactly once: $O(n + m)$
    
    Overall: $O(n + m)$
    
      
    

> **Proposition**: The Dynamic Programming algorithm for finding shortest (longest) paths in DAGs is exact

  

## Project Planning

A ==**project**== consists of a set of $m$ activities with their duration: activity $A_i$ has (estimated) duration $d_i \geq 0$ , $i = 1,..., m$

Some pairs of activities are subject to a precedence constraint: $A_i \prec A_j$ indicates that $A_j$ can start only after the end of $A_i$

- **Model**:
    - A project can be represented by a directed graph $G = (N,A)$ where:
        - each arc corresponds to an activity
        - arc length represents the duration of the activity
    - To account for the precedence constraints, the arcs must be positioned so that:
      
        <img src="assets/image 31.png" alt="image" style="zoom:13%; margin-left: 0" />
        
    - Each node $v$ corresponds to the event “end of all the activities $(i,v) \in \delta^-(v)$” and, hence, to the possible beginning of all the $(v,j) \in \delta^+(v)$
      
        <img src="assets/image 32.png" alt="image" style="zoom:8%; margin-left: 0" />
    
- **Example 1**:
    
    - Activities: $A, \ B, \ C, \ D, \ E$
    - Precedences: $A \prec B, \ A \prec C, \ B \prec D, \ C \prec D, \ B \prec E$
      
        <img src="assets/image 33.png" alt="image" style="zoom:15%; margin-left: 0" />
        
          
        

> **Property**: The directed graph $G$ representing any project is acyclic (it is a DAG)

  

- **Example 2**:
    - Graph $G$ can be simplified by contracting some arcs:
      
        <img src="assets/image 34.png" alt="image" style="zoom:17%; margin-left: 0" />
        
    - Pay attention ==**not to introduce unwanted precedence constraints**==
      
        <img src="assets/image 35.png" alt="image" style="zoom:11%; margin-left: 0" />
        
        - If this dummy activity is maintained, ==**unwanted**== $C \prec E$ is implied!
    - Artificial nodes or artificial arcs are introduced so that graph $G$ :
      
        - contains a unique initial nodes $s$ corresponding to the event “beginning of the project”,
        - contains a unique final node $t$ corresponding to the event “end of the project”,
        - does not contain multiple arcs (with same endpoints)
        
        <img src="assets/image 36-5164665.png" alt="image" style="zoom:16%; margin-left: 0" />
    
- **Problem**: Given a project (set of activities with durations and precedence constraints), ==**schedule**== the activities so as to ==**minimise**== the overall ==**project duration**==, i.e., the time needed to complete all activities
  
    <img src="assets/image 37.png" alt="image" style="zoom:12%; margin-left: 0" />

  

> **Property**: The ==**minimum**== overall project ==**duration**== = ==**length of a longest path**== from $s$ to $t$

  

### Critical Path Method (CPM)

- **Determine**:
    - a ==**schedule**== (specifying the order and the alotted time for the activities) that minimises the overall project duration
    - the ==**slack**== of each activity, i.e., the amount of time by which its execution can be delayed, without affecting the overall minimum project duration
- **Initialization**: Construct the graph $G$ representing the project and find a topological order of the nodes
- **Steps**:
    1. Consider the nodes by increasing indices and for each $h \in N$, find: the ==**earliest time**== $T_{min_h}$ at which the event associated to node $h$ can occur ( $T_{min_n}$ corresponds to the ==**minimum project duration**==)
    2. Consider the nodes by decreasing indices and for each $h \in N$, find: the ==**latest time**== $T_{max_h}$ at which the event associated to node $h$ can occur without delaying the project completion date beyond $T_{min_n}$
    3. For each activity $(i,j) \in A$, find the ==**slack**==: $\sigma_{ij} = T_{max_j} - T_{min_i} - d_{ij}$
- **Example**:
  
    <img src="assets/image 38.png" alt="image" style="zoom:30%; margin-left: 0" />
    
    <img src="assets/image 39.png" alt="image" style="zoom:30%; margin-left: 0" />
    
    - **Step I**:
      
        <img src="assets/image 40.png" alt="image" style="zoom:23%; margin-left: 0" />
        
    - **Step II**:
      
        <img src="assets/image 41.png" alt="image" style="zoom:26%; margin-left: 0" />
        
    - **Longest Path**:
      
        <img src="assets/image 42.png" alt="image" style="zoom:20%; margin-left: 0" />
    
- **Input**: Graph $G=(N,A)$, with $n = |N|$, $m = |A|$ and the duration $d_{ij}$ associated to each $(i,j) \in A$
- **Output**: $(T_{min_i}, \ T_{max_i})$, for $i = 1,...,m$
- **Complexity**:
  
    <img src="assets/image 43.png" alt="image" style="zoom:20%; margin-left: 0" />
    
    $O(n+m)$, or simply $O(m)$
    
- **Definition 1**: An activity $(i,j)$ with zero slack $(\sigma_{ij} = T_{max_j} - T_{min_i} - d_{ij} = 0)$ is ==**critical**==
  
    <img src="assets/image 44.png" alt="image" style="zoom:20%; margin-left: 0" />
    
    - $(3, 7)$, $(4,6)$, $(4,8)$ are not critical, since: $\sigma_{37} = 9-5-3=1$, $\sigma_{46} = 10-6-3 = 1$, $\sigma_{48} = 11-6-4 = 1$
    - **Note**: $T_{min_i} = T_{max_i}$ and $T_{min_j} = T_{max_j}$ do not suffice to have $\sigma_{ij} = T_{max_j} - T_{min_i} - d_{ij} = 0$ !
- **Definition 2**: A ==**critical path**== is an $s-t$ path only composed of critical activities (one such path always exists)
  
    <img src="assets/image 45.png" alt="image" style="zoom:21%; margin-left: 0" />
    
- **Gantt Charts**:
  
    <img src="assets/image 46.png" alt="image" style="zoom:30%; margin-left: 0" />

  

# Network Flows

- **Definition**:
    - A ==**network**== is a directed and connected graph $G = (N,A)$ with a source $s \in N$ and a sink $t \in N$ , with $s \neq t$ , and a ==**capacity**== ==$k_{ij} \geq 0$== , for each arc $(i,j) \in A$
      
        <img src="assets/image 47.png" alt="image" style="zoom:13%; margin-left: 0" />
        
    - A ==**feasible flow**== $\underline{x}$ from $s$ to $t$ is a vector $\underline{x} \in \mathbb{R}^m$ with a component $x_{ij}$ for each arc $(i,j) \in A$ satisfying the ==**capacity constraints**==
      
        $$0 \leq x_{ij} \leq k_{ij}, \quad \forall (i,j) \in A$$
        
        <img src="assets/image 48.png" alt="image" style="zoom:20%; margin-left: 0" />
        
        and the ==**flow balance constraints**== at each intermediate node $u \in N$ ( $u \neq s, t$ )
        
        $$\sum_{(i,u) \in \delta^-(u)} x_{iu} = \sum_{(u,j) \in \delta^+(u)} x_{uj}, \quad \forall u \in N \backslash \{s, t\}$$
        
    - The ==**value of flow**== $\underline{x}$ is $\displaystyle \varphi = \sum_{(s,j) \in \delta^+(s)} x_{sj}$
    - Given a network and a feasible flow $\underline{x}$ , and arc $(i,j) \in A$ is ==**saturated**== (==**empty**==) if $x_{ij} = k_{ij}$ ( $x_{ij} = 0$ )
        - $x_{1t}$ is empty
        - $x_{2t}$ is saturated

  

## Maximum Flow Problem

- **Problem**: Given a network $G = (N,A)$ with an integer capacity $k_{ij}$ for each arc $(i,j) \in A$ , and nodes $s, t \in N$ , determine a ==**feasible flow**== $\underline{x} \in \mathbb{R}^m$ from $s$ to $t$ of ==**maximum value**==
- **Note**: If there are many sources/sinks with a unique type of product, dummy nodes $s^*$ and $t^*$ can be added:
  
    <img src="assets/image 49.png" alt="image" style="zoom:17%; margin-left: 0" />
    
- **Linear Programming Model**:
  
    $$\max \quad \varphi \\ \text{s. t.} \quad \sum_{(u,j) \in \delta^+(u)} x_{uj} - \sum_{(i,u) \in \delta^-(u)} x_{iu} = \begin{cases} \varphi & u = s \\ -\varphi & u = t \\ 0 & \text{otherwise} \end{cases} \\ 0 \leq x_{ij} \leq k_{ij} \quad (i,j) \in A \\ \varphi \in \mathbb{R} \quad x_{ij} \in \mathbb{R} \quad (i,j) \in A$$
    
    where $\varphi$ denotes the value of the feasible flow $\underline{x}$ ( $\varphi$ is also the amount exiting from $s$ )
    
      
    

## Cuts & Feasible Flows & Weak Duality

- **Definition**:
    - A ==**cut separating**== ==$s$== ==**from**== ==$t$== is $\delta(S)$ of $G$ with $s \in S \subset N$ and $t \in N \backslash S$
    - ==**Capacity of the cut**== $\delta(S)$ induced by $S$ : $k(S) = \sum_{(i,j) \in \delta^+(S)} k_{ij}$
      
        <img src="assets/image 50.png" alt="image" style="zoom:27%; margin-left: 0" />
        
    - Given a feasible flow $\underline{x}$ from $s$ to $t$ and a cut $\delta(S)$ separating $s$ from $t$ , the ==**value of the feasible flow**== ==$\underline{x}$== ==**through the cut**== ==$\delta(S)$== is
      
        $$\varphi(S) = \sum_{(i,j) \in \delta^+(S)} x_{ij} - \sum_{(i,j) \in \delta^-(S)} x_{ij}$$
        
        <img src="assets/image 51.png" alt="image" style="zoom:16%; margin-left: 0" />
        
        With this notation the value of the flow $\underline{x}$ is $\varphi = \varphi(\{s\})$
        

  

> **Property**: Let $\underline{x}$ be any feasible flow from $s$ to $t$ . For every cut $\delta (S)$ separating $s$ from $t$ , we have $\varphi(S) = \varphi (\{s\})$
>
> <img src="assets/image 52.png" alt="image" style="zoom:15%; margin-left: 0" />

  

> **Property**: For every feasible flow $\underline{x}$ from $s$ to $t$ and every cut $\delta(S)$ , with $s \subseteq N$ , separating $s$ from $t$ , we have $\varphi(S) \leq k(S)$ (value of the flow ≤ capacity of the cut)

  

- **Consequence**:
    - If $\varphi(S) = k(S)$ for a subset $S \subseteq N$ with $s \in S$ and $t \notin S$ , then $\underline{x}$ is a flow of maximum value and the cut $\delta(S)$ is of minimum capacity
    - The property ==$\varphi(S) \leq k(S)$== for any feasible flow $\underline{x}$ and for any cut $\delta(S)$ separating $s$ from $t$, expresses a ==**weak duality relationship**== between the two problems:
        - **Primal Problem**: Given $G = (N,A)$ with integer capacities on the arcs and $s,t \in N$ , determine a ==**feasible flow**== of ==**maximum value**==
        - **Dual Problem**: Given $G=(N,A)$ with integer arc capacities and $s, t \in N$ , determine a cut (separating $s$ from $t$) of ==**minimum capacity**==

  

## Ford-Fulkerson’s Algorithm

- **Idea**: Start from a feasible flow $\underline{x}$ and try to iteratively increase its value $\varphi$ by sending, at each iteration, an additional amount of product along a (an undirected) path from $s$ to $t$ with a strictly positive residual capacity
  
    <img src="assets/image 53.png" alt="image" style="zoom:16%; margin-left: 0" />
    
    Can the value of the current feasible flow $\underline{x}$ ( $\varphi(\{s\} = 2)$ ) be increased?
    
    - If $(i.j)$ is ==**not saturated**== ( $x_{ij} < k_{ij}$ ), we can increase $x_{ij}$
    - $(i, j)$ is ==**not empty**== ( $x_{ij}$ > 0 ) , we can decrease $x_{ij}$ while respecting $0 \leq x_{ij} \leq k_{ij}$
    
    <img src="assets/image 54.png" alt="image" style="zoom:22%; margin-left: 0" />
    
- **Rationale**: The unit of product that was going from 1 to 2 is redirected to $t$ and the missing unit in 2 is supplied from $s$
  
    <img src="assets/image 55.png" alt="image" style="zoom:120%; margin-left: 0" />
    
- **Definition**: A path $P$ from $s$ to $t$ is an ==**augmenting path**== w.r.t. the current feasible flow $\underline{x}$ if $x_{ij} < k_{ij}$ for every forward arc and $x_{ij} > 0$ for every backward arc
    - Since the maximum additional amount of product that can be sent along the augmenting path $\langle (s,1), \ (1,2), \ (2,t) \rangle$ is equal to $\delta = 1$
      
        <img src="assets/image 56.png" alt="image" style="zoom:14%; margin-left: 0" />
        
        We obtain the new feasible flow $x$
        
        <img src="assets/image 57.png" alt="image" style="zoom:14%; margin-left: 0" />
        
    - Given a feasible flow $\underline{x}$ for $G = (N,A)$ , we construct ==the== ==**residual network**== ==$\bar{G} = (N, \bar{A})$== associated to $\underline{x}$ , according for all possible flow variations w.r.t. $\underline{x}$
      
        <img src="assets/image 58.png" alt="image" style="zoom:42%; margin-left: 0" />
        
        <img src="assets/image 59.png" alt="image" style="zoom:20%; margin-left: 0" />
    
- **Example**:
  
    <img src="assets/image 60.png" alt="image" style="zoom:42%; margin-left: 0" />
    
    <img src="assets/image 61.png" alt="image" style="zoom:45%; margin-left: 0" />
    
    <img src="assets/image 62.png" alt="image" style="zoom:22%; margin-left: 0" />
    
    For $S^* = \{1,4\}$ , all ==**outgoing**== arcs of $\delta_G(S^*)$ are ==**saturated**== and all ==**entering**== ones are ==**empty**==
    

  

> **Theorem**: The value of a feasible flow of maximum value = the capacity of a cut of minimum capacity: $\varphi(S) = k(S^*)$

  

- **Notes**:
    - If all the capacities $k_{ij}$ are integer ( $\in \mathbb{Z}^+$ ) , the flow $\underline{x}$ of maximum value has all $x_{ij}$ integer and an integer value $\varphi^*$
    - Ford-Fulkerson’s algorithm is not greedy ( $x_{ij}$ are also decreased, it doesn’t just increase flows along augmenting paths without regard to possible backwards flows)
- **Input**: Graph $G = (N,A)$ with capacity $k_{ij} > 0$ , for every $(i,j) \in A$ , $s,t \in N$
- **Output**: Feasible flow $\underline{x}$ from $s$ to $t$ of maximum value $\varphi^*$
- **Complexity**:
  
    <img src="assets/image 63.png" alt="image" style="zoom:30%; margin-left: 0" />
    
    - Since $\delta > 0$ , the value $\varphi$ increases at each iteration (cycle)
    - If all $k_{ij}$ are integer, $\underline{x}$ and $\bar{k_{ij}}$ integer and $\delta \geq 1$ , then there are at most $\varphi^*$ increases
    - Since
      
        $$\varphi^* \leq k(\{s\}) \leq m \cdot k_{max}$$
        
        where $m=|A|$ and $k_{max} = \max\{k_{ij} : (i,j) \in A\}$ and each cycle is $O(m)$ , the ==**overall complexity**== is ==$O(m^2 \cdot k_{max})$==