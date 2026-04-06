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
- [x] **Step 5** — Add RGB camera link/joint to the URDF model
- [x] **Step 6** — Add collision/inertial properties to URDF and launch robot in Gazebo (Gz Sim)
- [x] **Step 7** — Simulate an RGB camera sensor in Gazebo and stream images to ROS 2
- [x] **Step 8** — Integrate `ros2_control` and control joints from slider GUI in simulation

---

## Workspace Structure

- `src/arduinobot_cpp` → C++ ROS 2 examples (`arduinobot_cpp_examples`)
- `src/arduinobot_py` → Python ROS 2 examples (`arduinobot_py_examples`)
- `src/arduinobot_description` → Robot URDF/Xacro, Gazebo, and RViz resources
- `src/arduinobot_controller` → ROS 2 control setup, controllers config, and slider bridge nodes

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

---

## Step 5 — Assignment: Add RGB Camera to URDF (`arduinobot_description`)

### Goal
Extend the robot model by adding an RGB camera as a new link and connecting it with a new joint.

### Prerequisite check
Before starting, validate the existing model is working:

```bash
source /opt/ros/$ROS_DISTRO/setup.bash
source /home/nima/arduinobot_ws/install/setup.bash
ros2 launch arduinobot_description display.launch.py
```

### Configuration
- Added mesh: `src/arduinobot_description/meshes/pi_camera.STL`

### Assignment summary
- Added new link: `rgb_camera` using `pi_camera.STL`
- Added new joint: `rgb_camera_joint` connecting `base_link` to `rgb_camera`
- Used a **fixed** joint (camera is rigidly attached to the robot structure)
- Set camera link transform from `base_link` to approximately:
  - `xyz="0 0.40 0.18"`
  - `rpy="0 1.0 1.57"`
- Tuned the mesh pose inside the `rgb_camera` visual to center the camera optic:
  - `<origin rpy="0 0 -${PI / 2}" xyz="-0.135 0.12 0"/>`

### Files updated in this step
- `src/arduinobot_description/urdf/arduinobot.urdf.xacro`
- `src/arduinobot_description/meshes/pi_camera.STL`

### What I learned in Step 5
- How to extend a URDF model with additional sensor links
- When to use a fixed joint for mounted sensors
- How to iteratively tune `<origin>` values for correct mesh alignment in RViz2

---

## Step 6 — Gazebo Simulation with Collision & Inertial Properties (`arduinobot_description`)

### Goal
Prepare the robot model for physics simulation by adding `<collision>` and `<inertial>` elements to every URDF link, then launch the robot inside Gazebo (Gz Sim) using a new launch file.

### What I used
- **Package:** `arduinobot_description`
- **URDF/Xacro model:** `src/arduinobot_description/urdf/arduinobot.urdf.xacro`
- **New launch file:** `src/arduinobot_description/launch/gazebo.launch.py`

### URDF changes
- Added a reusable `default_inertial` Xacro macro that attaches a `<inertial>` block (mass + diagonal inertia tensor) to any link:
  ```xml
  <xacro:macro name="default_inertial" params="mass">
      <inertial>
          <mass value="${mass}"/>
          <inertia ixx="0.1" ixy="0.0" ixz="0.0" iyy="0.1" iyz="0.0" izz="0.1"/>
      </inertial>
  </xacro:macro>
  ```
- Applied `<xacro:default_inertial mass="..."/>` to every link (`base_link`, `base_plate`, `forward_drive_arm`, `horizontal_arm`, `claw_support`, `gripper_right`, `gripper_left`, `rgb_camera`)
- Added `<collision>` elements to every link, mirroring the visual geometry and origin

### Launch file (`gazebo.launch.py`)
The new launch file starts four processes:
1. `robot_state_publisher` — publishes robot description with `use_sim_time: True`
2. `gz_sim` (via `ros_gz_sim/launch/gz_sim.launch.py`) — starts Gazebo with an empty world (`-r empty.sdf`)
3. `ros_gz_sim create` — spawns the robot into the world by subscribing to the `/robot_description` topic
4. `ros_gz_bridge parameter_bridge` — bridges the `/clock` topic from Gazebo to ROS 2 (`gz.msgs.Clock` → `rosgraph_msgs/msg/Clock`)

