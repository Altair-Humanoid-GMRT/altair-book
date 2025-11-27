# Altair Framework and Software Architecture

Altair Framework adalah program [OP3 Framework](https://github.com/ROBOTIS-GIT/ROBOTIS-Framework) dari ROBOTIS. Package altair_manager menginisialisasi berbagai `motion module`. `Motion module` adalah program controller kita seperti walking dan kick. Untuk menambahkan motion module, kita hanya perlu memanggil fungsi addMotionModule(). Contoh:

```cpp
controller->addSensorModule((SensorModule*) RobotState::getInstance());
controller->addSensorModule((SensorModule*) Odometry::getInstance());
/* Add Motion Module */
controller->addMotionModule((MotionModule*) ActionModule::getInstance());
controller->addMotionModule((MotionModule*) BaseModule::getInstance());
controller->addMotionModule((MotionModule*) HeadControlModule::getInstance());
controller->addMotionModule((MotionModule*) Kicking::getInstance());
controller->addMotionModule((MotionModule*) QuinticWalk::getInstance());
```

## Motion Module

Motion module adalah package ROS2 tetapi isi programnya mengikuti kerangka dasar yang telah dibentuk oleh `base class` Altair Framework. Berikut ini adalah `Base class` motion module ([atau cek langsung source code Github](https://github.com/ROBOTIS-GIT/ROBOTIS-Framework/blob/master/robotis_framework_common/include/robotis_framework_common/motion_module.h)):

```cpp
class MotionModule
{
protected:
  bool        enable_;
  std::string module_name_;
  ControlMode control_mode_;

public:
  std::map<std::string, DynamixelState *> result_;

  virtual ~MotionModule() { }

  std::string getModuleName()   { return module_name_; }
  ControlMode getControlMode()  { return control_mode_; }

  void setModuleEnable(bool enable)
  {
    if(this->enable_ == enable)
      return;

    this->enable_ = enable;
    if(enable)
      onModuleEnable();
    else
      onModuleDisable();
  }
  bool getModuleEnable() { return enable_; }

  virtual void  onModuleEnable() { }
  virtual void  onModuleDisable() { }

  virtual void  initialize(const int control_cycle_msec, Robot *robot) = 0;
  virtual void  process(std::map<std::string, Dynamixel *> dxls, std::map<std::string, double> sensors) = 0;

  virtual void	stop() = 0;
  virtual bool	isRunning() = 0;
};

```

Berdasarkan `base class` tersebut, ketika kita ingin membuat `motion module` yahg baru, kita perlu membuat program dengan prinsip OOP, [**`Inheritance`**(Klik untuk cek lebih lanjut)](https://www.geeksforgeeks.org/cpp/inheritance-in-c/). Contoh implementasi, asumsi nama class di program motion module kita yang baru yaitu ExperimentModule:

```cpp
class ExperimentModule : public robotis_framework::MotionModule, public robotis_framework::Singleton<ExperimentModule>, public rclcpp::Node
```

Berdasarkan cuplikan program di atas, class ExperimentModule menurunkan dari base class `robotis_framework::MotionModule`, `robotis_framework::Singleton`, dan `rclcpp::Node`. 

Penjelasan tiap fungsi `Motion Module`:

- `initialize()` \
Dipanggil sekali setiap program dijalankan. Biasanya digunakkan untuk inisialisasi paramater dan membuat subscriber/ publisher. 

- `queueThread()` \
Fungsi thread lainnya untuk menjalankan instruksi secara paralel dengan fungsi `process()`. 

- `stop()` \
Fungsi yang dipanggil ketika motion module dihentikan.

- `isRunning()` \
Fungsi untuk mengecek apakah motion module masih berjalan atau tidak. 

- `onModuleEnable()` \
Fungsi yang dipanggil ketika motion module pertama kali dijalankan. Jika `initialize()` dipanggil hanya satu kali diawal ketika motion module dijalankan, `onModuleEnable()` dipanggil setiap kali motion module diaktifkan.

- `onModuleDisable()` \
Fungsi yang dipanggil ketika motion module dimatikan. 

- `process()` \
Fungsi utama yang berjalan secara looping terus menerus hingga motion module dimatikan secara manual atau terjadi error. 

    Pernyataan dari ROBOTIS Docs terkait `process()` (https://emanual.robotis.com/docs/en/software/robotis_framework_packages/):

    > What process() does:
    > - Receives status packets with Bulk Read to get status of each sensors and joints.
    > - Transfers the result value of the Motion Module with Sync Write.
    > - Transfers instruction packet to each sensors and joints with Bulk Read.
    > - Calls process() function of the Sensor Module in the list and saves the result value.
    > - Calls process() function of the Motion Module in the list and saves the result value.



Credit:

- https://github.com/ROBOTIS-GIT/ROBOTIS-Framework
- https://emanual.robotis.com/docs/en/software/robotis_framework_packages/


| Author | Contact | Date |
|--------|---------|------|
| Falah Naufal Zaki | falahnzk.dev@gmail.com | 27 Nov, 2025 |