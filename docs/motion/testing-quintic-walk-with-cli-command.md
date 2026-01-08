# Testing Quintic Walk with CLI Command

It might be useful as testing the quintic walk in isolated manner, without another module. To do so, we can use CLI command to directly command the robot.

# The CLI Commands

> Note: This can works on real and simulated robot. 
> Use the altair_lab workspace.

1. Launch the altair_manager

    - For simulated environment:
    
    ```bash
    ros2 launch altair_manager op3_simulation.launch.py
    ```

    - For real environment:
    
    ```bash
    ros2 launch altair_manager altair_manager.launch.py
    ```

2. Set to quintic_walk module

```bash
ros2 service call /robotis/set_present_ctrl_modules robotis_controller_msgs/srv/SetModule "{module_name: 'quintic_walk_module'}"
```

3. Set to init pose

```bash
ros2 topic pub /robot_state altair_msgs/msg/RobotControlState "{state: 8}"
```

4. Start walk

```bash
 ros2 topic pub /cmd_vel geometry_msgs/msg/Twist "{linear: {x: 0.05, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 0.0}}"
```

> Note: if you want to stop, set the angular.x to -1.0

