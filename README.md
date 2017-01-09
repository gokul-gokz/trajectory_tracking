# trajectory_tracking
This is a ROS application to test trajectory tracking algorithms for mobile robots using Gazebo as simulator.
The robot that has been used for this project is [Turtlebot 2][1] which is an affordable mobile robot widely used for
research.

Algorithms that have been used and are working properly so far:
* Numerical method controller using Euler's approximation.
* PID controller

These algorithms have been tested, and are properly working for the following trajectories:
* Linear Trajectory
* Circular Trajectory
* Squared Trajectory

## Installation
Before cloning this repository, you should install ROS and Gazebo.
It is highly recommended to use Ubuntu as OS. I also suggest installing
Gazebo before ROS in order to avoid Gazebo version conflicts.

### Installing Gazebo
In order to install Gazebo, you can follow the instructions provided in
[this tutorial][9], or you can just execute this command line:

```
$ curl -ssL http://get.gazebosim.org | sh
```

### Installing ROS
The ROS version used in this project is `kinect`. In order to install
ROS, you can follow the instruction in [this tutorial][8], or you can
execute the following command lines:

```
$ sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
$ sudo apt-key adv --keyserver hkp://ha.pool.sks-keyservers.net:80 --recv-key 421C365BD9FF1F717815A3895523BAEEB01FA116
$ sudo apt-get update
$ sudo apt-get install ros-kinetic-desktop-full
$ sudo rosdep init
$ rosdep update
```

Finally you should source the enviroment variables to avoid doing it
every time you want to use ROS:

```
$ echo "source /opt/ros/kinetic/setup.bash" >> ~/.bashrc
$ source ~/.bashrc
```

You could optionally install `rosinstall` in order to download packages
easily:

```
$ sudo apt-get install python-rosinstall
```

### Installing Turtlebot package

Once ROS and Gazebo have been installed, you have to install the
Turtlebot package. Run the following command line:

```
$ sudo apt-get install ros-kinetic-turtlebot-gazebo
```

In order to make sure that the installation process was successful,
execute the following command line, which will open a Gazebo world
with some objects and a Turtlebot between them:

```
$ roslaunch turtlebot_gazebo turtlebot_world.launch
```

In case you get an error when executing the previous command, just
restart your computer and try again.

### Creating a workspace

Now, it is time to create a workspace, so you can create it in your
home directory by executing the following commands, in this case
the workspace will be named `turtlebot_ws`

```
$ mkdir -p ~/turtlebot_ws/src
$ cd ~/turtlebot_ws/src/
$ catkin_init_workspace
$ cd ~/turtlebot_ws/
$ catkin_make
```

Once the workspace has been created source it:

```
$ source ~/turtlebot_ws/devel/setup.bash
```

### Cloning repository

Now it is possible to clone this repository. Because this repository
is a ROS package, it should be cloned inside `~/turtlebot_ws/src/`:

```
$ cd ~/turtlebot_ws/src/
$ git clone https://github.com/bit0001/trajectory_tracking.git
```

After cloning the repo, you have to run `catkin_make` again:

```
$ cd ~/turtlebot/
$ catkin_make
```

Finally, this package is ready to be used.

## Usage
### Running a simulation
Open a terminal window, and you have to source the workspace, and execute
a launch file in order to initialize Gazebo:

```
$ source ~/turtlebot_ws/devel/setup.bash
$ roslaunch trajectory_tracking turtlebot_world.launch
```

This will open Gazebo in a world where a turtlebot is shown in the middle of a room.
Now, open a new terminal, source again the workspace, and run the file
`trajectory_tracking twist.py`:

```
$ source ~/turtlebot_ws/devel/setup.bash
$ rosrun trajectory_tracking twist.py cmd_vel:=cmd_vel_mux/input/teleop
```

Finally, open again a new terminal, source the workspace and run the file `control.py`:
```
$ source ~/turtlebot_ws/devel/setup.bash
$ rosrun trajectory_tracking control.py <controller_name> <trajectory_name> [simulation_time]
```

Where controller_name could be either `euler` or `pid`, and trajectory_name
could be either `linear`, `circular`, or `squared`.
The simulation_time is a positive number, and if it is not given, a
default time is used. Example:

```
$ rosrun trajectory_tracking control.py euler squared 60.0
```
The previous command uses the the euler method controller to follow
a squared trajectory during 60 seconds. Once an experiment is
completed, results are plotted and shown, and when plot windows are
closed, simulation data is stored in a database, which is stored in the
root directory of this project, creating a table which name follows the
following format: `controller_trajectory_YYYY_mm_dd_HH_MM_SS`.

### Plotting results of the last simulation
Once a simulation has been completed, it is possible to plot again the results obtained
in that simulation. In order to achive this, one has to run the module `plotter` and pass
the absolute path to the database `results.db` as an argument of the command-line:

```
$ cd ~/turtlebot_ws/src/trajectory_tracking/src/
$ python -m plotter /absolute/path/to/database/results.db
```

### Plotting results of a simulation specifying its name
It is also possible to plot again the results of a simulation by specifying its name:

```
$ cd ~/turtlebot_ws/src/trajectory_tracking/src/
$ python -m plotter /absolute/path/to/database/results.db simulation_name
```