It also sets the `GZ_SIM_RESOURCE_PATH` environment variable so Gazebo can find the package's meshes.

### `package.xml` changes
Added two new exec dependencies:
```xml
<exec_depend>ros_gz_sim</exec_depend>
<exec_depend>ros_gz_bridge</exec_depend>
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
ros2 launch arduinobot_description gazebo.launch.py
```

### What I learned in Step 6
- Why `<collision>` and `<inertial>` elements are required for physics simulation in Gazebo
- How to write a reusable Xacro macro for inertial properties
- How to write a ROS 2 launch file that starts Gz Sim, spawns a robot from a topic, and bridges clock
- How `GZ_SIM_RESOURCE_PATH` is used to help Gazebo locate mesh files
- How `ros_gz_bridge` maps Gazebo Transport message types to ROS 2 message types

---

## Step 7 — Simulate RGB Camera Sensor in Gazebo (`arduinobot_description`)

### Goal
Activate Gazebo's camera sensor simulation on the `rgb_camera` link so it publishes a live video stream that can be consumed by ROS 2 nodes and visualized in RViz2.

### What I used
- **Package:** `arduinobot_description`
- **URDF/Xacro model:** `src/arduinobot_description/urdf/arduinobot.urdf.xacro`
- **Launch files:** `src/arduinobot_description/launch/gazebo.launch.py`, `src/arduinobot_description/launch/display.launch.py`

### URDF changes

**Sensors system plugin** — a world-level `<gazebo>` block loads the Gz Sim sensors system so Gazebo actually renders and publishes sensor data:
```xml
<gazebo>
    <plugin filename="gz-sim-sensors-system" name="gz::sim::systems::Sensors">
        <render_engine>ogre2</render_engine>
    </plugin>
</gazebo>
```

**Camera sensor** — a link-level `<gazebo reference="rgb_camera">` block configures the simulated Raspberry Pi Camera 3:
```xml
<gazebo reference="rgb_camera">
    <sensor name="camera" type="camera">
        <camera name="camera">
            <horizontal_fov>1.15</horizontal_fov>
            <image>
                <width>2304</width>
                <height>1296</height>
                <format>RGB_INT8</format>
            </image>
            <clip>
                <near>0.1</near>
                <far>100</far>
            </clip>
        </camera>
        <always_on>1</always_on>
        <update_rate>30</update_rate>
        <visualize>true</visualize>
        <topic>image_raw</topic>
        <gz_frame_id>/rgb_camera</gz_frame_id>
    </sensor>
</gazebo>
```

Camera parameters match the real Raspberry Pi Camera 3 hardware:
| Parameter | Value |
|---|---|
| Resolution | 2304 × 1296 |
| FPS | 30 |
| Horizontal FoV | 1.15 rad (66°) |

### `gazebo.launch.py` changes
Two additional topics bridged from Gazebo Transport to ROS 2:
```python
gz_ros2_bridge = Node(
    package = "ros_gz_bridge",
    executable="parameter_bridge",
    arguments=[
        "/clock@rosgraph_msgs/msg/Clock[gz.msgs.Clock",
        "/image_raw@sensor_msgs/msg/Image[gz.msgs.Image",
        "/camera_info@sensor_msgs/msg/CameraInfo[gz.msgs.CameraInfo",
    ]
)
```

### `display.launch.py` changes
RViz2 is now started with `use_sim_time: True` so its clock stays in sync with Gazebo:
```python
rviz_node = Node(
    package="rviz2",
    executable="rviz2",
    name="rviz2",
    output="screen",
    arguments=["-d", os.path.join(...)],
    parameters=[{"use_sim_time": True}]
)
```

### Build
```bash
cd ~/arduinobot_ws
colcon build --packages-select arduinobot_description
```

### Source environment
```bash
source /opt/ros/$ROS_DISTRO/setup.bash
source ~/arduinobot_ws/install/setup.bash
```

### Launch Gazebo simulation
```bash
ros2 launch arduinobot_description gazebo.launch.py
```

