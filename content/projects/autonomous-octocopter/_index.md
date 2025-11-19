---
title: "Autonomous Octocopter with Real-Time Object Detection"
date: 2018-12-15T10:00:00-00:00
description: "Building an autonomous octocopter using Jetson Nano and YOLOv3 for real-time object detection"
categories: ["Robotics", "Computer Vision"]
tags: ["octocopter", "jetson-nano", "yolov3", "autonomous-navigation", "computer-vision", "embedded-systems"]
weight: 2
---

## Final Test Flight

{{< youtube TTFNwvMHMU8 >}}

An autonomous flight system built around NVIDIA's Jetson Nano running YOLOv3 for real-time object detection and navigation.

## Project Overview

During my time at the University of Minnesota Duluth, I built an autonomous octocopter that combined embedded AI with flight control systems. The goal was to create a drone capable of autonomous navigation using real-time computer vision.

## Hardware Architecture

### Core Components

**Flight Platform**
- Custom octocopter frame (8 motors for stability and redundancy)
- Pixhawk/iX4 autopilot flight controller
- GPS module for position tracking
- Electronic Speed Controllers (ESCs) for motor control

**AI Computing**
- **NVIDIA Jetson Nano**: The brain of the operation
  - 128-core Maxwell GPU for YOLOv3 inference
  - Quad-core ARM A57 CPU
  - 4GB LPDDR4 memory
  - Power consumption optimized for battery operation

**Sensors & Connectivity**
- USB camera for computer vision input
- Telemetry radio for ground station communication
- Battery monitoring system

### Hardware Integration Photos

![Jetson Nano Bottom View 1](/images/autonomous-octocopter/20191226_113616.jpg)
*Bottom view showing the NVIDIA Jetson Nano mounted on the octocopter frame with the Pixhawk flight controller, GoPro camera, and power distribution system*

![Jetson Nano Bottom View 2](/images/autonomous-octocopter/20191228_141510.jpg)
*Close-up of the Jetson Nano with custom cooling fan, showing the USB ports, GPIO header, and power connections*

### Integration Challenges

Integrating the Jetson Nano with the flight controller required:
- Custom power distribution to provide clean 5V to the Jetson
- UART/MAVLink communication between Jetson and Pixhawk
- Weight optimization (every gram counts in flight time)
- Vibration isolation for the camera

## YOLOv3 Real-Time Object Detection

### Why YOLOv3?

YOLOv3 (You Only Look Once) was chosen for its balance of accuracy and speed - critical for real-time autonomous navigation:
- Single-pass detection (vs. region proposal methods)
- ~30 FPS on Jetson Nano with optimized models
- Multi-scale prediction for objects at various distances

### Implementation Details

**Model Optimization**
- Used TensorRT to optimize YOLOv3 for Jetson's GPU
- Reduced model precision (FP16) while maintaining accuracy
- Custom training on aerial imagery dataset
- Focused on detecting: people, vehicles, obstacles, landing markers

**Real-Time Pipeline**
1. Capture frame from camera
2. Preprocess and resize (416x416)
3. Run YOLOv3 inference on GPU
4. Parse detections and compute bounding boxes
5. Send navigation commands to flight controller

## Autonomous Navigation

The system operated in several modes:

**Object Tracking Mode**
- Lock onto and follow detected objects
- Maintain safe distance using depth estimation
- Automatic obstacle avoidance

**Waypoint Navigation**
- GPS-based waypoint following
- Object detection for dynamic obstacle avoidance
- Return-to-home on low battery or signal loss

**Landing Assistance**
- Visual marker detection for precision landing
- Automatic descent when safe landing zone detected

## Results & Performance

![Winter Flight Testing](/images/autonomous-octocopter/20191220_163642.jpg)
*Field testing in winter conditions - the octocopter proved reliable even in cold weather and challenging lighting conditions*

### What Worked

**Flight Stability**
- Octocopter configuration provided excellent stability
- Motor redundancy allowed continued flight with single motor failure
- Smooth integration between vision system and flight controller

**Detection Performance**
- Consistent 25-30 FPS for object detection
- Reliable detection at ranges of 5-50 meters
- Low false positive rate after model fine-tuning

**Autonomous Behavior**
- Successfully demonstrated autonomous takeoff, navigation, and landing
- Real-time obstacle detection and avoidance
- Stable tracking of moving objects

### Limitations

**Power Constraints**
- Jetson Nano added significant power draw
- Flight time reduced to ~12 minutes (vs. ~18 without AI payload)
- Battery weight trade-offs

**Processing Latency**
- End-to-end latency of ~100ms (camera → decision → actuation)
- Acceptable for many tasks, but challenging at high speeds

**Weather Sensitivity**
- Computer vision performance degraded in poor lighting
- Rain and fog significantly impacted detection reliability

## Technical Lessons Learned

**Embedded AI Optimization**
- TensorRT optimization was crucial for real-time performance
- FP16 precision provided best speed/accuracy trade-off
- Model quantization and pruning techniques extended battery life

**Hardware Integration**
- Power filtering is critical - motor noise can crash the Jetson
- Vibration isolation matters more than expected for vision
- UART communication needs proper flow control for reliability

**Autonomous Systems**
- Multiple redundant safety systems are non-negotiable
- Failsafe behaviors need extensive testing
- Always test with GPS spoofing and signal loss scenarios

## Future Improvements

If I were to rebuild this system today, I would:

**Hardware Upgrades**
- Jetson Xavier NX or Orin Nano (better performance per watt)
- Stereo camera for depth perception
- LiDAR for all-weather obstacle detection

**Software Enhancements**
- Newer YOLO versions (YOLOv8/YOLOv9) for better efficiency
- Visual-inertial odometry (VIO) for GPS-denied navigation
- Reinforcement learning for more sophisticated flight behaviors

**Safety Features**
- Parachute deployment system
- Geofencing with multiple boundary layers
- Computer vision-based landing zone assessment

## Conclusion

This project was an excellent introduction to autonomous robotics, combining:
- Embedded systems engineering
- Real-time computer vision
- Flight control systems
- AI model optimization

The experience of getting AI running efficiently on embedded hardware while meeting the strict timing requirements of autonomous flight taught valuable lessons about system integration and real-world constraints.

While commercial solutions have since surpassed this custom build, the hands-on experience of building an autonomous system from scratch provided insights that aren't available from using off-the-shelf solutions.

## Technical Stack

- **Flight Controller**: Pixhawk/iX4 Autopilot
- **AI Platform**: NVIDIA Jetson Nano
- **Computer Vision**: YOLOv3 + OpenCV
- **Optimization**: TensorRT, CUDA
- **Communication**: MAVLink protocol
- **Development**: Python, C++
- **Ground Station**: QGroundControl

---

*This project was completed in 2018 as part of my studies at the University of Minnesota Duluth.*
