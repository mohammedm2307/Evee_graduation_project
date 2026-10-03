# EVee: Autonomous Electric Vehicle (AEV)

An open-source, compact, single-passenger Autonomous Electric Vehicle developed for low-speed autonomous transportation in structured environments such as university campuses, indoor facilities, and pedestrian zones. EVee integrates a robust mechanical chassis, distributed embedded systems, real-time computer vision, and a cross-platform mobile application into a unified intelligent mobility platform.

## Project Overview

EVee operates at a maximum speed of 5 km/h and relies on a heterogeneous compute hierarchy. This architecture intentionally separates computationally intensive perception algorithms (handled by a central Linux-based system) from the strict, hard real-time requirements of physical actuators (handled by bare-metal microcontrollers).

## Key Features

* **Autonomous Navigation:** Utilizes an Extended Kalman Filter (EKF) to fuse GPS, IMU, and wheel odometry data for accurate state estimation, paired with a Pure Pursuit path-tracking algorithm.


* **Real-Time Perception:** Features a custom stereo vision rig and a TensorRT-optimized YOLOv8s model to detect pedestrians, vehicles, and speed bumps, achieving a mean Average Precision (mAP@0.50) of 0.939.


* **Distributed Low-Level Control:** Employs three independent ESP32 microcontrollers to guarantee microsecond-precision interrupt responses for steering, propulsion, and acoustic safety.


* **Smartphone Integration:** A companion Flutter application allows users to authenticate via Firebase, locate available vehicles in real-time, transmit waypoints over Bluetooth Low Energy (BLE), and control motion using a safety-engineered "Hold to GO" interface.


* **Structural Integrity:** Built on a lightweight 3030 aluminum T-slot ladder chassis and a steel tubular body frame, achieving a simulated Factor of Safety (FOS) of 35.3 under an 80 kg design load.



## System Architecture

### 1. High-Level Compute (Perception & Planning)

* **Core Hardware:** NVIDIA Jetson Orin Nano module featuring a 128-core Maxwell GPU and quad-core ARM Cortex-A57 CPU.


* **Operating System & Middleware:** Ubuntu 20.04 LTS running Robot Operating System (ROS 1 Noetic).


* **Vision Pipeline:** Dual Raspberry Pi IMX219 cameras capture video via a hardware-accelerated GStreamer pipeline using the NVIDIA Argus API. Depth is extracted using the ROS `stereo_image_proc` package with StereoBM block matching.



### 2. Low-Level Embedded Control (Actuation)

Three Espressif ESP32 microcontrollers run FreeRTOS firmware, communicating with the Jetson via `rosserial` over UART at 115,200 baud:

* **ESP32 #1 (Steering Node):** Actuates the Ackermann steering mechanism using a NEMA 23 stepper motor, DM860H microstepping driver, and a TR8x8 lead screw. Incorporates a V-156-1C25 limit switch for hardware homing.


* **ESP32 #2 (Propulsion & Kinematic Node):** Controls two 350 W rear Brushless DC (BLDC) in-wheel hub motors via Dalishen controllers. It also parses I2C data from a Bosch BNO055 9-axis IMU and processes quadrature encoder interrupts.


* **ESP32 #3 (Acoustic Safety & BLE Node):** Processes distance readings from six HC-SR04 ultrasonic sensors and manages the NimBLE-Arduino Bluetooth stack for mobile app communication.



### 3. Mobile Application (Flutter)

The user interface is built with Flutter and Dart, utilizing a unidirectional BLOC state machine.

* **Backend:** Firebase Authentication (Phone OTP) for secure access and Cloud Firestore for real-time vehicle availability tracking.


* **Communication:** Pairs with the vehicle via QR code scanning and transmits decoded GPS polylines as 20-byte BLE chunks directly to the ESP32 GATT server.


* **Safety Interface:** Uses a continuous long-press interaction model ("Hold to GO / Hold to STOP") to prevent accidental activation.



## ROS 1 Node Topology

| Package / Node | Function |
| --- | --- |
| `camera0_node` / `camera1_node` | Captures left and right video streams at 1280x720 (30 fps) using Jetson ISP.

 |
| `stereo_image_proc` | Performs stereo rectification and generates dense disparity maps and 3D point clouds.

 |
| `yolo_node` | Executes TensorRT-compiled YOLOv8s inference in FP16 precision.

 |
| `ekf_localization_node` | Fuses 50 Hz IMU, 20 Hz odometry, and 5 Hz GPS (`ublox_gps`) data.

 |
| `path_follower_node` | Calculates steering angles and velocity commands using the Pure Pursuit geometric formulation.

 |

## Authors & Acknowledgments

This project was developed at the **Faculty of Engineering, Mechanical Engineering Department (Mechatronics), Capital University** for the 2025-2026 academic year.

**Engineering Team:**

* Mohamed Hussien Abdelatty Saleh


* Mohamed Ayman Abdelaleem


* Mohammed Mahmoud Mohammed


* Omar Magdy Mohamed Ali


* Mohamed Zaki Abdulfattah Masharka


* Mathew Mamdooh Wasfy


* Hasan Osama Walid Khamees



**Supervisors:**

* Dr. Abdelshafi Ali


* Dr. Mohamed Adel
