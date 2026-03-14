# Arduinobot ROS 2 Practice Workspace

This repository is my hands-on practice workspace for the Udemy course:
**Robotics and ROS 2 - Learn by Doing! Manipulators**

Course link: https://www.udemy.com/course/robotics-and-ros-2-learn-by-doing-manipulators/

---

## Learning Journey (Step by Step)

I am documenting my progress in small, practical milestones.

- [x] **Step 1** — Simple publisher and subscriber in C++ and Python
- [x] **Step 2** — Robot model in URDF/Xacro (`arduinobot_description`)
- [x] **Step 3** — Simple parameter in C++ and Python
- [x] **Step 4** — RViz visualization with a custom launch file

---

## Workspace Structure

- `src/arduinobot_cpp` → C++ ROS 2 examples (`arduinobot_cpp_examples`)
- `src/arduinobot_py` → Python ROS 2 examples (`arduinobot_py_examples`)

---

## Step 1 — Simple Publisher and Subscriber (`arduinobot_cpp` and `arduinobot_py`)

### Goal
Understand how to create and run basic ROS 2 publisher/subscriber nodes in both C++ and Python.

### What I used
- **C++ package:** `arduinobot_cpp_examples`
  - Executables: `simple_publisher`, `simple_subscriber`
  - Sources:
    - `src/arduinobot_cpp/src/simple_publisher.cpp`
    - `src/arduinobot_cpp/src/simple_subscriber.cpp`
- **Python package:** `arduinobot_py_examples`
  - Entry points: `simple_publisher`, `simple_subscriber`
  - Sources:
    - `src/arduinobot_py/arduinobot_py_examples/simple_publisher.py`
    - `src/arduinobot_py/arduinobot_py_examples/simple_subscriber.py`

### Build
From workspace root:

```bash
cd /home/nima/arduinobot_ws
colcon build
```

### Source environment

```bash
source /opt/ros/$ROS_DISTRO/setup.bash
source /home/nima/arduinobot_ws/install/setup.bash
```

### Run the C++ publisher/subscriber (in two terminals)

Terminal 1:

```bash
ros2 run arduinobot_cpp_examples simple_subscriber
```

Terminal 2:

```bash
ros2 run arduinobot_cpp_examples simple_publisher
```

### Run the Python publisher/subscriber (in two terminals)

Terminal 1:

```bash
ros2 run arduinobot_py_examples simple_subscriber
```

Terminal 2:

```bash
ros2 run arduinobot_py_examples simple_publisher
```

### Verify topic output
In another terminal:

```bash
source /opt/ros/$ROS_DISTRO/setup.bash
source /home/nima/arduinobot_ws/install/setup.bash
ros2 topic list
ros2 topic echo /chatter
```

### What I learned in Step 1
- How ROS 2 publisher/subscriber nodes are structured in C++ (`rclcpp`) and Python (`rclpy`)
- How to build packages with `colcon build`
- How to run nodes with `ros2 run`
- How to inspect topic communication using `ros2 topic` tools

### Notes / Reflection
- C++ and Python implementations follow the same ROS 2 publish/subscribe communication concept.
- The main difference is syntax and package setup (`CMakeLists.txt` vs `setup.py`).

---

## Step 2 — Robot Description in URDF/Xacro (`arduinobot_description`)

### Goal
Create the Arduinobot kinematic model using URDF/Xacro so it can be visualized and used in simulation/planning workflows.

### What I used
- **Description package:** `arduinobot_description`
- **Main model file:** `src/arduinobot_description/urdf/arduinobot.urdf.xacro`
- **Meshes:** `src/arduinobot_description/meshes/*.STL`

### Model highlights
- Defined reusable Xacro properties (`PI`, `effort`, `velocity`)
- Added links: `world`, `base_link`, `base_plate`, `forward_drive_arm`, `horizontal_arm`, `claw_support`, `gripper_right`, `gripper_left`
- Added joints: one fixed world joint, revolute arm joints (`joint_1` to `joint_3`), fixed wrist support joint, and gripper joints (`joint_4`, `joint_5`)
- Added gripper mimic behavior with `<mimic joint="joint_4" multiplier="-1"/>`

### Build
From workspace root:

