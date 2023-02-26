### 3.2.8 More Algorithms
D* and IDA* appear to be worth reading up on. But I got no idea at this point, what these algorithms are. Apparently, D* is like A* but easier to update in case your environment changes.

FlowFields are also an interesting concept, especially, if there is only a few Targets, but hundreds or thousands of Actors. Here, you create a Grid for each Target, telling for each Cell, how to get there. Then each actor can know without any calculations, how to get there.

<img width="814" alt="image" src="https://user-images.githubusercontent.com/7360266/153094784-17684959-4787-4ec4-817d-c3bbe2722187.png">


### 3.2.9 More Graphs

It may make sense to generate multiple Graphs for your Game. 
- e.g. one for big enemies and one for small ones.
- one for characters that can climb and one for ones that can't.
- one for highways and one for small streets

They will allow you to map more precise paths for each use-case.

### 3.2.10 Less Actors

If you want to move multiple units from one place to another, maybe not each of them needs to calculate the path, but only one "leader" and then they can share the path?

### 3.2.11 Storing Pathfinding Information on the Nodes

Very often, you'll find that information like the Total Costs and the Predecessor are stored on the Nodes directly instead of in Dictionaries. That makes a lot of sense! But I did not want to show different Node-Class implementations but instead focus on the Algorithm.

But as mentioned before (on the Hashing-Slides):

```
Dictionary<Cell, int> costs;
costs[cell] = 5;
```

Is the same as:
```
cell.costs = 5;
```


