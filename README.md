# Arduinobot ROS 2 Practice Workspace

This repository is my hands-on practice workspace for the Udemy course:
**Robotics and ROS 2 - Learn by Doing! Manipulators**

Course link: https://www.udemy.com/course/robotics-and-ros-2-learn-by-doing-manipulators/

---

## Learning Journey (Step by Step)

I am documenting my progress in small, practical milestones.

- [x] **Step 1** — Simple publishers in C++ and Python
- [x] **Step 2** — Robot model in URDF/Xacro (`arduinobot_description`)
- [ ] **Step 3** — _(to be added)_
- [ ] **Step 4** — _(to be added)_

---

## Workspace Structure

- `src/arduinobot_cpp` → C++ ROS 2 examples (`arduinobot_cpp_examples`)
- `src/arduinobot_py` → Python ROS 2 examples (`arduinobot_py_examples`)

---

## Step 1 — Simple Publishers (`arduinobot_cpp` and `arduinobot_py`)

### Goal
Understand how to create and run a basic ROS 2 publisher node in both C++ and Python.

### What I used
- **C++ package:** `arduinobot_cpp_examples`
  - Executable: `simple_publisher`
  - Source: `src/arduinobot_cpp/src/simple_publisher.cpp`
- **Python package:** `arduinobot_py_examples`
  - Entry point: `simple_publisher`
  - Source: `src/arduinobot_py/arduinobot_py_examples/simple_publisher.py`

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

### Run the C++ simple publisher

```bash
ros2 run arduinobot_cpp_examples simple_publisher
```

### Run the Python simple publisher

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
- How ROS 2 publisher nodes are structured in C++ (`rclcpp`) and Python (`rclpy`)
- How to build packages with `colcon build`
- How to run nodes with `ros2 run`
- How to inspect topic output using `ros2 topic` tools

### Notes / Reflection
- C++ and Python implementations follow the same ROS 2 communication concept.
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
