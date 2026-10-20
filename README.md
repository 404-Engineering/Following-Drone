# Following-Drone

A custom quadcopter that follows a jeep on off-road trails and builds 3D maps of the trail with an onboard stereo camera.

The drone uses a Jetson Orin Nano for mapping and following, a ZED stereo camera, and Raspberry Pi Pico 2 modules for the flight controller, radio, and sensors. The modules talk to each other over a CAN bus. A communication board in the jeep sends the jeep's GPS position to the drone and relays commands from the operator.

The full design is in **[docs/DESIGN.md](docs/DESIGN.md)**.

## Status

**Current milestone: M1, 3D mapping** (software only, no flight).

| Milestone | Name | Summary |
|---|---|---|
| **M1** | 3D mapping | ZED + Jetson capture, SVO recording, mapping, post-processing |
| M2 | Design and simulation | Freeze the hardware and firmware design; prove control, failsafes, and following in Gazebo |
| M3 | Flight bring-up | Build the drone; stabilization, altitude and position hold, autonomous takeoff and landing |
| M4 | Manual flight | Operator flies the drone from the jeep, with assisted modes and manual override |
| M5 | Vehicle following | Drone follows the jeep using its GPS beacon and maps the trail |

## System overview

| Module | Hardware | Role |
|---|---|---|
| Companion computer | Jetson Orin Nano | Mapping, SVO recording, follow logic |
| Camera | ZED stereo camera (USB 3) | Stereo images, depth, visual odometry |
| Flight controller | Pico 2 + IMU / magnetometer + 4× ESC | Stabilization, position hold, failsafes |
| Radio | Pico 2 + NRF24L01 | Link to the jeep; safety commands go straight to the flight controller |
| Sensors | Pico 2 + barometer + temp/humidity + GPS | Altitude, position, environment |
| Vehicle comm board | Pico 2 + NRF24L01 + GPS (in the jeep) | Broadcasts the jeep's position; relays operator commands |

The flight controller keeps flying if the Jetson crashes. Arm, disarm, and kill commands go from the radio module to the flight controller without passing through the Jetson.

## Repository layout

| Path | Contents |
|---|---|
| [`docs/`](docs/) | Design document |
| [`Jetson-Software/`](Jetson-Software/) | Software for the Jetson Orin Nano |
| [`Jetson-Software/3D_Space_Mapping/`](https://github.com/404-Engineering/3d_space_mapping) | **Submodule.** M1 3D mapping software |
| [`Embedded-Software/`](Embedded-Software/) | Firmware for the Pico 2 modules |
| [`Electrical-Schematics/`](Electrical-Schematics/) | KiCad schematics and PCB layouts |
| [`Electrical-Datasheet/`](Electrical-Datasheet/) | Component datasheets |
| [`Mechanical-Designs/`](Mechanical-Designs/) | 3D models (Fusion, STEP) |

## Getting started

Clone with submodules:

```bash
git clone --recurse-submodules https://github.com/404-Engineering/Following-Drone.git
```

If you already cloned without them:

```bash
git submodule update --init --recursive
```

To pull the latest commits for every submodule:

```bash
git submodule update --remote
```

## Tools

| Area | Tool |
|---|---|
| Schematics and PCBs | KiCad |
| 3D modeling and clearance checks | Autodesk Fusion |
| Simulation (M2) | Gazebo (standalone, no ROS) |
| Jetson software | JetPack, ZED SDK |

## License

GNU General Public License v3.0. See [LICENSE](LICENSE).
