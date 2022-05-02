# Breadth First Search
## AIM

To develop an algorithm to find the route from the source to the destination point using breadth-first search.

## THEORY
Breadth First Search (BFS) Algorithm. Breadth first search is a graph traversal algorithm that starts traversing the graph from root node and explores all the neighbouring nodes. Then, it selects the nearest node and explore all the unexplored nodes. The algorithm follows the same process for each of the nearest node until it finds the goal.It follows FIFO.It travel from left to right.
## DESIGN STEPS
### STEP 1:
Identify a location in the google map:

### STEP 2:
Select a specific number of nodes with distance and set that specific loation alone.

### STEP 3:
Import the required packages and create some methods to define and understand breadthfirstsearch.

### STEP 4:
Include the nodes(locations) and values(distance) in dictionary as keys and values.

### STEP 5:
Pass the required location it will return the distance and destination.


## ROUTE MAP
![ 2022-05-02 ](https://user-images.githubusercontent.com/75235477/166265439-74ce1adb-0b65-42b8-9db2-27bf239931c0.jpeg)


## PROGRAM
```pyhton
%matplotlib inline
import matplotlib.pyplot as plt
import random
import math
import sys
from collections import defaultdict, deque, Counter
from itertools import combinations

class Problem(object):
   def _init_(self, initial=None, goal=None, **kwds): 
       self._dict_.update(initial=initial, goal=goal, **kwds) 
       
   def actions(self, state):        
       raise NotImplementedError
   def result(self, state, action): 
       raise NotImplementedError
   def is_goal(self, state):        
       return state == self.goal
   def action_cost(self, s, a, s1): 
       return 1
   
   def _str_(self):
       return '{0}({1}, {2})'.format(
           type(self)._name_, self.initial, self.goal)
           
class Node:
   def _init_(self, state, parent=None, action=None, path_cost=0):
       self._dict_.update(state=state, parent=parent, action=action, path_cost=path_cost)

   def _str_(self): 
       return '<{0}>'.format(self.state)
   def _len_(self): 
       return 0 if self.parent is None else (1 + len(self.parent))
   def _lt_(self, other): 
       return self.path_cost < other.path_cost
       
failure = Node('failure', path_cost=math.inf) 
cutoff  = Node('cutoff',  path_cost=math.inf)

def expand(problem, node):
   "Expand a node, generating the children nodes."
   s = node.state
   for action in problem.actions(s):
       s1 = problem.result(s, action)
       cost = node.path_cost + problem.action_cost(s, action, s1)
       yield Node(s1, node, action, cost)
       

def path_actions(node):
   "The sequence of actions to get to this node."
   if node.parent is None:
       return []  
   return path_actions(node.parent) + [node.action]


def path_states(node):
   "The sequence of states to get to this node."
   if node in (cutoff, failure, None): 
       return []
   return path_states(node.parent) + [node.state]
   
FIFOQueue = deque

def breadth_first_search(problem):
   "Search shallowest nodes in the search tree first."
   node = Node(problem.initial)
   if problem.is_goal(problem.initial):
       return node
   frontier = FIFOQueue([node])
   reached = {problem.initial}
   while frontier:
       node = frontier.pop()
       for child in expand(problem, node):
           s = child.state
           if problem.is_goal(s):
               return child
           if s not in reached:
               reached.add(s)
               frontier.appendleft(child)
   return failure
   
class RouteProblem(Problem):
   """A problem to find a route between locations on a `Map`.
   Create a problem with RouteProblem(start, goal, map=Map(...)}).
   States are the vertexes in the Map graph; actions are destination states."""
   
   def actions(self, state): 
       """The places neighboring `state`."""
       return self.map.neighbors[state]
   
   def result(self, state, action):
       """Go to the `action` place, if the map says that is possible."""
       return action if action in self.map.neighbors[state] else state
   
   def action_cost(self, s, action, s1):
       """The distance (cost) to go from s to s1."""
       return self.map.distances[s, s1]
   
   def h(self, node):
       "Straight-line distance between state and the goal."
       locs = self.map.locations
       return straight_line_distance(locs[node.state], locs[self.goal])
       
class Map:
   """A map of places in a 2D world: a graph with vertexes and links between them. 
   In `Map(links, locations)`, `links` can be either [(v1, v2)...] pairs, 
   or a {(v1, v2): distance...} dict. Optional `locations` can be {v1: (x, y)} 
   If `directed=False` then for every (v1, v2) link, we add a (v2, v1) link."""

   def _init_(self, links, locations=None, directed=False):
       if not hasattr(links, 'items'): # Distances are 1 by default
           links = {link: 1 for link in links}
       if not directed:
           for (v1, v2) in list(links):
               links[v2, v1] = links[v1, v2]
       self.distances = links
       self.neighbors = multimap(links)
       self.locations = locations or defaultdict(lambda: (0, 0))

       
def multimap(pairs) -> dict:
   "Given (key, val) pairs, make a dict of {key: [val,...]}."
   result = defaultdict(list)
   for key, val in pairs:
       result[key].append(val)
   return result
   

nearby_locations = Map(
   {('Pondur','Sriperumputhur'):  2,('Sriperumputhur', 'Pennalur'):  6, ('Sriperumputhur', 'Pillaipakkam'): 2,('Pillaipakkam','Annai college'): 3,('Sriperumputhur', 'thodukkadu'): 11,('thodukkadu','Valarpuram'): 9,('Valarpuram','Saveetha college'): 1,('Pennalur', 'Rit college'): 6, ('Pennalur', 'Annai college'): 3,('Annai college', 'Cit college'):11,('Rit college','Saveetha college'):1,('Saveetha college','Chembarambakkam'):4,('Chembarambakkam','Thirumazhisai'):4, 
    ('Pillaipakkam','Amarambedu'): 7,('Amarambedu','Mudichur'): 10,('Mudichur','Thambaram'): 4,('Thambaram','Thandalam'): 11,('Thandalam','Porur'): 10, ('Mudichur','Kundrathur'): 14,('Kundrathur','poonamalle Bridge'): 9,('Kundrathur','Kattupakkam'): 10,('Cit college','Malayambakkam'): 3,('Malayambakkam','Kundrathur'): 4, ('Thirumazhisai','poonamalle Bridge'): 3, ('poonamalle Bridge','poonamallee bus stand'):  2,('poonamallee bus stand','Kattupakkam'):  3,('Kattupakkam','Porur'): 5,
  })

r0 = RouteProblem('Sriperumputhur','Saveetha college', map=nearby_locations)
r1 = RouteProblem('Pondur', 'poonamalle Bridge', map=nearby_locations)



goal_state_path=breadth_first_search(r1)
path_states(goal_state_path)
print("GoalStateWithPath:{0}".format(goal_state_path))
print("Total Distance={0} Kilometers".format(goal_state_path.path_cost))
```


## OUTPUT:

![2022-05-02 (2)](https://user-images.githubusercontent.com/75235477/166265272-b3e7d97a-a678-4fd3-8f56-740201b722f7.png)

## SOLUTION JUSTIFICATION:
The Route solutions are found by Breadth First Search algorithm(following FIFO and routes travelling from left to right).
## RESULT:
Thus,an algorithm developed to find the route from the source to the destination point using breadth-first search.
