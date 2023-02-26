# 2 Bug Algorithms: Pathfinding on the Move

The first set of Pathfinding Algorithms all start walking first and then react to their obstacles as they hit them. This might sound stupid, but it is very low in initial resource costs (the AI does not need to plan first), can be an interesting mechanic (an AI that's supposed to explore) and sometimes it's the only possible approach (e.g. cleaning robots don't get a map of your apartment).

It can also be used in combination with smarter Pathfinding Algorithms to allow your units to begin walking while waiting for the path calculation to be finished as a background process.

For all the upcoming samples, we will assume:
- a static 2d terrain
- a square grid
- every grid cell is either clear or blocked
- terrain is completely known
- `start` and `target` cells ar known
- can receive a cell's `neighbors`

## 2.1 First off, walk towards target

```
current_cell = start
while not at target
   neighbors = current_cell.neighbors
   best_neighbor = closest_to_target(neighbors)
   if best_neighbor is walkable
      current_cell = best_neighbor
   else
      error: "I'm stuck! Help!"
   end if
end while
```

This algorithm can nicely navigate to its target in an Open Field. But the slightest obstacle... and it will fail. 

## 2.2 If blocked, find a way around

```
current_cell <= start
while not at target
   neighbors <= current_cell.neighbors
   best_neighbor <= closest_to_target(neighbors)
   if best_neighbor is walkable
      current_cell <= best_neighbor
   else
      current_cell <= run_evasion_strategy(current_cell, best_neighbor)
   end if
end while
```

Okay, obviously, we need to find a way around the obstacle. But what could this strategy look like?

### 2.2.1 Evasion Strategy 1: Random Bounce

<img width="329" alt="image" src="https://user-images.githubusercontent.com/7360266/152982906-7ac859aa-38c9-480b-b825-144035c49224.png">



Our first strategy will be very naive: we just walk a random direction and hope that it brings us around the obstacle at some point.

```
candidates <= neighbors - best_neighbor
current_cell <= random from candidates
```

- not very smart
- might bounce back and forth a lot
- but eventually, it will often find its target

<img width="315" alt="image" src="https://user-images.githubusercontent.com/7360266/152982991-5f2812f8-5f78-43d1-8d43-08110fec914e.png">


- unfortunately, it gets stuck in concave obstacles
  - because it needs to walk "backwards"
  - but afterwards, it will always the previous cell as a "good candidate" again
- it will not notice, if there's no way

### 2.2.2 Evasion Strategy 2: Simple Tracing

<img width="315" alt="image" src="https://user-images.githubusercontent.com/7360266/152983015-9e8e318f-23cc-465d-abcf-412d14dcd3e3.png">


This time, we'll apply a classic strategy used in Mazes: we circumvent the obstacle by following the wall until we can freely walk in the original direction that we were blocked in. In this case, we'll always walk the left way and keep our "right hand" on the wall:

```
blocked_direction <= direction from current_cell to best_neighbor
while(current_cell + blocked_direction is not walkable)
   current_cell = follow_the_wall()
```

- Works nicely, even for concave obstacles

<img width="315" alt="image" src="https://user-images.githubusercontent.com/7360266/152983057-8197d090-63ba-403a-99f0-3e26578dd242.png">


- But fails in some cases

### 2.2.3 Evasion Strategy 3: Robust Tracing

<img width="315" alt="image" src="https://user-images.githubusercontent.com/7360266/153022117-a938ed33-69e5-44e8-8ceb-bf9b98a86d70.png">



This solution is a standard solution in robotics. As soon as we collide, we:
- calculate a line between current position and target
- we again choose left/right and trace the wall
- we stop tracing when both:
  - line was crossed at least twice
  - the original blocking point is no longer between position and target
- tracing fails, if it reaches the starting point of tracing again

```
original_obstacle <= best_neighbor
blocked_direction <= direction from current_cell to best_neighbor
blocked_line <= line through current_cell and target_cell
while(current_cell + blocked_direction is not walkable)
   current_cell = follow_the_wall()
```

An easier implementation of this would be:
- Trace the wall for as long as the total rotation is not 0 Degrees and the original direction is free.
- Turning Left = -90 Degrees.
- Turning Right = 90 Degrees.
- Walk until back at 0 Degrees. Not 360 or - 360.

## 2.3 SLAM: Simultaneous Localization and Mapping

We'll skip on this chapter, but this is a series of algorithms that explore their environment while building a Map of it at the same time. You could say it's learns while moving, or it has memory.

## 2.4 Ant Colony Optimization Algorithms

Also another interesting field: Ants walk randomly / explore and leave a decaying trace when they find something interesting, which makes other ants more likely to follow this path, too. Those ants will again increase the trace, if their search was successful, attracting more ants. This is also being applied in Computer Science.