<img src="./banner.jpg" alt="" width="100%"/>

# Table of contents

- [Kinect v2](#kinect-v2)
- [Intel D-series](#intel-d-series)
- [Intel ZR300](#intel-zr300)
- [Kinect Azure](#kinect-azure)

# Installation of external sensors

The following libraries are/may be required:

```bash
sudo apt install libgtk-3-dev
sudo apt install libturbojpeg
sudo apt install clinfo
sudo apt install opencl-headers
sudo apt install ocl-icd-libopencl1
sudo apt install ocl-icd-opencl-dev
sudo apt install libglfw3 libglfw3-dev
sudo apt install ros-kinetic-ddynamic-reconfigure
sudo apt install ros-kinetic-librealsense
sudo apt install ros-kinetic-librealsense2
```

## Kinect v2

### Compilation of libfreenect2

We install `libfreenect2` in the home directory because of IAI kinect
searching for it there.

```bash
cd ~/Downloads
git clone https://github.com/OpenKinect/libfreenect2
cd libfreenect2/
mkdir build
cd build
cmake -DENABLE_CXX11=ON -DCMAKE_INSTALL_PREFIX=${HOME}/freenect2 ..
make
make install
```

We also need to allwo accessing the device:

```bash
cd ~/Downloads
sudo cp libfreenect2/platform/linux/udev/90-kinect2.rules /etc/udev/rules.d/
```

### Compilation of the ROS workspace

Then, we can compile the ROS package to access the kinect. Feel free
to adapt the path to your custim installation:

```bash
cd ~/cameras_ws/src
git clone https://github.com/code-iai/iai_kinect2
cd ~/cameras_ws/
catkin_make -DCMAKE_BUILD_TYPE=Release
# You may need to do
# catkin_make -DCMAKE_BUILD_TYPE=Release -DCATKIN_BLACKLIST_PACKAGES="kinect2_viewer"
```

### How to use camera Kinect v2 with ROS

```bash
roslaunch kinect2_bridge kinect2_bridge.launch publish_tf:=1 calib_path:=/PATH/TO/CALIB_DIR worker_threads:=3 fps_limit:=15
```

## Intel D-series

### Compilation of librealsense2

```bash
cd ~/Downloads
https://github.com/IntelRealSense/librealsense
cd librealsense
mkdir build
cd build
cmake -DCMAKE_BUILD_TYPE=Release ..
make -j $(nproc)
make install
```

### Compilation of the ROS workspace

```bash
cd YOUR_CATKIN_WORKSPACE/src
git clone https://github.com/intel-ros/realsense
cd YOUR_CATKIN_WORKSPACE
catkin_make
```

If the compilation fails, your version of librealsense2 may be "too
new" compared to the ROS wrapper. You can checkout the librealsense2
to a earlier commit and re-install librealsense2 (previous section).

### How to use camera D400 via ROS

```bash
# In terminal 1
roslaunch realsense2_camera rs_camera.launch align_depth:=1 # Quick

# or
# W=640; H=360; Wd=$W; Hd=$H; fps=15; roslaunch realsense2_camera rs_camera.launch align_depth:=1 enable_color:=1 enable_depth:=1 color_fps:=${fps} color_height:=${H} color_width:=${W} depth_fps:=${fps} depth_height:=${Hd} depth_width:=${Wd} infra_fps:=${fps} infra_height:=${Hd} infra_width:=${Wd}
```


## Intel ZR300

This is librealsense for the ZR300 camera.

### Compilation librealsense (version 1, legacy branch)

```bash
git clone -b legacy https://github.com/IntelRealSense/librealsense
cd librealsense
# git checkout legacy # v1.12.1
mkdir build
cd build
cmake -DCMAKE_BUILD_TYPE=Release ..
make -j $(nproc)
make install
```

### Compilation of the ROS workspace

```
# In the workspace your_ws
cd YOUR_CATKIN_WORKSPACE/src
git clone https://github.com/nlyubova/realsense.git
cd YOUR_CATKIN_WORKSPACE
catkin_make
```

### How to use camera ZR300

Run

```
roslaunch realsense_camera zr300_nodelet_rgdb.launch
```

## Kinect Azure

The camera can easily be installed on Ubuntu >= 18.

For Ubuntu 16, the following instructions were adapted from
[here](https://tianyusong.com/2019/11/13/how-to-install-azure-kinect-sdk-on-ubuntu-16-04/). There
is a bit of hacks...

### Installation of K4A

```bash
sudo dpkg --add-architecture amd64
sudo apt update
```

We installed from required libraries:

```bash
sudo apt install pkg-config
sudo apt install ninja-build
sudo apt install doxygen
sudo apt install clang
sudo apt install gcc-multilib
sudo apt install g++-multilib
sudo apt install python3
sudo apt install nasm
sudo apt install libgl1-mesa-dev
sudo apt install libsoundio-dev
sudo apt install libvulkan-dev
sudo apt install libx11-dev
sudo apt install libxcursor-dev
sudo apt install libxinerama-dev
sudo apt install libxrandr-dev
sudo apt install libusb-1.0-0-dev
sudo apt install libssl-dev
sudo apt install libudev-dev
sudo apt install mesa-common-dev
sudo apt install uuid-dev
```

We will install the SDK in `~/local`, but feel free to adapt.

```bash
mkdir ~/local
mkdir ~/local/lib
```

We also need a newer version of cmake (which we install in `~/local/bin/cmake`):

```bash
cd ~/Downloads
wget https://cmake.org/files/v3.11/cmake-3.11.0-Linux-x86_64.sh
bash cmake-3.11.0-Linux-x86_64.sh --skip-license --prefix=${HOME}/local
```

We need to download library `libk4a` which we copy in `~/local/lib`:

```bash
cd ~/Downloads
wget https://packages.microsoft.com/ubuntu/18.04/prod/pool/main/libk/libk4a1.2/libk4a1.2_1.2.0_amd64.deb
dpkg-deb -x libk4a1.2_1.2.0_amd64.deb deb-k4a
cd deb-k4a/usr/
cp -rP lib/x86_64-linux-gnu/lib* ~/local/lib
```

Library `libk4a` requires a specific version of `libstd6` which we
install in `~/local/lib`:

```bash
cd ~/Downloads
wget http://security.ubuntu.com/ubuntu/pool/main/g/gcc-8/libstdc++6_8.4.0-1ubuntu1~18.04_amd64.deb
dpkg-deb -x libstdc++6_8.4.0-1ubuntu1~18.04_amd64.deb deb-std6
cp -P deb-std6/usr/lib/x86_64-linux-gnu/lib* ~/local/lib
```

### Compilation of the SDK

We can now compile the SDK:

```bash
cd ~/Downloads
git clone --recursive https://github.com/microsoft/Azure-Kinect-Sensor-SDK.git
cd Azure-Kinect-Sensor-SDK/
git checkout release/1.3.x
mkdir build
cd build
~/local/bin/cmake -DCMAKE_BUILD_TYPE=RELEASE -DCMAKE_INSTALL_PREFIX=${HOME}/local ..
make
# make -j $(nproc) # sometimes fail...
make install
```

### Compilation of the ROS workspace

```bash
cd  ~/azure_ws/src
git clone https://github.com/microsoft/Azure_Kinect_ROS_Driver
cd Azure_Kinect_ROS_Driver/

# Fix some gcc issues
sed -i "s/std::atomic_int64_t/std::atomic<std::int64_t>/" include/azure_kinect_ros_driver/k4a_ros_device.h
sed -i "s/std::atomic_uint64_t/std::atomic<std::uint64_t>/" include/azure_kinect_ros_driver/k4a_ros_device.h

# Fix some issues
sed -i "s/cxx_std_11//" CMakeLists.txt
```

Then can be compiled with

```bash
source /opt/ros/kinetic/setup.bash
cd  ~/azure_ws
catkin_make -DCMAKE_PREFIX_PATH="${HOME}/local/lib/cmake;${CMAKE_PREFIX_PATH}" -DCMAKE_CXX_FLAGS="-std=c++11 ${CMAKE_CXX_FLAGS}"
```
