# Control-and-Trajectory-Tracking-for-Autonomous-Vehicle


# Proportional-Integral-Derivative (PID)

In this project, I have applied the skills acquired in this course to design a PID controller to perform vehicle trajectory tracking. Given a trajectory as an array of locations, and a simulation environment, I have designed and code a PID controller and test its efficiency on the CARLA simulator used in the industry.

## Installation

Run the following commands to install the starter code in the Udacity Workspace:

Clone the <a href="https://github.com/udacity/nd013-c6-control-starter/tree/master" target="_blank">repository</a>:

`git clone https://github.com/udacity/nd013-c6-control-starter.git`

### Run Carla Simulator

Open new window

* `su - student`
// Will say permission denied, ignore and continue
* `cd /opt/carla-simulator/`
* `SDL_VIDEODRIVER=offscreen ./CarlaUE4.sh -opengl`

### Compile and Run the Controller

Open new window

* `cd nd013-c6-control-starter/project`
* `./install-ubuntu.sh`
* `cd pid_controller/`
* `rm -rf rpclib`
* `git clone https://github.com/rpclib/rpclib.git`
* `cmake .`
* `make` (This last command compiles your c++ code, run it after every change in your code)

### Testing

To test your installation run the following commands.

* `cd nd013-c6-control-starter/project`
* `./run_main_pid.sh`
This will silently fail `ctrl + C` to stop
* `./run_main_pid.sh` (again)
Go to desktop mode to see CARLA

If error bind is already in use, or address already being used

* `ps -aux | grep carla`
* `kill id`


## Project Instructions

In the previous project you built a path planner for the autonomous vehicle. Now you will build the steer and throttle controller so that the car follows the trajectory.

You will design and run the a PID controller as described in the previous course.

In the directory [/pid_controller](https://github.com/udacity/nd013-c6-control-starter/tree/master/project/pid_controller)  you will find the files [pid_controller.cpp](https://github.com/udacity/nd013-c6-control-starter/blob/master/project/pid_controller/pid_controller.cpp)  and [pid_controller.h](https://github.com/udacity/nd013-c6-control-starter/blob/master/project/pid_controller/pid_controller.h). This is where you will code your pid controller.
The function pid is called in [main.cpp](https://github.com/udacity/nd013-c6-control-starter/blob/master/project/pid_controller/main.cpp).

### Step 1: Build the PID controller object
Complete the TODO in the [pid_controller.h](https://github.com/udacity/nd013-c6-control-starter/blob/master/project/pid_controller/pid_controller.h) and [pid_controller.cpp](https://github.com/udacity/nd013-c6-control-starter/blob/master/project/pid_controller/pid_controller.cpp).

Run the simulator and see in the desktop mode the car in the CARLA simulator. Take a screenshot and add it to your report. The car should not move in the simulation.
### Step 2: PID controller for throttle:
1) In [main.cpp](https://github.com/udacity/nd013-c6-control-starter/blob/master/project/pid_controller/main.cpp), complete the TODO (step 2) to compute the error for the throttle pid. The error is the speed difference between the actual speed and the desired speed.

Useful variables:
- The last point of **v_points** vector contains the velocity computed by the path planner.
- **velocity** contains the actual velocity.
- The output of the controller should be inside [-1, 1].

2) Comment your code to explain why did you computed the error this way.

3) Tune the parameters of the pid until you get satisfying results (a perfect trajectory is not expected).

### Step 3: PID controller for steer:
1) In [main.cpp](https://github.com/udacity/nd013-c6-control-starter/blob/master/project/pid_controller/main.cpp), complete the TODO (step 3) to compute the error for the steer pid. The error is the angle difference between the actual steer and the desired steer to reach the planned position.

Useful variables:
- The variable **y_points** and **x_point** gives the desired trajectory planned by the path_planner.
- **yaw** gives the actual rotational angle of the car.
- The output of the controller should be inside [-1.2, 1.2].
- If needed, the position of the car is stored in the variables **x_position**, **y_position** and **z_position**

2) Comment your code to explain why did you computed the error this way.

3) Tune the parameters of the pid until you get satisfying results (a perfect trajectory is not expected).

### Step 4: Evaluate the PID efficiency
The values of the error and the pid command are saved in thottle_data.txt and steer_data.txt.
Plot the saved values using the command (in nd013-c6-control-refresh/project):

```
python3 plot_pid.py
```

You might need to install a few additional python modules: 

```
pip3 install pandas
pip3 install matplotlib
```

### Tips:
- When you wil be testing your c++ code, restart the Carla simulator to remove the former car from the simulation.
- If the simulation freezes on the desktop mode but is still running on the terminal, close the desktop and restart it.
- When you will be tuning the PID parameters, try between those values:

## Project Report:

The image below shows the ego car in the CARLA simulator at the starting point.
<img src="/img/1.PNG"/>





### What is the effect of the PID according to the plots, how each part of the PID affects the control command?

| Plot: PID Steering Errors               | Plot: PID Throttle Errors               |
|:---------------------------------------:|:---------------------------------------:|
| <img src="/img/2.PNG"/> | <img src="/img/3.PNG"/> |

**Steering plot**: In the beginning, an oscillation with significant steering errors is kept along the ride by the iterations. The peaks are avoided obstacles on the road while always recovering to keep on track.

**Throttle plot**: In the beginning, a large oscillation with a significant throttle error due to the rapid starting acceleration decreases along the iterations to a smoother speed for safer obstacle avoidance.

PID coeffitients: **Kp**, **Kd**, **Ki**.
**Kp** directly affected the error. High values apply a significant change and make the car oscillate when close to the desired path. The low value may cause the vehicle to run smoother, but it is hard to steer or throttle.
**Kd** applies a counterforce opposite to the oscillations. This item is produced based on the error gradient, not the error itself. So Kd will flatten the error into a horizontal line.
**Ki** increases with iterations and helps to reduce the error to zero. A high value will make the error smaller. When the error is stable, ki is needed to delete the error. 

### How would you design a way to automatically tune the PID parameters?
The Twiddle function is a way to tune the PID parameter automatically. Twiddle might be dangerous in a real-world self-driving vehicle application. Therefore, the traditional method is to measure the reaction signal of the hardware and calculate the mathematical representation to get a similar output signal, then tune a model.

### PID controller is a model free controller, i.e. it does not use a model of the car. Could you explain the pros and cons of this type of controller?

**Pros**: 
1. Not complex and simple to understand.
2. Car boundries are not considered.
3. Applicable to autonomous systems and vehicles.

**Cons**: 
1. The differential and integral factors make it non-linear for the changes.
2. One parameter affects the final result by a change in the other parameter.