### Visualize the camera stream in RViz2
In a second terminal:
```bash
ros2 launch arduinobot_description display.launch.py
```
Inside RViz2:
1. Set `Fixed Frame` → `base_link`
2. Click **Add** → **By topic** → `/image_raw` → **Image** → **OK**

Verify the topic is streaming at ~30 Hz:
```bash
ros2 topic hz /image_raw
```

### What I learned in Step 7
- How Gazebo's `gz-sim-sensors-system` plugin enables actual sensor simulation (without it sensors produce no output)
- How to configure a simulated camera with a specific resolution, FoV, FPS, and topic name using the `<gazebo>` and `<sensor>` URDF tags
- How to bridge `sensor_msgs/Image` and `sensor_msgs/CameraInfo` from Gazebo Transport to ROS 2 using `ros_gz_bridge`
- Why `use_sim_time: True` must be set on RViz2 when running alongside a Gazebo simulation
- How to add an Image display in RViz2 and subscribe to a camera topic

---

## Step 8 — ROS 2 Control + Slider Joint Control in Simulation (`arduinobot_controller`)

### Goal
Integrate `ros2_control` into the robot model and command the arm/gripper joints in Gazebo using a slider GUI.

### What I used
- **New controller package:** `arduinobot_controller`
  - Launch files:
    - `src/arduinobot_controller/launch/controller.launch.py`
    - `src/arduinobot_controller/launch/slider_controller.launch.py`
  - Controllers config:
    - `src/arduinobot_controller/config/arduinobot_controllers.yaml`
  - Slider bridge nodes:
    - `src/arduinobot_controller/src/slider_control.cpp`
    - `src/arduinobot_controller/arduinobot_controller/slider_control.py`
- **Description updates:** `arduinobot_description`
  - Added `src/arduinobot_description/urdf/arduinobot_ros2_control.xacro`
  - Added `src/arduinobot_description/urdf/arduinobot_gazebo.xacro`
  - Updated `src/arduinobot_description/urdf/arduinobot.urdf.xacro` to include both control-related Xacro files

### Controller setup
The controllers are defined in `arduinobot_controllers.yaml`:
- `joint_state_broadcaster` (`joint_state_broadcaster/JointStateBroadcaster`)
- `arm_controller` (`joint_trajectory_controller/JointTrajectoryController`) for `joint_1`, `joint_2`, `joint_3`
- `gripper_controller` (`joint_trajectory_controller/JointTrajectoryController`) for `joint_4`

### URDF / Gazebo integration
- `arduinobot_ros2_control.xacro` defines command/state interfaces for arm and gripper joints.
- `arduinobot_gazebo.xacro` loads the ROS 2 control plugin in Gazebo:
  - `ign_ros2_control` for ROS 2 Humble
  - `gz_ros2_control` for ROS 2 Iron+ 
- `gazebo.launch.py` passes `is_ignition` to Xacro based on `$ROS_DISTRO`.

### Slider control flow
`slider_controller.launch.py` starts:
1. `controller.launch.py` to spawn controllers
2. `joint_state_publisher_gui` with remap `/joint_states` → `/joint_commands`
3. `slider_control` node

The `slider_control` node subscribes to `/joint_commands` (`sensor_msgs/JointState`) and publishes trajectory commands to:
- `/arm_controller/joint_trajectory`
- `/gripper_controller/joint_trajectory`

### Build
```bash
cd ~/arduinobot_ws
colcon build --packages-select arduinobot_description arduinobot_controller
```

### Source environment
```bash
source /opt/ros/$ROS_DISTRO/setup.bash
source ~/arduinobot_ws/install/setup.bash
```

### Run Step 8 (two terminals)
Terminal 1 (Gazebo + robot):
```bash
ros2 launch arduinobot_description gazebo.launch.py
```

Terminal 2 (controllers + sliders):
```bash
ros2 launch arduinobot_controller slider_controller.launch.py
```

### Verify
```bash
ros2 control list_controllers
ros2 topic list | grep joint_trajectory
```

### What I learned in Step 8
- How to connect URDF/Xacro with `ros2_control` interfaces and Gazebo control plugins
- How to configure and spawn trajectory controllers from `controller_manager`
- How to remap GUI joint output and translate it into controller trajectory commands
- How to keep a control stack modular by separating robot description and controller packages