```bash
cd /home/nima/arduinobot_ws
colcon build
```

### Source environment

```bash
source /opt/ros/$ROS_DISTRO/setup.bash
source /home/nima/arduinobot_ws/install/setup.bash
```

### Visualize the URDF/Xacro model

```bash
ros2 launch urdf_tutorial display.launch.py model:=/home/nima/arduinobot_ws/src/arduinobot_description/urdf/arduinobot.urdf.xacro
```

### What I learned in Step 2
- How to structure a robot model with links and joints in URDF/Xacro
- How to use Xacro properties to keep limits and constants maintainable
- How mimic joints simplify synchronized gripper motion

---

## Step 3 — Simple Parameter (`arduinobot_cpp` and `arduinobot_py`)

### Goal
Understand how to declare and update ROS 2 node parameters in both C++ and Python.

### What I used
- **C++ package:** `arduinobot_cpp_examples`
  - Executable: `simple_parameter`
  - Source: `src/arduinobot_cpp/src/simple_parameter.cpp`
- **Python package:** `arduinobot_py_examples`
  - Entry point: `simple_parameter`
  - Source: `src/arduinobot_py/arduinobot_py_examples/simple_parameter.py`

### Build
From workspace root:

```bash
cd /home/nima/arduinobot_ws
colcon build
```

### Source environment

```bash
source /opt/ros/$ROS_DISTRO/setup.bash
source /home/nima/arduinobot_ws/install/setup.bash
```

### Run the C++ simple parameter node

```bash
ros2 run arduinobot_cpp_examples simple_parameter
```

### Run the Python simple parameter node

```bash
ros2 run arduinobot_py_examples simple_parameter
```

### Verify and change parameters
Run these commands while one `simple_parameter` node is active:

```bash
ros2 param list /simple_parameter
ros2 param get /simple_parameter simple_int_param
ros2 param set /simple_parameter simple_int_param 42
ros2 param set /simple_parameter simple_string_param "Nima"
```

### What I learned in Step 3
- How to declare parameters with default values in C++ and Python
- How to react to runtime parameter updates via callbacks
- How to inspect and modify parameters using `ros2 param` CLI

---

## Step 4 — RViz Visualization with a Custom Launch File (`arduinobot_description`)

### Goal
Replace the external `urdf_tutorial` launcher with a custom ROS 2 launch file that starts `robot_state_publisher`, `joint_state_publisher_gui`, and RViz2 with a pre-configured display, all in one command.

### What I used
- **Package:** `arduinobot_description`
- **Launch file:** `src/arduinobot_description/launch/display.launch.py`
- **RViz config:** `src/arduinobot_description/rviz/display.rviz`

### What the launch file does
It starts three nodes:
1. `robot_state_publisher` — parses the Xacro model and publishes `/robot_description` and TF transforms
2. `joint_state_publisher_gui` — provides an interactive GUI to move each joint
3. `rviz2` — opens RViz with the saved `display.rviz` config (robot model + TF visualised)

The `model` argument defaults to the installed Xacro file but can be overridden at launch time.

### CMakeLists.txt changes
The `launch` and `rviz` directories are now installed alongside `urdf` and `meshes`:
```cmake
install(
  DIRECTORY urdf meshes launch rviz
  DESTINATION share/${PROJECT_NAME}
)
```

### Build
```bash
cd /home/nima/arduinobot_ws
colcon build
```

### Source environment
```bash
source /opt/ros/$ROS_DISTRO/setup.bash
source /home/nima/arduinobot_ws/install/setup.bash
```

### Launch
```bash
ros2 launch arduinobot_description display.launch.py
```

To visualize a different model:
```bash
ros2 launch arduinobot_description display.launch.py model:=/path/to/your.urdf.xacro
```

### What I learned in Step 4
- How to write a ROS 2 Python launch file from scratch
- How to use `DeclareLaunchArgument` and `LaunchConfiguration` for flexible, overridable arguments
- How to start `robot_state_publisher` programmatically with a Xacro-processed description
- How to use `joint_state_publisher_gui` to interactively control joints
- How to launch RViz2 with a saved configuration file
- How to install `launch` and `rviz` directories via `CMakeLists.txt`
