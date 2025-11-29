# How to Build altair_lab

## Git Clone

```bash
git clone https://github.com/Altair-Humanoid-GMRT/altair_lab
git submodule update --init --recursive --remote
```

## Compile

Masuk ke folter altair_lab

```bash
cd altair_lab
```

Build altair_lab

```bash
colcon build --packages-up-to dynamixel_msgs dynamixel_sdk robotis_device robotis_framework_common robotis_controller op3_description rf_gz_bridge op3_gazebo_ros2 op3_base_module op3_action_module experiment_module altair_manager --executor sequential --symlink-install
```

```bash
source install/setup.bash
```

## Run the Simulation in Gazebo Sim

Kita dapat menjalankan altair_lab dalam simulasi Gazebo. Caranya yaitu dengan launch:

```bash
ros2 launch altair_manager op3_simulation.launch.py
```

Jika tidak ada error, kamu dapat menggerakkan servo robot di dalam simulasi secara manual. 

Berikut ini ros2 service untuk claim semua servo untuk menggunakan module `experiment_module`.

```bash
 ros2 service call /robotis/set_present_ctrl_modules robotis_controller_msgs/srv/SetModule "{module_name: 'experiment_module'}"
```

Berikut ini ros2 service untuk claim hanya servo `head_pan` & `head_tilt` yang menggunakan `experiment_module`

```bash
ros2 service call /robotis/set_present_joint_ctrl_modules robotis_controller_msgs/srv/SetJointModule "{joint_name: ['head_pan', 'head_tilt'], module_name: ['experiment_module', 'experiment_module']}"
```

Proses claim bertujuan agar servo tidak dijalankan oleh lebih dari satu `motion_module` sehingga tidak menimbulkan bentrok. 

Berikut ini `ros2 topic pub` untuk menggerakkan servo head_pan dan head_tilt dengan nilai yang kita mau secara manual:

```bash
ros2 topic pub -r 10 /robotis/override_command sensor_msgs/msg/JointState "{name: ['head_pan', 'head_tilt'], position: [0.0, -0.3]}"
```

Jika kita hanya meng-*claim* servo `head_pan` & `head_tilt`, `experiment_module` hanya dapat menggerakkan `head_pan` & `head_tilt`