## 3.3 Problem: The Navigation

Well, now, all you got is a list of points. But your character needs to nicely follow these points, still.

## 3.3.1 Steering

<img src="https://arongranberg.com/astar/docs/images/aipath_variables.png">

Steering has many important features
- determine current target node
- evaluate target direction
- calculate desired velocity
- correct current velocity
- determine whether close enough to pick next target node

Steering can be exceptionally complex, if your nodes are special nodes (which require actions like interactions, jumping, ...) or if the steering correction is more complex (car, helicopter, ...)

## 3.3.2 Smoothing
Make the path smoother by using Bezier curves, or just use a smart steering algorithm to do this for you.

## 3.3.3 Funneling
Optimize the paths by applying the Funnel Algorithm to find shortest paths on NavMesh (e.g. Simple Stupid Funnel Algorithm), again, a smart steering behavior might be able to do these steps, too.

## 3.3.4 Local Avoidance
Avoid other actors on the way.

## 3.3.5 Reevaluate
Recalculate the path in regular intervals, when the environment changes or when the actor notices that it is stuck (maybe, he got pushed down or missed a jump)
