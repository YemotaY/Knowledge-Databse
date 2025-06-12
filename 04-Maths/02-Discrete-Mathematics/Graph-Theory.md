# Graph Theory

**🏠 [Main](../../README.md)** | **🧮 [Mathematics](../README.md)** | **🔢 [Discrete Mathematics](../README.md)**

Graph theory is the study of graphs, which are mathematical structures used to model pairwise relations between objects. It's fundamental to computer science, providing the theoretical foundation for networks, algorithms, and data structures.

## Overview

Graph theory applications in computer science:
- **Algorithms**: Shortest path, minimum spanning tree, network flow
- **Data Structures**: Trees, heaps, linked structures
- **Network Analysis**: Social networks, internet topology, circuit design
- **Optimization**: Scheduling, resource allocation, routing
- **Artificial Intelligence**: State space search, knowledge representation
- **Database Systems**: Query optimization, schema design

## Basic Definitions

### Graph Components

**Graph G = (V, E)**
- V: Set of vertices (nodes)
- E: Set of edges (connections between vertices)

**Types of Graphs:**
- **Undirected Graph**: Edges have no direction
- **Directed Graph (Digraph)**: Edges have direction
- **Weighted Graph**: Edges have associated weights/costs
- **Simple Graph**: No self-loops or multiple edges
- **Multigraph**: Multiple edges between same vertices allowed

**Graph Properties:**
- **Order**: Number of vertices |V|
- **Size**: Number of edges |E|
- **Degree**: Number of edges incident to a vertex
  - In directed graphs: in-degree and out-degree
- **Adjacent**: Two vertices connected by an edge
- **Incident**: Edge connects to a vertex

### Common Graph Types

**Complete Graph Kₙ:**
- Every pair of vertices is connected
- Number of edges: n(n-1)/2

**Bipartite Graph:**
- Vertices can be divided into two disjoint sets
- Edges only between different sets
- **Complete Bipartite Kₘ,ₙ**: Every vertex in one set connects to every vertex in the other

**Cycle Graph Cₙ:**
- Vertices form a single cycle
- Each vertex has degree 2

**Path Graph Pₙ:**
- Vertices form a single path
- Two vertices of degree 1, others degree 2

**Tree:**
- Connected graph with no cycles
- n vertices, n-1 edges
- Unique path between any two vertices

## Graph Representations

### Adjacency Matrix
2D array where A[i][j] = 1 if edge exists between vertices i and j.

```python
class GraphMatrix:
    def __init__(self, num_vertices):
        self.V = num_vertices
        self.adj_matrix = [[0] * num_vertices for _ in range(num_vertices)]
    
    def add_edge(self, u, v, weight=1):
        self.adj_matrix[u][v] = weight
        self.adj_matrix[v][u] = weight  # For undirected graph
    
    def has_edge(self, u, v):
        return self.adj_matrix[u][v] != 0
```

**Advantages:**
- O(1) edge lookup
- Simple implementation
- Good for dense graphs

**Disadvantages:**
- O(V²) space complexity
- O(V²) time to iterate through all edges

### Adjacency List
Array of lists where each list contains neighbors of a vertex.

```python
class GraphList:
    def __init__(self, num_vertices):
        self.V = num_vertices
        self.adj_list = [[] for _ in range(num_vertices)]
    
    def add_edge(self, u, v, weight=1):
        self.adj_list[u].append((v, weight))
        self.adj_list[v].append((u, weight))  # For undirected graph
    
    def get_neighbors(self, u):
        return self.adj_list[u]
```

**Advantages:**
- O(V + E) space complexity
- Efficient for sparse graphs
- Easy to iterate through neighbors

**Disadvantages:**
- O(degree(v)) time for edge lookup
- More complex implementation

### Edge List
Simple list of all edges in the graph.

```python
class EdgeList:
    def __init__(self):
        self.edges = []
    
    def add_edge(self, u, v, weight=1):
        self.edges.append((u, v, weight))
    
    def get_edges(self):
        return self.edges
```

