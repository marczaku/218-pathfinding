## 3.2 Problem: The Path

Okay, we got a Graph. We have some Data Format, where we can find the Start-Node and the End-Node and we can find Neighbors of each Nodes. How do we find the Path from Start to End now?

There is a few ways of classifying Pathfinding Algorithms:
- Efficiency - how many nodes need to be visited in order to find the path?
- Completeness - is it guaranteed to find a solution, if it exists?
- Optimality - does it find the best solution?

### 3.2.1 Depth-First-Search

![image](https://user-images.githubusercontent.com/7360266/153076821-d55923c8-6290-4869-aebb-e8aacebcda89.png)

In blue, you can see the trace of the algorithm scanning the Grid.
In pink, you can see the final path that has been found.

Depth First, as already mentioned in Graphs, will always follow one path through until it is blocked and then backtrack to the last unblocked state and continue finding a path from there. As you can see in the example, it is quite inefficient at finding a path. But if a Path exists, it will always find it.


```
procedure find_path(start_node, end_node)
   path = [start_node]

   while path not empty
      found_next_node = false
      for each neighbor in path.peek().getNeighbors()
         if(neighbor in path)
            continue
         else
            path.push(neighbor)
            if(neighbor == end_node)   // NEW
               return path             // NEW
            end if                     // NEW
            found_next_node = true
            break
         end if
      end for
      if(not found_next_node)
         path.pop()
      end if
   end while
   return null // NEW
end procedure
```

There is a problem with this algorithm, though:

![image](https://user-images.githubusercontent.com/7360266/153077795-5a5dcd1b-45fe-4104-a75b-9b02a62e7612.png)

It's not very good at finding the shortest path :|

- Pro: Complete. Low Memory Usage.
- Con: Not CPU Efficient. Not Optimal.

### 3.2.2 Iterative Deepening Depth-First-Search

You could change the Depth-First-Search by limiting the depth and then iteratively going deeper and deeper. Meaning that in previous example:

Iteration 1: Depth-First-Search with Depth-Limit: 1
![image](https://user-images.githubusercontent.com/7360266/153079592-0297d962-23fa-4fc2-9fa5-b7a60995464e.png)

Iteration 2: Depth-First-Search with Depth-Limit: 2
![image](https://user-images.githubusercontent.com/7360266/153079668-aa53275e-5d6c-49bf-95ee-9ae8dfd10bfc.png)

Iteration 3: Depth-First-Search with Depth-Limit: 3
![image](https://user-images.githubusercontent.com/7360266/153079764-34cf00b0-3006-4170-95b9-e571ea7be00d.png)

Iteration 4: Depth-First-Search with Depth-Limit: 4
![image](https://user-images.githubusercontent.com/7360266/153079871-d1d5a36f-27d8-4b2a-8af3-a4e1fd728d16.png)

Careful: the following Pseudo-Code does not handle the case well, if no Path exists to the Target. You'd have to do an extra Error handling when you notice, that `depth` is larger than the maximum reachable depth.

```
procedure find_path(start_node, end_node)
   depth = 1
   while(true)
      result = find_path(start_node, end_node, depth++)
      if(result != null)
         return result
      end if
   end while
end procedure
```

```
procedure find_path(start_node, end_node, allowed_depth) // NEW
   visited_nodes = [start_node]
   path = [start_node]

   while path not empty
      found_next_node = false
      for each neighbor in path.peek().getNeighbors()
         if(neighbor in visited_nodes)
            continue
         else if depth > 0                // NEW
            visited_nodes.add(neighbor)
            path.push(neighbor)
            allowed_depth--               // NEW
            if(neighbor == end_node)
               return path
            end if
            found_next_node = true
            break
         end if
      end for
      if(not found_next_node)
         path.pop()
         allowed_depth++                  // NEW
      end if
   end while
   return null
end procedure
```

Those are a lot of steps. But! The algorithm is very Memory Efficient. While the number of visited cells might grow largely (which can be prevented by adding a visited-Flag on the Graph-Nodes instead - which then again needs to be reset before finding a path again), the path nodes will never be larger than the length of the final path. And the path is directly available as soon as the target has been found!

- Pro: Complete. Optimal. Low Memory Usage.
- Con: Not CPU Efficient.

### 3.2.3 Breadth-First-Search

![image](https://user-images.githubusercontent.com/7360266/153085691-05278aa0-b494-409f-8099-9a1ec4b38264.png)

In above image, you can see:
- blue: visited nodes
- purple: queued nodes
- pink: found path

Breadth First, as already mentioned in Graphs, will check all nodes in a queue. Making sure, that we first visit all nodes with a distance of 1, then all nodes with a distance of 2, then ... etc. until the target is found.

The special thing in Pathfinding is, that for each node that is queued, we additionally need to save the whole path that lead us to that node in the queue, too. After all:
- one path could be: ABCDF
- one path could be: ABCEG
- one path could be: ABCEH

In case the next neighbor of G (let's call him I) is our Target Node, we need to know, how we got there:

```
procedure find_path(start_node, end_node)
   visited_nodes = [start_node]
   todo_paths = [[start_node]]    // CHANGE

   while todo_paths not empty
      path = todo_paths.dequeue()
      current_node = path.peek()        // NEW
      for each neighbor in current_node.getNeighbors()
         if(neighbor == end_node)         // NEW
            return [path, neighbor]       // NEW
         else if(neighbor in visited_nodes)
            continue
         else
            visited_nodes.add(neighbor)
            todo_paths.enqueue([path, neighbor])    // CHANGE
         end if
      end for
   end while
end procedure
```

This escalates pretty quickly:

<img width="608" alt="image" src="https://user-images.githubusercontent.com/7360266/153086287-e8e33c27-77f1-440c-85f8-4c208b3d4480.png">

Here:
- the blue nodes are visited
- the green ones queued 
- yellow is the path to the Target Node

What you can't see:

![image](https://user-images.githubusercontent.com/7360266/153086789-d39ea5ed-60ae-4178-816f-b5aa3cd3473c.png)

In Pink: All of the different paths that need to be stored as well.

And that's actually a lot of used Memory. The direct neighbor of the start node is already part of 10 paths. The next one of 9 paths etce. We're talking about 40 Paths with a length of 10 until a path of length 10 is found (400 Nodes stored in Memory).

This can be reduced to 200, if you store, for each Cell, it's Previous Cell (Predecessor) in a Dictionary, or directly in the Cell. Instead of the neighbor of the start node being referenced in 10 paths, it will only know that it's predecessor was the Start Node.

Then, when we found a path, we walk from the current Node through all predecessors backwards to build the Path (and then reverse it).

```
procedure find_path(start_node, end_node)
   todo_nodes = [start_node]        // REVERT
   predecessors = []=>[]            // NEW

   while todo_nodes not empty
      current_node = todo_nodes.dequeue()
      for each neighbor in current_node.getNeighbors()
         if(neighbor == end_node)
            return build_path(predecessors, neighbor) // NEW
         else if(neighbor in predecessors)
            continue
         else
            predecessors[neighbor] = current_node  // NEW
            todo_nodes.enqueue(neighbor)           // REVERT
         end if
      end for
   end while
end procedure
```

```
procedure build_path(predecessors, neighbor)
   path = []
   while neighbor is not null
      path.push(neighbor)
      neighbor = predecessors[neighbor]
   end while
   path.reverse()
   return path
end procedure
```

Pro: Complete. Optimal.
Con: Complexity is polynomial (n^2). Memory usage is rather high.

### 3.2.4 Bidirectional Search

A really great optimization and one that works with most similar algorithms:

We can see, that the complexity gets more and more extreme, the longer the path is. It's 4*(n^2), actually. What, if we can make n half the size? Instead of 4 * (10 * 10) = 400 Nodes, it could be 4 * (5 * 5) = 100 Nodes! Nice! How do we do that?

<img width="581" alt="image" src="https://user-images.githubusercontent.com/7360266/153088243-d9bf5ef1-bbb7-455b-9b04-5c62d7be49d7.png">

We just start searching from Both Ends and complete the algorithm, as soon as a Node is in both ends' visited stacks! Then, you just build the path from there in both directions and combine it.

I won't specify the Pseudo-Code here. In general, it is a great optimization, but it does not change any characteristics of the Breadth-First-Search.

### 3.2.5 Dijkstra

Yay, finally an Algorithm named after a person. This algorithm must be a great improvement! Let's look at its results right away:

<img width="605" alt="image" src="https://user-images.githubusercontent.com/7360266/153089384-31e27a7b-9a27-4a17-a470-0b331c38c277.png">

Okay, that was underwhelming. Why is there no difference?

Becase the Dijkstra Algorithm does not actually change the kind of Search that we do on the Graph. All it changes, is, it allows us to specify costs on each transition
- e.g. road = 1, swamp = 3
- or Slussen to TCentralen = 7, Slussen to Sickla = 13
- or walk = 1, swim = 3, ladder = 10, teleport = 0

And then, the Algorithm helps us find the shortest path, respecting those costs. Cool after all, right?

```
procedure find_path(start_node, end_node)
   todo_nodes = []
   predecessors = []=>[]
   costs = []=>[]          // NEW
   
   todo_nodes.enqueue(0, start_node)
   costs[start_node] = 0   // NEW

   while path not empty
      current_node = todo_nodes.dequeue()
      if(current_node == end_node)
         return build_path(predecessors, current_node)
      end if
      for each connection in path.getNeighbors()
         neighbor = connection.next
         new_costs = costs[current_node] + connection.costs      // NEW
         if(neighbor in costs and costs[neighbor] <= new_costs)  // CHANGE
            continue
         else
            if(neighbor in todo_nodes)
               remove neighbor from todo_nodes
            end if
            predecessors[neighbor] = current_node
            costs[neighbor] = new_costs                // NEW
            todo_nodes.enqueue(new_costs, neighbor)    // CHANGE!
         end if
      end for
   end while
end procedure
```

Wait, what happened here? Why does `enqueue` suddenly take two arguments?

The first argument is the Priority. Lowest Value is returned first. Instead of always returning the one that was enqueued first, here the nodes receive a Priority and are returned in that order.

If you wish to implement this class yourself, have a go at implementing a Min-Heap. (It's a special kind of Binary Tree, not a Binary Search Tree)

Pro: Complete. Optimal. Can Deal with Connection-Costs.
Con: Complexity is polynomial (n^2). Memory usage is rather high.

### 3.2.6 Best-First-Search

After meeting two Families of Algorithms: Depth-First and Breadth-First, we'll now look at a third Family of Algorithms: Heuristic Algorithms.

Heuristic Algorithms are Algorithms that use a Heuristic (an approximation, an estimate, an assumption) into consideration when evaluating what's the best path.

For Dijkstra, all Nodes with a Distance of 5 are equally valuable. Even, if they head in the exact opposit direction.

With a Heuristic Algorithm, we pass in a Heuristic function which says, how promising a certain state looks.

This allows us, to then visit the most promising states first.

The first adaption of such an algorithm would be Best-First:

We use Dijkstra
- but sort todo_nodes by estimation instead of actual costs
- what could the estimation be? Let's start with Distance(node, end_node):

<img width="448" alt="image" src="https://user-images.githubusercontent.com/7360266/153093212-4bed33cc-2e60-4f46-9f8e-3dda74170456.png">

Hey, that's an algorithm that I can get behind of!

<img width="599" alt="image" src="https://user-images.githubusercontent.com/7360266/153093510-68956f35-09a8-4e64-a8e3-a457f8387318.png">

Problem: It can be fooled. Here, the upper path seems to always be closer to the goal than the lower path, so it doesn't really consider the lower path anymore. Looks like we still got some work ahead of us!

PRO: Complete. Fast.
CON: Non-Optimal. Ignores Costs.

### 3.2.7 A*

And now Kiss!
> A* = Dijkstra + Best-First

x = State
g(x) = actual costs
h(x) = estimated remaining costs
f(x) = rating, priority

f(x) = g(x) + h(x)

```
procedure find_path(start_node, end_node)
   todo_nodes = []
   todo_nodes.enqueue(0, start_node)
   predecessors = []=>[]
   costs = []=>[]
   costs[start_node] = 0

   while path not empty
      current_node = todo_nodes.dequeue()
      if(current_node == end_node)
         return build_path(predecessors, current_node)
      end if
      for each connection in path.getNeighbors()
         neighbor = connection.next
         new_costs = costs[current_node] + connection.costs
         if(neighbor in costs and costs[neighbor] <= new_costs)
            continue
         else
            if(neighbor in todo_nodes)
               remove neighbor from todo_nodes
            end if
            predecessors[neighbor] = current_node
            costs[neighbor] = new_costs
            heuristic = estimate_remaining(neighbor)            // NEW
            todo_nodes.enqueue(new_costs+heuristic, neighbor)   // CHANGE
         end if
      end for
      visited_nodes.add(current_node)
   end while
end procedure
```

<img width="568" alt="image" src="https://user-images.githubusercontent.com/7360266/153094147-7589f356-575c-458e-aa5d-735fc2400e5e.png">

That looks wonderful!

Fact: As long as your Heuristic Function never overestimates the actual remaining costs, A* is guaranteed to find the shortest path! => Stay Optimistic. But not too optimistic (like 0, this would be Dijkstra again), or your algorithm is useless (This is a huge problem, if you have Teleporters)

PRO: Complete. Fast. Optimal.*
CON: Heuristic must be correct.

### 3.2.8 More Algorithms
D* and IDA* appear to be worth reading up on. But I got no idea at this point, what these algorithms are. Apparently, D* is like A* but easier to update in case your environment changes.

FlowFields are also an interesting concept, especially, if there is only a few Targets, but hundreds or thousands of Actors. Here, you create a Grid for each Target, telling for each Cell, how to get there. Then each actor can know without any calculations, how to get there.

<img width="814" alt="image" src="https://user-images.githubusercontent.com/7360266/153094784-17684959-4787-4ec4-817d-c3bbe2722187.png">