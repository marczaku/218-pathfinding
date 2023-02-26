# Introduction

## 1.1 Traditional, 2d Grid

Pathfinding can solve this very traditional 2d grid maze problem:

<img src="https://en.scratch-wiki.info/w/images/Pathfinding.jpg">

You want to move from A to B on the shortest Path in a known grid of walkable and unwalkable cells. This path needs to be found (hence, Pathfinder), so we can walk it.

## 1.2 2d NavMesh

Your world could also be more complex than a simple grid, though. Objects of all shapes and sizes:

<img src="https://forum.unity.com/proxy.php?image=https%3A%2F%2Fi.gyazo.com%2F917507b346b0dcd98ce4cf20dd78b8a6.png&hash=affdb5a61a35ea8d1f164a73a4e61389">

In this case, you need to scan the area and find out, where you can walk and where we can't before finding a path from A to B.

## 1.3 3d NavMesh

Your game could also be 3-Dimensional:

<img src="https://docs.unity3d.com/550/Documentation/uploads/Main/NavMeshCover.png">

This will make a big difference in how you can actually scan the area. In a 2d-game, you can quite easily tell from above, what area is walkable, but in 3d, many parameters like walkable slope angles and walkable step height make the scan process more complicated.

## 1.4 Advanced Paths

Often, characters can do more than just walking to navigate environment:

<img src="https://doy2mn9upadnk.cloudfront.net/uploads/default/optimized/4X/6/c/5/6c55394420dec94acc9143d96b713b5bb57dd623_2_689x349.png">

They could maybe Jump, Fall, Open Doors, Climb Ladders, Crouch, Swim, Dive, Destroy environment, use a Vehicle, a Portal, a PowerUp or much more to navigate the Area. The Pathfinding algorithm needs to be able to consider these paths.

## 1.5 Dangerous Paths

Some paths could be dangerous, or limited, or slow you down:

<img src="https://developer.roblox.com/assets/blte5791b887ee3412c/Pathfinding-Paths-Shortest-Best.jpg">

The algorithm should make sure not to drown, consider whether swimming a shorter path is faster, or walking a longer path. It might have to weigh the options and consider walking into Open Fire to get to a goal or prefer to walk a long route in cover.

## 1.6 Limited Range of Motion

What if your character sits in a car?

<img src="https://3.bp.blogspot.com/-9mSvNWTvCIM/VwIQVT8lQMI/AAAAAAAAT1U/G3bPT-N1HfUumimyp_pbrK88L5kF_nw0g/s1600/self-driving-tesla-5.png">

Cars have a limited range of motion, they cannot rotate on the spot. They need to find paths that allow them to get around corners.

## 1.7 Extended Range of Motion

What if your character can fly?

<img src="https://preview.redd.it/6wodo82eydg41.png?width=960&crop=smart&auto=webp&s=805725d0a96aeba8ae27eb09580ff1c3a6dcb461">

Without being gravity-bound, the navigation is not as simple anymore. Surfaces have a local 2-dimensional space. And as long as we only walk on surfaces, 3-dimensional problems can be reduced to 2-dimensional ones. But what if we can truly navigate through a room of options?

## 1.8 Limited Knowledge

There might be Unexplored Areas or Fog of War

<img src="https://blog.gemserk.com/wp-content/uploads/2018/08/Stage1_fadeout_test5_with_objective_scaled.png">

This requires your Algorithm to "work with what it knows", possibly adapt its strategy as it "gains more knowledge".

## 1.9 Huge Amount of Data

Your world might be really, really huge

<img src="https://p.turbosquid.com/ts-thumb/1Z/JqcTPF/pf/hznew_0001/jpg/1606624876/1920x1080/fit_q99/81b598caca7b4d603c22a3a4884b2efe83f7843c/hznew_0001.jpg">

So you'll have to optimize your algorithms to make sure that you don't try to find a path one centimeter at a time.

## 1.10 Not quite a path, or...?

What, if your game looks like this?

<img src="https://image.winudf.com/v2/image1/Y29tLmdzci5ucHV6emxlX3NjcmVlbl8yXzE1OTk3MjM0NjVfMDI3/screen-2.jpg?fakeurl=1&h=360&type=webp">

Or this?

<img width="200" src="https://upload.wikimedia.org/wikipedia/commons/thumb/a/a6/Rubik%27s_cube.svg/960px-Rubik%27s_cube.svg.png">

Or this?

<img src="https://static.wikia.nocookie.net/essentialsdocs/images/7/70/Battle.png/revision/latest/scale-to-width-down/256?cb=20190219202514">

These are in fact all Pathfinding Problems, as we will see. You want to get from A (the game as it is right now) to B (you have solved the puzzle / won the battle) and ideally want to follow the shortest or safest path.

## 1.11 Other Variations
- you don't want the shortest, but the longest path (avoid losing)
- Yen's k-shortest Paths: you want the n shortest paths to compare them (alternative routes)
- All Pairs Shortest Path: you want the shortest path that walks the whole area (cleaning robot, patrolling AI)
- Minimum Spanning Tree: you want the shortest path that connects multiple points (travel planner, multiple win requirements)

Okay, that's enough. I hope that you can see that Pathfinding is equally complex and important.
