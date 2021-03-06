# Robotics-EIE3
## Robotics Assignment 3:

### 2.1 Representing and Displaying Uncertain Motion with a Particle Set.

We have created a list of 100 lists representing each of the particles. Each list contains (x,y,theta) coordinates.

Each time the robot moves straight, the X coordinate is updated with: `X = X + (D+e)cos(theta)`, the Y coordinate with: `Y = Y + (D+f)sin(theta)` and theta with: `theta = theta + g` where `e`, `f` and `g` are error terms created from a random sample of a Gaussian distribution with a mean of 0 (as on average the robot should end up in the same place) and standard deviation of 1/16 as we found this value to closest reflect the deviation of +/- 3cm we are experiencing.

In each turn we update all the particles with new theta values as : `theta = theta+g+angle`


### 2.2 Waypoint Navigation
Now we had the array set up containing all the projected points of the robot based on it's probable deviation from the prescribed path, it would be useful to have a function which will allow us to move to a particular point in 2D space. The robot would automatically adjusting it's position based on how far it expects to deviate from where it thinks it is. To do this we have implemented a function `navigate_to_waypoint` which takes an X and Y point as arguments (in meters). The robot looks at where it thinks it is based upon it's mean position, and calculates how far it needs to travel in order to reach the point assigned to it. To make the most direct route we turn by calling `set_robot_pose` which changes the angle of the robot based upon a starting reference of 0. The robot can then travel the hypotenuse of the `X` and `Y` values to allow it to reach the prescribed point (via the shortest route possible).

We managed to implement this correctly and have a video of the robot travelling to (0.5, 0), (0, 0.5) and then returning to (0,0).
[Link](https://www.youtube.com/watch?v=JyegLMHnY8Q)


### 2.3 Sonar investigation

#### _1:_ When placed facing and perpendicular to a smooth surface such as a wall, what are the minimum and maximum depths that the sensor can reliably measure?

The sensor is an analogue sensor which sends out a short burst of ultrasonic sound. The sensor then listens for this pulse to return and based upon the time it takes, the distance can be calculated as the speed of sound is already known. It then returns the distance to an object as a byte, expressed in cm. The value of 255 has a special meaning, it indicates there is no object within the maximum measuring range. The resolution of the sensor is 1 cm.

In theory its minimum range is 0 cm and its maximum range is 254 cm. In reality the minimum range is about 7 cm. The maximum range depends on the object to be detected, large and hard objects can be detected over a longer range than small and soft objects. False echoes can limit the practical range even further. A wall (large and hard) can often be detected if it is within a range of two metres, but for a reliable signal it has to be within a range of 1.6 metres.

#### _2:_ Move the sonar so that it faces the wall at a non-orthogonal incidence angle. What is the maximum angular deviation from perpendicular to the wall at which it will still give sensible readings?

As a rule of thumb one can assume that objects that are within an angle of 15 degrees to the left or right are detected. The total width of the detection area is about 30 degrees. When using the sensor for obstacle avoidance this wide beam is advantageous. When the sensor is used for mapping it is a drawback as objects seem wider then they really are. They also seem arc shaped, the arc having an angle of at least 30 degrees for small objects and more for wider objects.

#### _3:_ Do your sonar depth measurements have any systematic (non-zero mean) errors? To test this, set up the sensor at a range of hand-measured depths (20cm, 40cm, 60cm, 80cm, 100cm) from a wall and record depth readings. Are they consistently above or below what they should be?

We measured each distance 10 times and these are the results:

|  Distance | Mean |
|:-------:|:-------:|
|20       |22       |
|40       |41       |
|60       |60       |
|80       |79.7     |
|100      |101      |

The 10 consecutive measurements had variance and standard deviation of 0, meaning they did not change.

We observed a deviation of approximately +/- 2cm which almost agrees with the datasheet of the ultrasonic sensor. The datasheet suggests values up to +/-3 cm.

#### _4:_ What is the the accuracy of the sonar sensor and does it depend on depth? At each of two chosen hand-measured depths (40cm and 100cm), make 10 separate depth measurements (each time picking up and replacing the sensor) and record the values. Do you observe the same level of scatter in each case?

We started with the distance set to 40 cm. We took 10 measurements and recorded the mean, variance and standard deviation.

| Trial | Mean | Variance | Standard deviation |
|:-------:|:-------:|:-------:|:-----:|
|1   |43    |0 | 0|
|2   |43    |0 | 0|
|3   |43    |0 | 0|
|4   |42    |0 | 0|
|5   |42    |0 | 0|
|6   |42    |0 | 0|
|7   |42    |0 | 0|
|8   |42    |0 | 0|
|9   |42    |0 | 0|
|10  |42    |0 | 0|

Then we measured scatter with the distance of 100 cm.

| Trial | Mean | Variance | Standard deviation |
|:-------:|:-------:|:-------:|:-----:|
|1   |103.8  |0.16 | 0.4|
|2   |104    |0    | 0|
|3   |104    |0    | 0|
|4   |104    |0    | 0|
|5   |104    |0    | 0|
|6   |104    |0    | 0|
|7   |104    |0    | 0|
|8   |104    |0    | 0|
|9   |104    |0    | 0|
|10  |104    |0    | 0|

In the second case we saw double the error for the shorter distance. Therefore we assume that the depth definitely matters and the error increases proportionally to the depth being measured.

#### _5:_ In a range of general conditions for robot navigation, what fraction of the time do you think your sonar gives garbage readings very far from ground truth?

We have made 1000 consecutive measurements and we have observed that when the sensor is still the error is almost zero. However when we are moving the robot we have observed that approximately 1/20 to 1/15 of all the measurements are wrong.
