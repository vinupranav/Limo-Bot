# LimoBot — Autonomous Infrastructure Inspection Robot 🤖

![ROS2](https://img.shields.io/badge/ROS2-Humble-blue?logo=ros&logoColor=white)
![Python](https://img.shields.io/badge/Python-3.10-3776AB?logo=python&logoColor=white)
![C++](https://img.shields.io/badge/C++-17-00599C?logo=cplusplus&logoColor=white)
![Ubuntu](https://img.shields.io/badge/Ubuntu-22.04%20%7C%2024.04-E95420?logo=ubuntu&logoColor=white)
![TensorRT](https://img.shields.io/badge/TensorRT-8.x-76B900?logo=nvidia&logoColor=white)
![ONNX](https://img.shields.io/badge/ONNX-Runtime-005CED?logo=onnx&logoColor=white)
![SLAM](https://img.shields.io/badge/SLAM-2D%20%7C%203D-orange)
![Computer Vision](https://img.shields.io/badge/Computer%20Vision-YOLOv8-FF6F00)
![Jetson](https://img.shields.io/badge/NVIDIA-Jetson-76B900?logo=nvidia&logoColor=white)
![Gazebo](https://img.shields.io/badge/Simulation-Gazebo-informational)
![License](https://img.shields.io/badge/License-MIT-green)

> **Fully autonomous infrastructure inspection robot** — real-time 2D LiDAR SLAM, 3D depth mapping, YOLOv8-based pothole detection with precise area estimation, and autonomous navigation on NVIDIA Jetson edge hardware.

---

## 📌 Overview

LimoBot is a fully autonomous inspection robot built on the LIMO mobile platform running ROS 2 on an NVIDIA Jetson. It integrates a complete autonomy stack — from 2D LiDAR SLAM and 3D depth-based mapping to a real-time computer vision pipeline for pothole detection and precise spatial area estimation using an Orbbec DaBai depth camera.

The system operates **end-to-end without human intervention**, autonomously navigating real-world environments, detecting and measuring all potholes, and generating a complete infrastructure inspection report.

> 🔒 **Source code will be made open-source following paper publication. Demo videos below.**

---

## 🎬 Demo Videos

| Demo | Link |
|---|---|
| Full Autonomous Inspection Run | Coming Soon |
| Pothole Detection Pipeline | Coming Soon |
| 2D SLAM & 3D Mapping | Coming Soon |
| Gazebo Simulation | Coming Soon |

---

## 🎯 Key Results

| Metric | Value |
|---|---|
| Pothole Detection mAP | **99%** |
| Inference Speed | **43 FPS** |
| Potholes Detected | **48 / 48 (100%)** |
| Area Estimation | Precise spatial dimensions via depth camera |
| Operation | Fully autonomous, zero human intervention |
| Hardware | NVIDIA Jetson |

---

## 🏗️ System Architecture

```
┌──────────────────────────────────────────────────────────────────────┐
│                          SENSOR LAYER                                │
│                                                                      │
│   Motor Encoders        EAI LiDAR           Orbbec DaBai             │
│   (Wheel Odometry)      (360° Scanning)     (Depth + RGB)            │
│         │                    │               │            │          │
│         │               IMU (Inertial)       │            │          │
│         │                    │               │            │          │
└─────────┼────────────────────┼───────────────┼────────────┼──────────┘
          │                    │               │            │
          ▼                    ▼               │            │
┌─────────────────────┐  ┌─────────────────┐   │            │
│   EKF Sensor Fusion │  │   2D LiDAR SLAM │   │            │
│  (robot_localization│  │   Occupancy     │   │            │
│   Encoders+IMU+Lidar│  │   Grid Mapping  │   │            │
│   → Pose Estimate)  │  └────────┬────────┘   │            │
└──────────┬──────────┘           │            │            │
           │                      │            │            │
           └──────────┬───────────┘            │            │
                      │                        │            │
                      ▼                        ▼            ▼
┌──────────────────────────────────────────────────────────────────────┐
│                     PERCEPTION & MAPPING LAYER                       │
│                                                                      │
│   ┌──────────────────────────────┐   ┌────────────────────────────┐  │
│   │   AMCL + EKF Localisation   │   │   3D Depth Mapping          │  │
│   │   Combined Pose Source      │   │   Orbbec DaBai → Point      │  │
│   │   2D Occupancy Grid Map     │   │   Cloud → Area Estimation   │  │
│   └──────────────────────────────┘   └────────────────────────────┘  │
│                                                                      │
│   ┌──────────────────────────────────────────────────────────────┐   │
│   │              Computer Vision Pipeline                        │   │
│   │   Orbbec RGB + Depth → YOLOv8 + TensorRT + ONNX              │   │
│   │   ├── Pothole Detection (99% mAP, 43 FPS)                    │   │
│   │   ├── Precise Area Estimation (Depth Camera)                 │   │
│   │   └── Spatial Location Logging (48/48 Detected)              │   │
│   └──────────────────────────────────────────────────────────────┘   │
└──────────────────────────────┬───────────────────────────────────────┘
                               │
                               ▼
┌──────────────────────────────────────────────────────────────────────┐
│                     PLANNING & CONTROL LAYER                         │
│                                                                      │
│   ┌──────────────────────────┐      ┌───────────────────────────┐    │
│   │   SMAC Planner           │      │   RPP Controller          │    │
│   │   Global Path Planning   │      │   (Regulated Pure Pursuit)│    │
│   │   (Nav2)                 │      │   Local Motion Control    │    │
│   └────────────┬─────────────┘      │   + Obstacle Avoidance    │    │
│                │                    └──────────────┬────────────┘    │
│                └──────────────┬────────────────────┘                 │
│                               │                                      │
│                               ▼                                      │
│                    Nav2 Navigation Stack                             │
│              (Behaviour Trees + Costmap 2D)                          │
└──────────────────────────────┬───────────────────────────────────────┘
                               │
                               ▼
┌──────────────────────────────────────────────────────────────────────┐
│                      INSPECTION REPORT LAYER                         │
│   Pothole Locations + Dimensions → Autonomous Infrastructure Report  │
└──────────────────────────────────────────────────────────────────────┘
                               │
                               ▼
┌──────────────────────────────────────────────────────────────────────┐
│                         ACTUATION LAYER                              │
│                    Motor Commands → LIMO Robot Platform              │
└──────────────────────────────────────────────────────────────────────┘
```

---

## ⚙️ Tech Stack

| Category | Technologies |
|---|---|
| **Framework** | ROS 2 (Humble) |
| **SLAM** | 2D LiDAR SLAM, 3D Depth Mapping |
| **Localisation** | AMCL, robot_localization (EKF) |
| **Sensor Fusion** | EKF (Motor Encoders + IMU + LiDAR) |
| **Global Planner** | SMAC Planner |
| **Local Planner** | RPP Controller (Regulated Pure Pursuit) |
| **Navigation** | Nav2, Costmap 2D, Behaviour Trees |
| **Computer Vision** | YOLOv8, OpenCV, TensorRT, ONNX |
| **Depth Processing** | Orbbec DaBai SDK, Point Cloud Processing |
| **Simulation** | Gazebo, Rviz2 |
| **Hardware** | NVIDIA Jetson, EAI LiDAR, Orbbec DaBai, IMU |
| **Languages** | Python, C++ |
| **Tools** | Docker, Foxglove, Rerun, Linux Ubuntu |

---

## 🔑 Key Features

### 1. Fully Autonomous Inspection
- End-to-end autonomous operation from start to finish without human intervention
- Autonomous navigation through real-world environments using Nav2 and SMAC Planner
- RPP Controller for smooth, stable local motion and obstacle avoidance

### 2. Real-Time Pothole Detection Pipeline
- YOLOv8 object detection with TensorRT and ONNX optimisation
- 99% mAP — detected and reported all 48 potholes correctly
- 43 FPS real-time inference on NVIDIA Jetson edge hardware

### 3. Precise Area Estimation via Depth Camera
- Orbbec DaBai depth camera for accurate spatial measurement
- Each pothole logged with precise dimensions and location
- Depth-based point cloud processing for area estimation

### 4. Autonomous Infrastructure Reporting
- Complete inspection report generated autonomously
- All pothole locations and measurements logged for infrastructure analysis
- Production-ready deployment on edge hardware

---

## 🛠️ Hardware Platform

| Component | Specification |
|---|---|
| **Platform** | LIMO Mobile Robot |
| **Compute** | NVIDIA Jetson |
| **LiDAR** | EAI LiDAR (360° Scanning) |
| **Depth Camera** | Orbbec DaBai |
| **IMU** | Built-in IMU |
| **Odometry** | Motor Encoders |
| **Steering Mode** | Differential / Tracked |

---

## 📦 Installation

> 🔒 Source code will be released upon paper publication. Star the repo to get notified.

```bash
# Clone the repository
git clone https://github.com/vinupranav/Limo-Bot.git
cd Limo-Bot
```

### Prerequisites
- ROS 2 Humble
- NVIDIA Jetson (JetPack 5.x / 6.x)
- Nav2
- Gazebo
- Python 3.10+

---

## 📊 Performance Benchmarks

### Pothole Detection

| Metric | Result |
|---|---|
| mAP | **99%** |
| Inference Speed | **43 FPS** |
| Potholes Detected | **48 / 48 (100%)** |
| Area Estimation | Depth Camera — Precise Spatial Dimensions |
| Operation Mode | Fully Autonomous |

---

## 👤 Author

**Pranav Prakash**
AI/ML and Robotics Software Engineer
Newcastle upon Tyne, UK

[LinkedIn](https://linkedin.com/in/vinupranav) | [GitHub](https://github.com/vinupranav) | [Email](mailto:pranav.s.prakash88@gmail.com)

---

## 📜 License

MIT License — see [LICENSE](LICENSE) for details.