In order to see the list of simulation names use the `--sims` flag:
```
$ cd ~/turtlebot_ws/src/trajectory_tracking/src/
$ python -m plotter /absolute/path/to/database/results.db --sims
```

### Comparing the results of two simulations
It is possible to compare the obtained results in two different simulations.
There is one mandatory requirement that is comparing two simulations of the
same trajectory, i.e., the controller can be different.

Although at first glance it seems that the time that both simulations
lasted should be the same, it is possible to force a comparison.

To compare two simulations use:
```
$ cd ~/turtlebot_ws/src/trajectory_tracking/src/
$ python -m plotter /absolute/path/to/database/results.db sim1_name sim2_name
```

To force the plot comparison of two simulations use:
```
$ cd ~/turtlebot_ws/src/trajectory_tracking/src/
$ python -m plotter /absolute/path/to/database/results.db sim1_name sim2_name --f
```


## Trajectories
Three trajectories have been used to test each controller.

#### Linear Trajectory
This trajectory has been defined by the following parametric equations:

<img src="images/equations/linear_trajectory.png" alt="Linear Trajectory" width="190">

where (x, y) represent the position of the robot at time t, v_x and v_y are the linear velocities for the x and y axes,
and x_0 and y_0 are the initial values for x and y positions.

#### Circular Trajectory
This trajectory has been defined by the following parametric equations:

<img src="images/equations/circular_trajectory.png" alt="Circular Trajectory" width="265">

where (x, y) represent the position of the robot at time t, v is the linear velocity of the robot,
and x_0 and y_0 are the initial values for x and y positions, R represents the radius of the circle, and T the time that
the robot takes in order to complete the circular trajectory.

#### Squared Trajectory
This trajectory has been defined by the following equations:

<img src="images/equations/squared_trajectory.png" alt="Squared Trajectory" width="550">

where (x, y) represent the position of the robot at time t, v is the linear velocity of the robot,
and x_0 and y_0 are the initial values for x and y positions, s represents the side of the square, and T the time that
the robot takes in order to complete the circular trajectory.


### Experiments and Results
The performance of both controllers was excellent, and this is shown in the obtained results.
For each experiment a video and its corresponding results are shown. In order to run the
simulation as fast as possible for Gazebo, the parameter **_real_time_update_rate_** has been
set to 0.00000 in **_worlds/room.world_**.

#### Euler Method Controller - Linear Trajectory

**Video:** [Euler: Linear Trajectory Test][2]

<img src="images/results/euler/linear/linear_trajectory_euler_x_y.png" alt="Results for x and y" width="1000">
<img src="images/results/euler/linear/linear_trajectory_euler_theta_trajectory.png" alt="Results for theta and trajectory" width="1000">


#### Euler Method Controller - Circular Trajectory

**Video:** [Euler: Circular Trajectory Test][3]

<img src="images/results/euler/circular/circular_trajectory_euler_x_y.png" alt="Results for x and y" width="1000">
<img src="images/results/euler/circular/circular_trajectory_euler_theta_trajectory.png" alt="Results for theta and trajectory" width="1000">

#### Euler Method Controller - Squared Trajectory

**Video:** [Euler: Squared Trajectory Test][4]

<img src="images/results/euler/squared/squared_trajectory_euler_x_y.png" alt="Results for x and y" width="1000">
<img src="images/results/euler/squared/squared_trajectory_euler_theta_trajectory.png" alt="Results for theta and trajectory" width="1000">

#### PID Controller

#### PID Controller - Linear Trajectory

**Video:** [PID: Linear Trajectory Test][5]

<img src="images/results/pid/linear/linear_trajectory_pid_x_y.png" alt="Results for x and y" width="1000">
<img src="images/results/pid/linear/linear_trajectory_pid_theta_trajectory.png" alt="Results for theta and trajectory" width="1000">

#### PID Controller - Circular Trajectory

**Video:** [PID: Circular Trajectory Test][6]

<img src="images/results/pid/circular/circular_trajectory_pid_x_y.png" alt="Results for x and y" width="1000">
<img src="images/results/pid/circular/circular_trajectory_pid_theta_trajectory.png" alt="Results for theta and trajectory" width="1000">

#### PID Controller - Squared Trajectory

**Video:** [PID: Squared Trajectory Test][7]

<img src="images/results/pid/squared/squared_trajectory_pid_x_y.png" alt="Results for x and y" width="1000">
<img src="images/results/pid/squared/squared_trajectory_pid_theta_trajectory.png" alt="Results for theta and trajectory" width="1000">

[1]: http://www.turtlebot.com/
[2]: https://www.youtube.com/watch?v=WLiILRsv9n4
[3]: https://www.youtube.com/watch?v=8eaEPQzavvk
[4]: https://www.youtube.com/watch?v=M4-E_W8IbYI
[5]: https://www.youtube.com/watch?v=3AXAvi5Tdq8
[6]: https://www.youtube.com/watch?v=ZDh1iNf1wvU
[7]: https://www.youtube.com/watch?v=nGBi5b4sPTA
[8]: http://wiki.ros.org/ROS/Installation
[9]: http://gazebosim.org/tutorials?cat=install
