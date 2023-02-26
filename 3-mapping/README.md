# 3 Pathfinding: This time, with a Map and a Plan!

<img width="300" src="https://camo.githubusercontent.com/3ea84edd32d72fee15daeaf2bb1a2cef79fdf05fbb78471d09fa40ba6f6f2007/687474703a2f2f692e696d6775722e636f6d2f396330524d756a2e706e67">

Why were our previous Algorithms so inefficient and "stupid"? Because they had to find a way on their own without any help.

Solution: Use a Map of the environment and plan the path that we want to take first.

## 3.1 Problem: The Map

We need to create a Map for our Pathfinding algorithm! 

A digital Map uses the following vocabulary:

- State: the current situation, e.g. current position in the maze, current pieces on chess board
- Transition: an action that leads from one state to another, e.g. making a step, moving the king
- Successor state: a state that can be reached from the current state using one transition, e.g. a position right next to the current one, or a state where one piece has moved in chess
- Target state: the state that we want to find, e.g. exit of a maze, solved rubik's cube
- State space: collection of all accessible states, e.g. all walkable cells of a maze, all states of a rubik's cube (10^19)

What does this sound like?

Indeed, a Graph! Please continue [here](../algodata-205-grid).

### 3.1.1 Occupancy Grid

<img width="315" alt="image" src="https://user-images.githubusercontent.com/7360266/153027145-7715819d-ac75-427f-a615-a85cea797c6f.png">

The idea is, that we have a map in form of a Grid. Why a Grid? Because it is very efficient to find the correct cell from where we're standing. e.g.

If each Grid Cell is 1x1 in Size and we're at position (5.7, 3.6), then we're standing on Cell (5, 3)

If each Grid Cell was 2x2 in Size, then for the same position (5.7, 3.6), we'd be standing on Cell (2, 1)

What's the Math?

```
index_x = floor((position_x-cell_offset_x)/cell_width)
```

#### 3.1.1.1 Where's my Graph?

Okay, cool, but didn't you just say we need a Graph?

Well, that's all a matter of perspective:

<img width="315" alt="image" src="https://user-images.githubusercontent.com/7360266/153036206-f0d5fc40-5f84-4597-9a3e-3d15b50bfe8e.png">

You can see the centers of the cells (red dots) as Nodes and the neighbouring, walkable Cells as Adjacent Cells, shaping Connections between them. You don't even need to create a Graph ahead of time, instead, you can implement a method like this:

```
procedure get_adjacent_nodes(x, y)
   adjacent_nodes = []
   if(nodes[x-1,y].walkable)
      adjacent_nodes.push([x-1,y])
   endif
   if(nodes[x+1,y].walkable)
   ...
   endif
   return adjacent_nodes
end procedure
```

For Graph Traversal, all you need is a Start Node and each nodes' neighbors, right?

#### 3.1.1.2 But where's my Grid?

<img width="413" alt="image" src="https://user-images.githubusercontent.com/7360266/153038130-fa28fc83-6051-436a-8084-5872a4051d84.png">

In some cases you might be lucky and have your Game Data stored in a Grid anyways (old-school 2d RPGs with tiled maps). Here, you could just store the information what's walkable directly on the Tile Information.

<img width="413" alt="image" src="https://user-images.githubusercontent.com/7360266/153038260-1ff7ec17-667a-4b21-8141-5fbde82a1796.png">

But you might have a world that's not so "simple". How do you end up with a grid in these cases?

##### 3.1.1.2.1 Bounding Boxes

This is a very simple approach:

<img width="315" alt="image" src="https://user-images.githubusercontent.com/7360266/153041410-d2082b0f-f238-4d8a-bd31-27e728227586.png">

If each of your Cells has a `walkable`-Property:
- iterate over all objects
- foreach object calculate the Bounds
- all Cells that fall within the Bounds
  - floot the min coordinate of the Bounds
  - ceil the max coordinate of the Bounds
  - will be flagged as `!walkable`

If your Environment is destructible, you can also reference the object in that cell.
- This can allow your AI to decide to destroy the building in that cell instead of walking around.

