# nav_in_flatland

# What is this repository for?
Train DRL agents on ROS compatible simulations for autonomous navigation in highly dynamic environments. Flatland-DRL integration is based on Ronja Gueldenring's repo: drl_local_planner_ros_stable_baselines. Following features are included:

* Setup to train a local planner with reinforcement learning approaches from [stable baselines](https://github.com/hill-a/stable-baselines)

* Training in a simulator fusion of [Flatland](https://github.com/avidbots/flatland) and [pedsim_ros](https://github.com/srl-freiburg/pedsim_ros)

* Local planner has been trained on static and dynamic obstacles: [video](https://www.youtube.com/watch?v=nHvpO0hVnAg)

* Combination with arena2d levels for highly randomized training and better generalization

### Documentation ###
* How to use flatland: http://flatland-simulator.readthedocs.io
* 

## Installation
0. Standard ROS setup (Code has been tested with ROS-melodic on Ubuntu 18.04) with catkin_ws
Install ROS Melodic
```
webpage
```
Install additional ros pkgs 
```
sudo apt-get update && sudo apt-get install -y \
libqt4-dev \
libopencv-dev \
liblua5.2-dev \
screen \
python3-catkin-pkg-modules \
python3-rospkg-modules \
python3-empy
```

1. Create a catkin_ws and clone this repo into your catkin_ws 
````
cd $HOME
mkdir -p catkin_ws/src && cd catkin_ws/src
git clone https://github.com/ignc-research/arena-rosnav
cd arena-rosnav && rosws update
./geometry2_install.sh
source $HOME/.zshrc
cd ../.. 
catkin_make -DPYTHON_EXECUTABLE=/usr/bin/python3
source devel/setup.zsh
````
2. To be able to use python3 with ROS, you need an virtual environment. We recommend using venv. Install virtual environment and wrapper (as root or admin! with sudo) on your local pc (without conda activated, deactivate conda env. if you have one active)
```
sudo pip3 install --upgrade pip
sudo pip3 install virtualenv
sudo pip3 install virtualenvwrapper
which virtualenv   # should output /usr/local/bin/virtualenv  
```
      
3. Create venv folder inside your home directory
```
cd $HOME
mkdir python_env   # create a venv folder in your home directory 
```

4. Add exports into your .zshrc (if you use bash change the last line to bashrc instead of zshrc):
```
echo "export WORKON_HOME=/home/linh/python_env   #path to your venv folder
export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3   #path to your python3 
export VIRTUALENVWRAPPER_VIRTUALENV=/usr/local/bin/virtualenv
source /usr/local/bin/virtualenvwrapper.sh" >> ~/.zshrc
```
5. Create a new venv
```
mkvirtualenv --python=python3.6 rosnav
workon rosnav
```

6. Install packages inside your venv (venv always activated!):
```
pip install --extra-index-url https://rospypi.github.io/simple/ rospy rosbag tf tf2_ros --ignore-installed
pip install pyyaml catkin_pkg netifaces
```     

Install stable_baselines3 for training DRL into your venv (venv always activated!)
```
cd $HOME/catkin_ws/src/forks/stable-baselines3
pip install -e .
```

## Usage
Before you test out the packages, always source your setup.zsh /setup.bash inside your catkin workspace also source your $HOME/.zshrc:
```
cd $HOME/catkin_ws
source devel/setup.zsh
source $HOME/.zshrc
```
### quick start simulation env and launch
````
roslaunch flatland_bringup start_flatland.launch  train_mode:=false
````
### quick test with the training 
In one terminnal
```bash
roslaunch flatland_bringup start_flatland.launch  train_mode:=true
```
In another terminal
```
roscd plan_local_drl
python scripts/training/training_example.py
```
Hint: During 2021-01-05 and 2021-01-10, plan_local_drl package is still under the development, which means the api of the class could be drastically changed. Sorry about the inconvinience!

###  start plan manager with FSM
````
rosrun plan_manage plan_manager_node
````

### use task generator to spawn random obstacles
````
roslaunch flatland_bringup start_flatland.launch  train_mode:=false
rosrun task_generator task_generator_node.py 
````
Now you can manually generate new tasks using the Pluggin inside RVIZ "Generate Task". You should set a "Flatland Goal" with the button and afterwards a "2D navigation goal". Afterwards the robot will automatically move once you spawn a new tasks by clicking the "Generate Task" button.

### use flatland_gym_env
````
roslaunch flatland_bringup start_flatland.launch  train_mode:=true

rosrun plan_local_drl flatland_gym_env.py

````


### Structure of the packges
1. flatland bringup: final launch file
2. nav: 
   1. plan_global
   2. plan_local
   3. plan_manage
   4. plan_manage_move_base
   5. plan_msgs
3. simulator_setup
   1. maps
   2. obstacles
   3. robot
4. task_generator
5. utils
   1. rviz_plugin