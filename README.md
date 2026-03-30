<div align="center">

```
███╗   ██╗██╗██╗  ██╗██╗██████╗  ██████╗ 
████╗  ██║██║██║ ██╔╝██║██╔══██╗██╔═══██╗
██╔██╗ ██║██║█████╔╝ ██║██████╔╝██║   ██║
██║╚██╗██║██║██╔═██╗ ██║██╔══██╗██║   ██║
██║ ╚████║██║██║  ██╗██║██║  ██║╚██████╔╝
╚═╝  ╚═══╝╚═╝╚═╝  ╚═╝╚═╝╚═╝  ╚═╝ ╚═════╝
            ⚙️  HARDWARE
```

### **Real Robot. Real Firmware. Real Deployment.**
*Micro-ROS · Teensy 4.1 · Raspberry Pi 5 · BTS7960 · YDLidar X2*

[![ROS2](https://img.shields.io/badge/ROS2-Humble-blue?style=for-the-badge&logo=ros)](https://docs.ros.org/en/humble/)
[![Micro-ROS](https://img.shields.io/badge/Micro--ROS-Teensy_4.1-purple?style=for-the-badge)](https://micro.ros.org/)
[![PlatformIO](https://img.shields.io/badge/PlatformIO-Firmware-orange?style=for-the-badge&logo=platformio&logoColor=white)](https://platformio.org/)
[![Based On](https://img.shields.io/badge/Based_on-linorobot2-grey?style=for-the-badge)](https://github.com/linorobot/linorobot2_hardware)

</div>

---

## 🤖 What is Nikiro Hardware?

This repo contains the **embedded firmware and hardware setup** for the physical Nikiro AMR — the real-world counterpart to the simulation stack. It bridges a Teensy 4.1 microcontroller to ROS 2 via Micro-ROS, handling motor control, encoder feedback, and IMU data in real-time.

> Built on top of [linorobot2_hardware](https://github.com/linorobot/linorobot2_hardware) and extended for the Nikiro platform.

---

## 🔩 Bill of Materials

| Component | Spec | Qty |
|-----------|------|:---:|
| **DC Motor w/ Encoder** | Pro-Range Planetary Gear 12V 96RPM 121.7 N-cm · PG36M555-50.9K · ME-37 7PPR | 2× |
| **Wheels** | 100mm × 40mm × 8mm | 2× |
| **Battery** | 12V | 2× |
| **Microcontroller** | Teensy 4.1 | 1× |
| **SBC** | Raspberry Pi 5 | 1× |
| **IMU** | MPU6050 | 1× |
| **Motor Driver** | BTS7960 | 2× |
| **Buck Converter** | 12V → logic level | 2× |
| **LiDAR** | YDLidar X2 | 1× |

---

## ⚡ Inside Nikiro

<div align="center">

![Inside Nikiro](https://github.com/user-attachments/assets/b1985cd6-d1f5-4c6a-a035-96975a60183b)

</div>

---

## 🔌 Wiring Reference

<div align="center">

| BTS7960 Motor Driver | MPU6050 IMU |
|:-:|:-:|
| ![BTS7960 Connection](https://github.com/user-attachments/assets/f38ccecc-9a5e-4315-9e65-a4ef8998eca6) | ![IMU Connection](https://github.com/user-attachments/assets/03c34d0a-17e2-4fb2-a1a1-65babee18f5b) |

</div>

---

## 🚀 Getting Started

### Step 1 — Clone the Repo

```bash
cd $HOME
git clone https://github.com/logesh1516/Nikiro_hardware
```

### Step 2 — Install PlatformIO

PlatformIO lets you build and upload firmware headlessly — no Arduino IDE needed. Ideal for Raspberry Pi deployment.

```bash
curl -fsSL -o get-platformio.py https://raw.githubusercontent.com/platformio/platformio-core-installer/master/get-platformio.py
python3 get-platformio.py
```

Add PlatformIO to your `$PATH`:
```bash
echo "PATH=\"\$PATH:\$HOME/.platformio/penv/bin\"" >> $HOME/.bashrc
source $HOME/.bashrc
```

---

## 🔧 Calibration

Before flashing the main firmware, run the calibration routine to find your motor's encoder counts per revolution.

```bash
cd linorobot2_hardware/calibration
pio run --target upload -e <your_teensy_board>
```

**Supported Teensy boards:**

| Board | Flag |
|-------|------|
| Teensy 3.1 | `teensy31` |
| Teensy 3.5 | `teensy35` |
| Teensy 3.6 | `teensy36` |
| Teensy 4.0 | `teensy40` |
| Teensy 4.1 | `teensy41` |

**In the serial monitor:**

```
spin    →  check motor rotation direction
sample  →  read encoder counts per revolution
```

> Copy the CPR value from `sample` and replace it in the main firmware config before uploading.

---

## 🧠 Micro-ROS Setup

Micro-ROS bridges the Teensy 4.1 to ROS 2 over serial — enabling the microcontroller to publish odometry and subscribe to velocity commands directly on the ROS 2 graph.

```bash
# Source ROS 2
source /opt/ros/$ROS_DISTRO/setup.bash

# Create workspace & clone micro-ROS tools
mkdir microros_ws && cd microros_ws
git clone -b $ROS_DISTRO https://github.com/micro-ROS/micro_ros_setup.git src/micro_ros_setup

# Install dependencies
sudo apt update && rosdep update
rosdep install --from-paths src --ignore-src -y
sudo apt-get install python3-pip

# Build micro-ROS tools
colcon build
source install/local_setup.bash

# Download & build the micro-ROS agent
ros2 run micro_ros_setup create_agent_ws.sh
ros2 run micro_ros_setup build_agent.sh
source install/local_setup.bash

# Launch the agent (connect to Teensy via USB serial)
ros2 run micro_ros_agent micro_ros_agent serial --dev /dev/ttyACM0
```

---

## 🕹️ Robot Operation

**Drive the robot with keyboard:**
```bash
ros2 run teleop_twist_keyboard teleop_twist_keyboard
```

**Verify all topics are publishing:**
```bash
ros2 topic list
```

Expected topics on a healthy bringup:

```
/cmd_vel
/imu/data
/odom/unfiltered
/parameter_events
/rosout
```

---

## 🏗️ Architecture

```
┌──────────────────────────────────────────────────────┐
│                  NIKIRO HARDWARE STACK                │
│                                                      │
│  ┌─────────────┐    serial    ┌───────────────────┐  │
│  │  Teensy 4.1 │◄────────────►│  Raspberry Pi 5   │  │
│  │             │   Micro-ROS  │                   │  │
│  │  Firmware   │              │  ROS 2 (Humble)   │  │
│  │  - PID      │              │  Nav2 / SLAM      │  │
│  │  - Encoders │              │  micro_ros_agent  │  │
│  │  - IMU      │              │                   │  │
│  └──────┬──────┘              └────────┬──────────┘  │
│         │                              │             │
│   ┌─────▼──────┐               ┌──────▼──────┐      │
│   │ BTS7960 ×2 │               │ YDLidar X2  │      │
│   │ DC Motors  │               │ /scan topic │      │
│   └────────────┘               └─────────────┘      │
└──────────────────────────────────────────────────────┘
```

---

## 🌐 The Nikiro Ecosystem

| Repo | Description | Link |
|------|-------------|------|
| **Nikiro_simulation** | ROS 2 sim — SLAM, Nav2, conveyor & arm | [→ View](https://github.com/logesh1516/Nikiro_simulation) |
| **Nikiro_docker** | One-command containerized deployment | [→ View](https://github.com/logesh1516/Nikiro_docker) |
| **nikiro_isaac_sim** | NVIDIA Isaac Sim USD scenes & AI training | [→ View](https://github.com/logesh1516/nikiro_isaac_sim) |
| **Nikiro_hardware** | Embedded firmware & real robot setup | *you're here* |

---

<div align="center">

Built with ⚙️ + 🤖 by **Logesh**  
*Where the simulation ends and the real robot begins.*

</div>
