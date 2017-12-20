# CarND-Path-Planning-Project
#### Write up
--- 

## Introduction.

In this project, your goal is to design a path planner that is able to create smooth, safe paths for the car to follow along a 3 lane highway with traffic. A successful path planner will be able to keep inside its lane, avoid hitting other cars, and pass slower moving traffic all by using localization, sensor fusion, and map data.


## Code structure:

* The main code is present in src/main.cpp.
* The main libraries used for this project are :-
* Eigen 3.3: To help on defining matrices and vectors.
* spline.h: To create spline/polynominal. 
(Source:http://kluge.in-chemnitz.de/opensource/spline/ )
* uWS.h : WebSocket & HTTP server implementation 
(Source: https://github.com/uNetworking/uWebSockets)


## Implementation.

**Given**: map waypoints, ego car state (location & speed), other cars' state.

There are two parts of the solution

* **First Goal** - the car should be able to drive inside a lane.
* **Second Goal** - maintain speed, and a safe distance from the car in front.
* **Third Gaol** - acceleration and jerk shouldn't exceed the limits provided.
* **Fourth Goal** - Implement a lane changing policy, to safely change lane (without collisions), when the car in front drives too slow.

For the first three goals, I used Path Planning and took help from 'Project Walkthrough and Q&A' by Aaron & David.
For the fourth goal, I implemented a lane changing policy.

#### Path Planning.

1. If there is already an existing path planned, take previous path endpoint as reference.
1. If less than 2 points from previous path available, then use car as starting reference, using two points that make the path tangent to the car.
1. Next, add evenly spaced 30m points in Frenet, ahead of the starting reference (30, 60, 90m).
1. Shift the car reference angle to 0 degrees, and then create a spline.
1. Then, spline points are broken up in such a way, that we travel at our desired reference velocity.
1. Generate a path using points from the spline.


#### Lane Changing 
1. Lane change takes place, when the car in front of the ego vehicle moves slower than the ego desired speed, and it is safe to change to the lane left, or right.
1. It is considered safe to change lane if there is no vehicle between -5 and +20 meters from the ego longitudinal position.
1. I go over all the available sensor data and see all the lanes that are available. and then proceed to change only if the adjoining lane is available  
1. The final rubric to change the lanes is as follows
```
Check the availability of all the lanes

if lane == 2 (leftmost) or lane == 0 (righmost)
	 if center lane available
	 	shift
	 else 
	 	stay
else if lane == 1 (center)
	if left lane available,  
		shift
	else if right lane is available
		shift
	else
		stay
```



## Results and Conclusions

Complete video - [https://www.youtube.com/watch?v=-7QPip0AEdk](https://www.youtube.com/watch?v=-7QPip0AEdk)

1. The simulation run for some time without incidents occurring, sometimes exceeding 30'.[Success](1.png?raw=true "Success")
1. A rare case of incident was a collision, when a car from a neighbouring lane, having more or less the same longitudinal coordinate as ego, suddenly changed to the ego lane.
1. Collision occurs when there is a sudden decrease in the velocity of the car in front. In the image the car infront of the car infornt of me suddenly changed lanes so the car infront of me suddenly decelerated causing a collision.  [Collision](2.png?raw=true "Collision")
1. The ego car is forced to follow the car ahead if all three lanes are blocked. [Stuck](3.png?raw=true "Stuck")


#### Possible improvements
Introduce checks whether changing to another lane is beneficial
Check if lane can be changed to a lane farther away, rather than the one immediately next to it