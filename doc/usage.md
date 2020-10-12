<img src="./banner.jpg" alt="" width="100%"/>

# The Idiap Human Perception system user guide

## Setting up `.bashrc`

The .bashrc should contain:

```
source /opt/ros/kinetic/setup.bash
export LD_LIBRARY_PATH=${HOME}/miniconda2/envs/py27-cu100-ihper/lib:${LD_LIBRARY_PATH}
export PYTHONPATH=${HOME}/miniconda2/envs/ihper/lib/python2.7/site-packages:${PYTHONPATH}
export PYTHONPATH=${HOME}/perception_deps:${PYTHONPATH}
export PYTHONPATH=${HOME}/perception_deps/vfoa:${PYTHONPATH}
export PYTHONPATH=${HOME}/perception_deps/archs:${PYTHONPATH}
export PYTHONPATH=${HOME}/perception_deps/apkit:${PYTHONPATH}
source /home/ocanevet/ihper_ws/devel/setup.bash
```

We assume `roscore` is running a terminal.

## Demo with your webcam

```bash
# In terminal 1
roslaunch video_stream_opencv camera.launch

# In terminal 2
roslaunch person_manager perception.launch webcam:=1 tracker_detector:=openheadpose manager_visu:=1
```

## Demo with Intel Realsense (Install [ROS Wrapper for Intel RealSense Devices](https://github.com/IntelRealSense/realsense-ros))

```bash
# In terminal 1
roslaunch realsense2_camera rs_camera.launch align_depth:=1 # Quick

# or
# W=640; H=360; Wd=$W; Hd=$H; fps=15; roslaunch realsense2_camera rs_camera.launch align_depth:=1 enable_color:=1 enable_depth:=1 color_fps:=${fps} color_height:=${H} color_width:=${W} depth_fps:=${fps} depth_height:=${Hd} depth_width:=${Wd} infra_fps:=${fps} infra_height:=${Hd} infra_width:=${Wd}

# In terminal 2
roslaunch person_manager perception.launch intel:=1 tracker_detector:=openheadpose manager_visu:=1
```

## Demo with the Kinect v2 (install [IAI kinect](https://github.com/code-iai/iai_kinect2))

Once you have calibrated your Kinect v2 device and put the files in
`/PATH/TO/CALIB_DIR/SERIAL_NO_OF_KINECT`, run

```bash
# In terminal 1
roslaunch kinect2_bridge kinect2_bridge.launch publish_tf:=1 calib_path:=/PATH/TO/CALIB_DIR worker_threads:=3 fps_limit:=15

# In terminal 2
roslaunch person_manager perception.launch kinect:=1 tracker_detector:=openheadpose manager_visu:=1
```

## With Pepper

The sound source localization works on Pepper 1.7 (cardiod microphones).

```bash
# In terminal 1
roslaunch naoqi_driver naoqi_driver.launch # network_interface:=eth0

# In terminal 2
# Only visual part
roslaunch person_manager perception.launch naoqi:=1 tracker_detector:=openheadpose manager_visu:=1
# With sound source localization
# roslaunch person_manager perception.launch naoqi:=1 tracker_detector:=openheadpose manager_visu:=1 with_audio:=1
```

## Options

| Option                                 | Description                                                                                              |
|----------------------------------------|----------------------------------------------------------------------------------------------------------|
| `tracker_scale (default 0.5)`          | Resizing ratio before processing the image. `1` is original resolution of the input image, `0.5` devides height and width by 2. The overall resolution for processing by IHPER should be > QVGA, otherwise the faces are too small for detection. |
| `tracker_detector (default cv)` | Which face detector to use. `cv` for OpenCV detector, `openpose` for OpenPose body join detector, `openheadpose` for OpenPose body joints and head pose from OpenHeadPose. |
| `tracker_delay (default 50)`           | Time in milliseconds between 2 calls of face detector by the tracker. Higher values speeds up the tracker, but the face detector is called less often. In particular, if the `openheadpose` is provided as a face detector, the OpenHeadPose pose is not provided "in between" calls of the detector. |
| `tracker_particles (default 100)`      | Number of particles for the tracker. Higher values provide more stable output but slows down the process. |
| `tracker_visu (default 0)`             | Whether to visualize the output of the tracker (displays tracklet IDs).                                   |
| `manager_visu (default 1)`             | Whether to visualize the output of the person manager (displays re-identified IDs).                       |
| `manager_keep_threshold (default 0.1)` | Threshold below which a face is not inserted in the gallery because considered too similar to other faces of the same identity. Lower value stores more faces and slows down the process. |
| `manager_reid_threshold (default 0.4)` | Threshold below which 2 faces are considered of the same identity. Higher values causes re-identification to be faster but with more false positives. |
