# CarND-Path-Planning-Project

Self-Driving Car Engineer Nanodegree Program

## Author: ChangYuan Liu

## The project instruction

### Goals of the project

In this project your goal is to safely navigate around a virtual highway with other traffic that is driving +-10 MPH of the 50 MPH speed limit. You will be provided the car's localization and sensor fusion data, there is also a sparse map list of waypoints around the highway. The car should try to go as close as possible to the 50 MPH speed limit, which means passing slower traffic when possible, note that other cars will try to change lanes too. The car should avoid hitting other cars at all cost as well as driving inside of the marked road lanes at all times, unless going from one lane to another. The car should be able to make one complete loop around the 6946m highway. Since the car is trying to go 50 MPH, it should take a little over 5 minutes to complete 1 loop. Also the car should not experience total acceleration over 10 m/s^2 and jerk that is greater than 10 m/s^3.

#### The map of the highway is in data/highway_map.txt

Each waypoint in the list contains  [x,y,s,dx,dy] values. x and y are the waypoint's map coordinate position, the s value is the distance along the road to get to that waypoint in meters, the dx and dy values define the unit normal vector pointing outward of the highway loop.

The highway's waypoints loop around so the frenet s value, distance along the road, goes from 0 to 6945.554.

#### Main car's localization Data (No Noise)

["x"] The car's x position in map coordinates

["y"] The car's y position in map coordinates

["s"] The car's s position in frenet coordinates

["d"] The car's d position in frenet coordinates

["yaw"] The car's yaw angle in the map

["speed"] The car's speed in MPH

#### Previous path data given to the Planner

//Note: Return the previous list but with processed points removed, can be a nice tool to show how far along
the path has processed since last time.

["previous_path_x"] The previous list of x points previously given to the simulator

["previous_path_y"] The previous list of y points previously given to the simulator

#### Previous path's end s and d values

["end_path_s"] The previous list's last point's frenet s value

["end_path_d"] The previous list's last point's frenet d value

#### Sensor Fusion Data, a list of all other car's attributes on the same side of the road. (No Noise)

["sensor_fusion"] A 2d vector of cars and then that car's [car's unique ID, car's x position in map coordinates, car's y position in map coordinates, car's x velocity in m/s, car's y velocity in m/s, car's s position in frenet coordinates, car's d position in frenet coordinates.

## Details

1. The car uses a perfect controller and will visit every (x,y) point it recieves in the list every .02 seconds. The units for the (x,y) points are in meters and the spacing of the points determines the speed of the car. The vector going from a point to the next point in the list dictates the angle of the car. Acceleration both in the tangential and normal directions is measured along with the jerk, the rate of change of total Acceleration. The (x,y) point paths that the planner recieves should not have a total acceleration that goes over 10 m/s^2, also the jerk should not go over 50 m/s^3. (NOTE: As this is BETA, these requirements might change. Also currently jerk is over a .02 second interval, it would probably be better to average total acceleration over 1 second and measure jerk from that.)

2. There will be some latency between the simulator running and the path planner returning a path, with optimized code usually its not very long maybe just 1-3 time steps. During this delay the simulator will continue using points that it was last given, because of this its a good idea to store the last points you have used so you can have a smooth transition. previous_path_x, and previous_path_y can be helpful for this transition since they show the last points given to the simulator controller with the processed points already removed. You would either return a path that extends this previous path or make sure to create a new path that has a smooth transition with this last path.

## Tips

A really helpful resource for doing this project and creating smooth trajectories was using http://kluge.in-chemnitz.de/opensource/spline/, the spline function is in a single hearder file is really easy to use.

---

## Implementation Details

### Path Planning

In the project, the other cars' information can be obtained from sensor fusion; the highway map from "data/highway_map.txt"; and the ego car's information and previous path from the simulator. The essential part of the path planning is to compute a suitable and smooth path of the ego car.

The program creates a list of widely spaced (x,y) waypoints, evenly spaced at 30 meters, then interpolates these waypoints with a spline, and fill it in with more points that control speed. Some other interpolation methods could be used as well, like polynomials.

### Lane Change Behavior

The ego car should behaves like a man-driving car:

1. Reach to the target speed (50 MPH) as soon as possible;

2. If there is a car ahead, it needs to slows down;

3. If there is a car ahead, the ego car is in the middle lane or right lane and its adjacent left lane is available, make a lane change to its left;

4. If there is a car ahead, the ego car is in the middle lane or left lane and its adjacent right lane is available, make a lane change to its right.

In 3 and 4, the Frenet coordinates make it easy to find which lane the other cars are in and how far away they are. The criterion of the left/right lane being available is that there is no car's s value in the range of [-15, 30] of the ego's s value.

---

## Reflection

At the first glance, this project didn't seem very related to the lectures, and it was hard to get hands on the project. By going through the Q&A video, I was able to better understand the project and sail it through. There are some potential improvements for the project:

1. Some of the knowledge learned in the lectures are not applied in the project, like search, and prediction, etc.

2. The lane change behavior planning can be more granular. For example, there is no logic to force the ego car stay in a lane for at least some time before it can make a subsequent lane change; the car doesn't change to the right lane if there is no traffic ahead and it is safe to do so.
