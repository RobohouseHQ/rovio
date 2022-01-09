## How to use this fork
### Installation
1. Install [ROS](http://wiki.ros.org/ROS/Installation)
2. Create a ROVIO ROS workspace somewhere on your filesystem (e.g. `mkdir -p ~/rovio_ws/src`)
3. Clone this repository inside the source folder of the workspace (rovio will be a ROS package) with `cd ~/rovio_ws/src/ && git clone https://github.com/RobohouseHQ/VAMR_VO_project.git`
4. Clone the files from rovio submodules with `git submodule update --init --recursive`
5. Clone the kindr repository as another ROS package in the rovio workspace with `cd ~/rovio_ws/src/ && git clone https://github.com/ethz-asl/kindr`
6. Build the ROS workspace with `cd ~/rovio_ws/ && catkin build rovio --cmake-args -DCMAKE_BUILD_TYPE=Release`. If you encounter issues related to Python in this step, the following build command solved our issue: `catkin build -DPYTHON_EXECUTABLE=/usr/bin/python3 -DPYTHON_INCLUDE_DIR=/usr/include/python3.8m`
7. Source the workspace with `source devel/setup.bash`

### Running
We ran ROVIO on a rosbag with monocular camera data (both distorted and undistorted) and IMU data.
If you want to do something similar, 
1. follow the template of [rovio_vamr.info](cfg/rovio_vamr.info) (for the pre-calibrated extrinsics), [vamr_cam.yaml](cfg/vamr_cam.yaml) or [vamr_cam_undistorted.yaml](cfg/vamr_cam_undistorted.yaml) for the instristics, and [rovio_vamr.launch](launch/rovio_vamr.launch) for the launchfile.
2. When you are done with configuring, run `roslaunch rovio rovio_vamr.launch`

## ROVIO

This repository contains the ROVIO (Robust Visual Inertial Odometry) framework. The code is open-source (BSD License). Please remember that it is strongly coupled to on-going research and thus some parts are not fully mature yet. Furthermore, the code will also be subject to changes in the future which could include greater re-factoring of some parts.

Video: https://youtu.be/ZMAISVy-6ao

Papers:
* http://dx.doi.org/10.3929/ethz-a-010566547 (IROS 2015)
* http://dx.doi.org/10.1177/0278364917728574 (IJRR 2017)

Please also have a look at the wiki: https://github.com/ethz-asl/rovio/wiki

### Install without opengl scene ###
Dependencies:
* ros
* kindr (https://github.com/ethz-asl/kindr)
* lightweight_filtering (as submodule, use "git submodule update --init --recursive")

```
#!command

catkin build rovio --cmake-args -DCMAKE_BUILD_TYPE=Release
```

### Install with opengl scene ###
Additional dependencies: opengl, glut, glew (sudo apt-get install freeglut3-dev, sudo apt-get install libglew-dev)
```
#!command

catkin build rovio --cmake-args -DCMAKE_BUILD_TYPE=Release -DMAKE_SCENE=ON
```

### Euroc Datasets ###
The rovio_node.launch file loads parameters such that ROVIO runs properly on the Euroc datasets. The datasets are available under:
http://projects.asl.ethz.ch/datasets/doku.php?id=kmavvisualinertialdatasets

### Further notes ###
* Camera matrix and distortion parameters should be provided by a yaml file or loaded through rosparam
* The cfg/rovio.info provides most parameters for rovio. The camera extrinsics qCM (quaternion from IMU to camera frame, Hamilton-convention) and MrMC (Translation between IMU and Camera expressed in the IMU frame) should also be set there. They are being estimated during runtime so only a rough guess should be sufficient.
* Especially for application with little motion fixing the IMU-camera extrinsics can be beneficial. This can be done by setting the parameter doVECalibration to false. Please be carefull that the overall robustness and accuracy can be very sensitive to bad extrinsic calibrations.