- Pro: Very simple to implement, low performance impact
- Con: Some shapes have a lot of "invisible blocked" Area

##### 3.1.1.2.2 Rasterization

For a more accurate representation, you could use Rasterization. A process that the GPU does when rendering your Vertex Data to the Screen: Converting Vertex-Data to Pixels:

<img width="413" alt="image" src="https://user-images.githubusercontent.com/7360266/153039852-012a3f6a-4511-48ba-9246-88b010fc70ac.png">

For Rasterization, there is multiple approaches. Either:
- you raycast the center of each Cell. If it hits something, then it's blocked (Sampling)
- you raycast multiple points of each Cell to better cover partially coverted cells (Multisampling)
- you first check the bounds of your shape (all cells outside the bounds don't matter)
  - and then run edge-tests (it's an algorithm) on your shape's edges
  - to find out, whether each cell is inside or not (imagine your shape is hollow, like a donut)

#### 3.1.1.3 Grid in 3D??

<img width="315" alt="image" src="https://user-images.githubusercontent.com/7360266/153045953-a338cf2d-cb15-4843-b3f2-2ac6615b9626.png">

<img width="315" alt="image" src="https://user-images.githubusercontent.com/7360266/153046160-7bea9893-07b0-4db8-b3aa-59c65dec0e67.png">

Your 3D World can also use a Grid for Navigation! It's just more Neighbor Cells for each cell. Keyword: Voxel [More](https://medium.com/ironequal/pathfinding-like-a-king-part-2-4b74588262af)

#### 3.1.1.4 It's not all Tiles

<img width="315" alt="image" src="https://user-images.githubusercontent.com/7360266/153048398-82480017-c016-41ed-9506-9b9320dfaccb.png">

- We talk about Tiles, if your Game has Rects and you can walk in four directions (left, right, up, down).
- It's Octiles, if you can also walk diagonally, resulting in a total of eight directions
- Hexagon-Tiles have a much nicer and organic shape, though, are not worse in performance and very often result in smoother paths. If your objects are more organically shaped, try Hexagons. If everything consists of Boxes, don't bother.

#### 3.1.1.5 Summary

So, Grids can be extremely useful because of how efficiently Cells can be accessed. The disadvantage is that they consume a lot of Memory (even 1000x1000 Cells are already 1 Million Cells = 1MB ber byte of Data in each Cell).

You can create Grids from your Game Data. Either during Runtime, or you could bake it during Build-Time. If your Game Data is simple enough and your World is small enough, this might be your best option. [More](https://svn.sable.mcgill.ca/sable/courses/COMP763/oldpapers/yap-02-grid-based.pdf)

Also, Grids are very efficient in case you need to Add or Remove Objects during Runtime. Updating Grids is very easy due to how the structure of Grids won't change and accessing Grids is so efficient. => e.g. RTS or Tower-Defence Game.

### 3.1.2 Graph

In many cases, your Data just happens to be a Graph Already. e.g. Public Transport Stations, Game States, maybe the character can only drive on Rails?

In this case, there is not much to add. We will see later, why we need a Graph of some sort.

### 3.1.3 Mesh

<img width="788" alt="image" src="https://user-images.githubusercontent.com/7360266/153051052-501dfc7a-c9b4-4fd1-b34a-9a50534d88b3.png">

Navigation Meshes are a great extension of Grids. Instead of rasterising the whole Map into same-sized blocks, the Map is smartly converted by generating a Mesh that covers all Corners of Obstacles nicely.

The advantage is that a really big empty area only needs a few vertices instead of thousands of empty Grid Cells.

#### 3.1.3.1 Where's my Graph?

<img width="544" alt="image" src="https://user-images.githubusercontent.com/7360266/153051587-418982e4-5ece-4e4f-bb4d-ff6338ef0295.png">

The Graph can be found by connecting each Polygon with its adjacent neighbors.

#### 3.1.3.2 But how do I create a Navigation Mesh?

Press the Correct Button in Unity or Unreal or get a Vertex-Editor and start modeling :D

Seriously, though, it's [not easy](https://gamedev.stackexchange.com/questions/38721/how-can-i-generate-a-navigation-mesh-for-a-tile-grid/43826)