**Use Cases:**
- Algorithms that process all edges (Kruskal's MST)
- When edge order matters
- Simple storage format

## Graph Traversal Algorithms

### Depth-First Search (DFS)
Explores as far as possible along each branch before backtracking.

```python
def dfs(graph, start, visited=None):
    if visited is None:
        visited = set()
    
    visited.add(start)
    print(start)  # Process vertex
    
    for neighbor in graph.get_neighbors(start):
        if neighbor not in visited:
            dfs(graph, neighbor, visited)
    
    return visited
```

**Applications:**
- **Topological Sorting**: Ordering of directed acyclic graph
- **Cycle Detection**: Finding cycles in graphs
- **Connected Components**: Finding disconnected parts
- **Path Finding**: Finding paths between vertices

**Time Complexity:** O(V + E)
**Space Complexity:** O(V) for recursion stack

### Breadth-First Search (BFS)
Explores all vertices at current depth before moving to next depth level.

```python
from collections import deque

def bfs(graph, start):
    visited = set()
    queue = deque([start])
    visited.add(start)
    
    while queue:
        vertex = queue.popleft()
        print(vertex)  # Process vertex
        
        for neighbor in graph.get_neighbors(vertex):
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
    
    return visited
```

**Applications:**
- **Shortest Path**: Unweighted graphs
- **Level-order Traversal**: Processing by distance from start
- **Connected Components**: Finding all reachable vertices
- **Bipartite Testing**: Checking if graph is bipartite

**Time Complexity:** O(V + E)
**Space Complexity:** O(V) for queue

## Shortest Path Algorithms

### Dijkstra's Algorithm
Finds shortest paths from single source to all vertices in weighted graph with non-negative weights.

```python
import heapq

def dijkstra(graph, start):
    distances = {vertex: float('infinity') for vertex in range(graph.V)}
    distances[start] = 0
    pq = [(0, start)]
    
    while pq:
        current_distance, current_vertex = heapq.heappop(pq)
        
        if current_distance > distances[current_vertex]:
            continue
        
        for neighbor, weight in graph.get_neighbors(current_vertex):
            distance = current_distance + weight
            
            if distance < distances[neighbor]:
                distances[neighbor] = distance
                heapq.heappush(pq, (distance, neighbor))
    
    return distances
```

**Time Complexity:** O((V + E) log V) with binary heap
**Space Complexity:** O(V)

**Applications:**
- **GPS Navigation**: Finding shortest routes
- **Network Routing**: Internet protocol routing
- **Game AI**: Pathfinding in games

### Bellman-Ford Algorithm
Handles negative edge weights and detects negative cycles.

```python
def bellman_ford(graph, start):
    distances = {v: float('infinity') for v in range(graph.V)}
    distances[start] = 0
    
    # Relax edges V-1 times
    for _ in range(graph.V - 1):
        for u in range(graph.V):
            for v, weight in graph.get_neighbors(u):
                if distances[u] + weight < distances[v]:
                    distances[v] = distances[u] + weight
    
    # Check for negative cycles
    for u in range(graph.V):
        for v, weight in graph.get_neighbors(u):
            if distances[u] + weight < distances[v]:
                return None  # Negative cycle detected
    
    return distances
```

**Time Complexity:** O(VE)
**Applications:**
- **Currency Arbitrage**: Detecting profit opportunities
- **Network Analysis**: Finding bottlenecks

### Floyd-Warshall Algorithm
Finds shortest paths between all pairs of vertices.

```python
def floyd_warshall(graph):
    dist = [[float('infinity')] * graph.V for _ in range(graph.V)]
    
    # Initialize distances
    for i in range(graph.V):
        dist[i][i] = 0
        for j, weight in graph.get_neighbors(i):
            dist[i][j] = weight
    
    # Find shortest paths
    for k in range(graph.V):
        for i in range(graph.V):
            for j in range(graph.V):
                dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j])
    
    return dist
```

**Time Complexity:** O(V³)
**Applications:**
- **Network Analysis**: All-pairs shortest paths
- **Game Theory**: Strategy optimization

## Minimum Spanning Tree

### Kruskal's Algorithm
Builds MST by adding edges in order of increasing weight.

```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [0] * n
    
    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]
    
    def union(self, x, y):
        px, py = self.find(x), self.find(y)
        if px == py:
            return False
        if self.rank[px] < self.rank[py]:
            px, py = py, px
        self.parent[py] = px
        if self.rank[px] == self.rank[py]:
            self.rank[px] += 1
        return True

def kruskal(graph):
    edges = sorted(graph.get_edges(), key=lambda x: x[2])
    uf = UnionFind(graph.V)
    mst = []
    
    for u, v, weight in edges:
        if uf.union(u, v):
            mst.append((u, v, weight))
            if len(mst) == graph.V - 1:
                break
    
    return mst
```

**Time Complexity:** O(E log E)
**Applications:**
- **Network Design**: Minimum cost network connections
- **Clustering**: Hierarchical clustering algorithms

### Prim's Algorithm
Builds MST by growing tree one vertex at a time.

```python
def prim(graph, start=0):
    mst = []
    visited = set([start])
    edges = [(weight, start, neighbor) for neighbor, weight in graph.get_neighbors(start)]
    heapq.heapify(edges)
    
    while edges and len(visited) < graph.V:
        weight, u, v = heapq.heappop(edges)
        
        if v not in visited:
            visited.add(v)
            mst.append((u, v, weight))
            
            for next_neighbor, next_weight in graph.get_neighbors(v):
                if next_neighbor not in visited:
                    heapq.heappush(edges, (next_weight, v, next_neighbor))
    
    return mst
```

**Time Complexity:** O(E log V)

## Advanced Topics

### Network Flow
**Maximum Flow Problem**: Find maximum flow from source to sink.

**Ford-Fulkerson Algorithm:**
```python
def ford_fulkerson(graph, source, sink):
    def bfs_path(source, sink, parent):
        visited = set([source])
        queue = deque([source])
        
        while queue:
            u = queue.popleft()
            for v, capacity in graph.get_neighbors(u):
                if v not in visited and capacity > 0:
                    visited.add(v)
                    parent[v] = u
                    if v == sink:
                        return True
                    queue.append(v)
        return False
    
    parent = {}
    max_flow = 0
    
    while bfs_path(source, sink, parent):
        path_flow = float('infinity')
        s = sink
        
        while s != source:
            path_flow = min(path_flow, graph.get_capacity(parent[s], s))
            s = parent[s]
        
        max_flow += path_flow
        v = sink
        
        while v != source:
            u = parent[v]
            graph.update_capacity(u, v, -path_flow)
            graph.update_capacity(v, u, path_flow)
            v = parent[v]
    
    return max_flow
```

**Applications:**
- **Network Capacity**: Internet bandwidth allocation
- **Transportation**: Traffic flow optimization
- **Bipartite Matching**: Assignment problems

### Graph Coloring
Assigning colors to vertices such that no adjacent vertices have the same color.

**Greedy Coloring:**
```python
def greedy_coloring(graph):
    colors = {}
    
    for vertex in range(graph.V):
        available_colors = set(range(graph.V))
        
        for neighbor, _ in graph.get_neighbors(vertex):
            if neighbor in colors:
                available_colors.discard(colors[neighbor])
        
        colors[vertex] = min(available_colors)
    
    return colors
```

**Applications:**
- **Scheduling**: Time slot assignment
- **Register Allocation**: Compiler optimization
- **Map Coloring**: Geographic map coloring

### Topological Sorting
Linear ordering of vertices in directed acyclic graph.

```python
def topological_sort(graph):
    in_degree = [0] * graph.V
    for u in range(graph.V):
        for v, _ in graph.get_neighbors(u):
            in_degree[v] += 1
    
    queue = deque([v for v in range(graph.V) if in_degree[v] == 0])
    result = []
    
    while queue:
        u = queue.popleft()
        result.append(u)
        
        for v, _ in graph.get_neighbors(u):
            in_degree[v] -= 1
            if in_degree[v] == 0:
                queue.append(v)
    
    return result if len(result) == graph.V else None  # Cycle detected
```

**Applications:**
- **Build Systems**: Dependency resolution
- **Course Scheduling**: Prerequisite ordering
- **Task Scheduling**: Project management

## Graph Algorithms Complexity Summary

| Algorithm | Time Complexity | Space Complexity | Use Case |
|-----------|----------------|------------------|----------|
| DFS | O(V + E) | O(V) | Connectivity, cycles |
| BFS | O(V + E) | O(V) | Shortest path (unweighted) |
| Dijkstra | O((V + E) log V) | O(V) | Shortest path (non-negative weights) |
| Bellman-Ford | O(VE) | O(V) | Shortest path (negative weights) |
| Floyd-Warshall | O(V³) | O(V²) | All-pairs shortest paths |
| Kruskal's MST | O(E log E) | O(V) | Minimum spanning tree |
| Prim's MST | O(E log V) | O(V) | Minimum spanning tree |
| Ford-Fulkerson | O(E × max_flow) | O(V) | Maximum flow |

## Practical Considerations

### Graph Library Usage
```python
import networkx as nx

# Creating graphs
G = nx.Graph()  # Undirected
G = nx.DiGraph()  # Directed
G = nx.MultiGraph()  # Multiple edges allowed

# Adding nodes and edges
G.add_node(1)
G.add_edge(1, 2, weight=3)

# Built-in algorithms
shortest_path = nx.shortest_path(G, source=1, target=2)
mst = nx.minimum_spanning_tree(G)
```

### Performance Optimization
1. **Choose Appropriate Representation**: Adjacency list for sparse, matrix for dense
2. **Memory Management**: Consider memory usage for large graphs
3. **Algorithm Selection**: Match algorithm to problem constraints
4. **Preprocessing**: Precompute frequently used information

## Common Applications

### Social Network Analysis
- **Centrality Measures**: Identifying influential nodes
- **Community Detection**: Finding clusters
- **Link Prediction**: Suggesting connections

### Computer Networks
- **Routing Protocols**: Finding optimal paths
- **Network Reliability**: Analyzing fault tolerance
- **Load Balancing**: Distributing traffic

### Compiler Design
- **Control Flow Graphs**: Program analysis
- **Dependency Graphs**: Optimization ordering
- **Register Allocation**: Resource management

---

*Graph theory provides the mathematical foundation for understanding and solving complex network and relationship problems in computer science.*
