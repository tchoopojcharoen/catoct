![alt text](https://github.com/[username]/[time_optimal_cubic_trajectory
]/blob/[main]/logo.jpg?raw=true)
# CATOCT: Continuous-Acceleraiton Time-Optimal Cubic Trajectory
This method is suitable for fully-actuated robotics manipulator.

## Objective:
Given a sequence of (multiple-degree-of-freedom) via points in joint space and limits on the magnitude of both velocity and acceleration of each degree of freedom, 
the goal is to generate a piece-wise cubic trajectory that is time-optimal and satisfies both velocity and acceleration constraints. The trajectory must be 
continuous in position, velocity, and acceleration.

## Why not quintic trajectory ?:
The advantage of using polynomial trajectory is its easily-computed derivatives based on its fixed structure of coefficients and the monomials. 
A cubic trajectory consists of 4 coefficients. Only 4 boundary conditions and the duration of thetrajectory are required to compute the coefficients. Typically, this is done by assigning 
the initial position, the final position, the intial velocity, and the final velocity. The resultant cubic trajectory can be appended togeter to form a 
piece-wise cubic trajectory. Since the velocity can be arbitarily assigned, one can form a piece-wise cubic trajectory that gaurantee to be continuous in its velocity.
However, if the duration is arbitary, one cannot gaurantee the continuity in the acceleration.

One simple way to solve this is to use a quintic trajectory, which has 6 coefficients. In addition to the given position and velocity of the boundaries, the accceleration can be assigned as well. 
The resultant piece-wise quintic trajectory can now be conitnuous in its position, velocity, and accceleration. This implies that, in order to generate a piece-wise quintic trajectory,
one needs the followings.
* durations of each sub-trajectory
* position at each via points (including the initial one)
* velocity at each via points (including the initial one)
* acceleration at each via points (including the initial one)
Given the position of the via points, there are several questions about the rest of the parameters.
* Can the durations be any values? Can they be too small or too large ?
* Can the velocity be any values? Is there any way we can compute this automatically?
* Can the acceleration be any values? Can the actuators achieve the given acceleration? What happens to the acceleration between the via points?

The problem is that, for the quintic trajectory, one has to determine many unknowns. A feasible trajectory does exist, but it might require extensive tuning.
This is why "CATOCT:Continuous-Acceleration Time-Optimal Cubic Trajectory" is developed.

## Ideas behind CATOCT:
In reality, a sequence of via points in joint space are the only things given to the trajectory generator. We must compute durations, via points' velocity, and acceleration automatically. Although we cannot assign any arbitary acceleration at the via points for a cubic trajectory, we can adjust the velocity at the via points so that the acceleration at the via points are continuous given the duration of each trajectory. In other words, we can solve for the velocity at each via points using the given durations. This actually results in a closed form solution.

Solving for the durations is a bit trickier. Theoretically, we can choose any arbitary durations and call it a day. However, if the durations are too large, the planned trajectory will take too long. But if it's too short, the trajectory may not be feasible for the actua physical system. This implies that there must be some physical limitations that we should impose on the durations. 

We approach this by using nonlinear parameteric optimization to solve for the shortest durations subject to the limited velocity and acceleration. Note that the limitations are only imposed at the via points. Since the acceleration is piece-wise linear, its extremas are at the via points, which implies that the acceleration will not violate the constraints at any time. However, the velocity is piece-wise parabolic. Therefore, some velocity violations may occur inbetween the via points. This is one of the down side of this approach. 

In reality, a robotics manipulator would start with zero acceleration. And it should also stop with zero-acceleration. Since the mentioned approach does not rely on the given acceleration, it cannot gaurantee zero acceleration at the boundaries (initial & terminal). To mitigate this problem, we can add auxilary via points in the middle of the first and the final sub-trajectories. The  
