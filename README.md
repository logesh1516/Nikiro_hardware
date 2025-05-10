# Nikiro_hardware
Nikiro is a ROS 2-based Autonomous Mobile Robot (AMR) platform designed for indoor navigation using a differential drive system. Built with modularity and simulation in mind, Nikiro leverages Gazebo, RViz, and Nav2 for SLAM, localization, and path planning.This repo is based on linorobot2.

Download linorobot2_hardware
```
cd $HOME
git clone https://github.com/logesh1516/Nikiro_hardware
```
Install PlatformIO
Download and install platformio. Platformio allows you to develop, configure, and upload the firmware without the Arduino IDE. This means that you can upload the firmware remotely which is ideal on headless setup especially when all components have already been fixed.
```
curl -fsSL -o get-platformio.py https://raw.githubusercontent.com/platformio/platformio-core-installer/master/get-platformio.py
python3 get-platformio.py
```
Add platformio to your $PATH:
```
echo "PATH=\"\$PATH:\$HOME/.platformio/penv/bin\"" >> $HOME/.bashrc
source $HOME/.bashrc
```

# COMPONENTS USED

2 X Pro-Range Planetary Gear DC Motor 12V 96RPM 121.7 N-cm PG36M555-50.9K with encoder ME-37,7PPR

2 X 100mmx40mmx8mm wheels

2 X 12v Battery

1 X Teensy 4.1 (Microcontroller)

1 X Raspberry pi 5 (SBC)

1 X MPU6050 (IMU)

2 X BuckConverters

2 X BTS7960 Motor Driver

1 X YDlidar X2 

# CONNECTIONS (EXAMPLE)
![bts7960_connection](https://github.com/user-attachments/assets/f38ccecc-9a5e-4315-9e65-a4ef8998eca6)

![imu_connection](https://github.com/user-attachments/assets/03c34d0a-17e2-4fb2-a1a1-65babee18f5b)

# INSIDE NIKIRO

![Niki](https://github.com/user-attachments/assets/b1985cd6-d1f5-4c6a-a035-96975a60183b)

# USAGE

use the calibration firmware to find the motor rev and replace it in the firmware.



