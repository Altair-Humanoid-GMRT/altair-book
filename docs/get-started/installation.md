# altair_ws

## Description
The next-generation altair_ws.

## Prerequisites
- Install Jazzy Jalisco:  
  [ROS2 Jazzy Jalisco Installation Guide](https://docs.ros.org/en/jazzy/Installation/Ubuntu-Install-Debs.html)
- Dynamixel SDK:

  ```bash
  sudo apt install ros-jazzy-dynamixel-sdk
  ```

- Docker on Raspberry Pi 5

## Cloning the Repository

### Clone the Repository

```bash
git clone https://github.com/Altair-Humanoid-GMRT/altair_ws.git
```

Then pull the submodules with:

```bash
git submodule update --init --recursive --remote
```

### Pulling Commits from the Repository

```bash
git pull --rebase
```

The `--rebase` flag avoids creating merge commits, which result in non-linear history. Always use it for straight pulls. If you encounter conflicts (indicated by a big `CONFLICT` message), edit the mentioned files, run `git add` on the files, and then use `git rebase --continue` to proceed.

## Installation
### Installation on Linux PC

1. Install the dependencies for ROS2:

    ```bash
    sudo apt install ros-jazzy-xacro ros-jazzy-rqt ros-jazzy-rviz2 ros-jazzy-yaml-cpp-vendor ros-jazzy-vision-msgs ros-jazzy-nav-msgs ros-jazzy-orocos-kdl-vendor ros-jazzy-joint-trajectory-controller ros-jazzy-joint-state-publisher ros-jazzy-joint-state-publisher-gui ros-jazzy-joint-limits ros-jazzy-interactive-markers ros-jazzy-image-geometry ros-jazzy-imu-sensor-broadcaster ros-jazzy-kinematics-interface ros-jazzy-libyaml-vendor libserial-dev libserialport-dev libserial1 ros-jazzy-examples-rclcpp-minimal-subscriber ros-jazzy-examples-rclcpp-minimal-publisher ros-jazzy-yaml-cpp-vendor
    ```

2. Install the dependencies for ROS2 Control:

    ```bash
    sudo apt install ros-jazzy-ros2-control ros-jazzy-hardware-interface ros-jazzy-forward-command-controller ros-jazzy-filters ros-jazzy-controller-manager ros-jazzy-controller-manager-msgs ros-jazzy-control-toolbox ros-jazzy-controller-interface ros-jazzy-ros2controlcli ros-jazzy-ros2component ros-jazzy-pid-controller ros-jazzy-joint-state-broadcaster ros-jazzy-gz-ros2-control ros-jazzy-ros-gz-sim
    ```

3. Install the dependencies for MoveIt2:

    ```bash
    sudo apt install ros-jazzy-moveit ros-jazzy-rmw-cyclonedds-cpp ros-jazzy-cyclonedds ros-jazzy-moveit-ros-control-interface ros-jazzy-ompl
    ```

4. Change the ROS_DOMAIN_ID and RMW to CycloneDDS:

    ```bash
    echo "export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp" >> ~/.bashrc &&
    echo "export ROS_DOMAIN_ID=96" >> ~/.bashrc
    ```

5. Configure the USB port:

    ```bash
    sudo cp 99-dynamixel.rules /etc/udev/rules.d && sudo cp 99-usb-microcontroller.rules /etc/udev/rules.d &&
    sudo udevadm control --reload-rules && sudo udevadm trigger
    ```

6. Build the project (only some packages, as the rest are still in development):

    ```bash
    colcon build --packages-select dynamixel_workbench_toolbox dynamixel_msgs altair_description altair_moveit_config biped_interfaces altair_msgs ros2_python_extension dynamixel_hardware bio_ik rot_conv altair_splines microcontroller_hardware_v3 quintic_walk --executor sequential
    ```

7. Ready to walk!

8. Optional instruction

    Creating alias for command `colcon build --packages-select` as `only`

    ```bash
    echo "alias only='colcon build --packages-select'" >> ~/.bashrc
    ```

### Installation on Raspberry Pi 5 with Docker

1. Build the image (this will take some time):

    ```bash
    docker build -t altair_ws .
    ```

2. Run the compose:

    ```bash
    docker compose up -d
    ```

3. Interact with the Docker container:

    ```bash
    docker exec -it altair_ws /bin/bash
    ```

4. Build the project as described in the previous method.

## Usage

1. **Configuring the Robot**  
   Use the `configure.py` file located in the workspace. It provides a one-step gateway to change the config file all at once. Run it using:

   ```bash
   python3 configure.py
   ```

   or:

   ```bash
   ./configure.py
   ```

   Available options:

   ```bash
   options:
     -h, --help            show this help message and exit
     --real, -r            Set real_mode
     --mock, -m            Set mock_mode
     --sim, -s             Set sim_mode (also affects use_sim_time)
     --tuning {true,false}, -t {true,false}
                           Set tuning_mode
     --hardware-unavailable {true,false}, -w {true,false}
                           Set hardware_unavailable
     --dynamixel-port DYNAMIXEL_PORT
                           Set Dynamixel port
     --dynamixel-baudrate DYNAMIXEL_BAUDRATE
                           Set Dynamixel baudrate
     --stm32-port STM32_PORT
                           Set STM32 port
     --stm32-baudrate STM32_BAUDRATE
                           Set STM32 baudrate
   ```

2. **Launch the System**  

   ```bash
   ros2 launch altair_moveit_config move_group.launch.py
   ```

   ```bash
   ros2 launch quintic_walk controller.launch.py
   ```

## Running on Gazebo Simulation

[Preview of how to launch the Gazebo Simulation: YouTube Video](https://youtu.be/w1ja08d_PO8)

   ```bash
   ros2 launch altair_moveit_config move_group.launch.py
   ```

   ```bash
   ros2 launch quintic_walk altair_gazebo.launch.py
   ```

   A gazebo sim window and new terminal window to manage the gazebo sim like reset, and pause the simulation will show up.

### Running the Project

TODO: Create a bringup launch file.

## Contributing

If you would like to contribute, please fork the repository or create a new branch. Pull requests are warmly welcome.

## Acknowledgments

- Hat tip to anyone whose code was used.
- Thanks to alfarobi_ws for the inspiration.
- Thanks to Bitbots for the inspiration.

## TODO

- Add odometry fuser.
- Add motion sequence for standing up.
- Add a way to show errors and pass them to the microcontroller screen.
- Add BehaviorTree as robot strategy.
