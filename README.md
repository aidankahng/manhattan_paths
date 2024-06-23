## Manhattan Paths

Starting from a position of (0, 0) you are tasked to reach a target location (a, b) where a and b are positive integers by only moving one unit in a positive direction at a time. How many valid paths are possible?

Combinatorial method:
```
from math import comb
def manhattan_paths1(target:tuple[int,int]) -> int:
    return comb(target[0]+target[1],target[0])
```

Dynamic Programming:
```
def manhattan_paths1(target:tuple[int,int]) -> int:
    dp_dict = {}
    dp_dict[target] = 1
    for i in range(target[0], -1, -1):
        for j in range(target[1], -1, -1):
            if (i,j) not in dp_dict:
                dp_dict[(i,j)] = dp_dict.get((i,j+1), 0) + dp_dict.get((i+1,j), 0)
    return dp_dict.get((0,0))
            
```


Oh No! New York City has some protests going on and certain coordinates are blocked by obstacles! In addition to the target location you are now also given a list of obstacle locations that you are not able to visit. Note that you are not able to circumvent (go around) these obstacles by moving in a negative direction.

```
def manhattan_paths2(target:tuple[int,int], obstacles:[tuple[int,int]]) -> int:
    dp_dict = {}
    dp_dict[target] = 1
    for obstacle in obstacles:
        dp_dict[obstacle] = 0
    for i in range(target[0], -1, -1):
        for j in range(target[1], -1, -1):
            if (i,j) not in dp_dict:
                dp_dict[(i,j)] = dp_dict.get((i,j+1), 0) + dp_dict.get((i+1,j), 0)
    return dp_dict.get((0,0))
```

You can't just drive aimlessly though! You are actually a part of a world famous pizza tour and need to visit a couple of amazing pizza shops as checkpoints along your path. The only valid paths not must visit all of the given pizza shop checkpoints if it is possible to do so without moving in any negative direction. Now, given an additional list of checkpoint locations, calculate the total number of valid paths you can take.

```
# We will treat going from one checkpoint to the next as a 
# miniature problem (identical to the problem with no checkpoints) 
# and at the end use multiplication similar to the idea of 
# combinatorial solving to calculate the total number of paths
def manhattan_path_mini(start:tuple[int, int], target:tuple[int,int], obstacles:[tuple[int,int]]) -> int:
    dp_dict = {}
    dp_dict[target] = 1
    for obstacle in obstacles:
        dp_dict[obstacle] = 0
    for i in range(target[0], start[0]-1, -1):
        for j in range(target[1], start[1]-1, -1):
            if (i,j) not in dp_dict:
                dp_dict[(i,j)] = dp_dict.get((i,j+1), 0) + dp_dict.get((i+1,j), 0)
    return dp_dict.get(start)

def manhattan_paths_checkpoints(target:tuple[int,int], 
    obstacles:[tuple[int,int]], checkpoints:[tuple[int,int]]) -> int:
    checkpoints.sort(key=lambda checkpoint: checkpoint[0] + checkpoint[1])
    mini_total_paths = []
    current_checkpoint = (0, 0)
    for checkpoint in checkpoints:
        mini_total_paths.append(manhattan_path_mini(current_checkpoint, checkpoint, obstacles))
        current_checkpoint = checkpoint
    mini_total_paths.append(manhattan_path_mini(current_checkpoint, target, obstacles))
    
    total_paths = 1
    for i in range(0, len(mini_total_paths)):
        total_paths *= mini_total_paths[i]
    return total_paths
```