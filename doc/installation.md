<img src="./banner.jpg" alt="" width="100%"/>

# Installation of the Idiap Human Perception system

## Create a conda environment

Remove from your `.bashrc` all lines containing `source ...`, `conda
activate ...`, `export PYTHONPATH=...`, or `export
LD_LIBRARY_PATH=...`

Remove pip's cache to avoid any cache to be used.

```bash
rm -r ${HOME}/.cache/pip
```

If you don't have conda yet, install it with:

```bash
wget https://repo.anaconda.com/miniconda/Miniconda2-latest-Linux-x86_64.sh
bash Miniconda2-latest-Linux-x86_64.sh
```

Then create the environment. Adapt to your NVIDIA drivers, see [this
table for
compatibility](https://docs.nvidia.com/deploy/cuda-compatibility/index.html#binary-compatibility):

```bash
eval "$(${HOME}/miniconda2/bin/conda shell.bash hook)"
# conda env create -f conda/ihper-cu92.yaml
conda env create -f conda/ihper-cu100.yaml
# conda env create -f conda/ihper-cu101.yaml
```

We don't need the conda environment in the rest of the installation.


## Dependencies

### CUDA and cuDNN

CUDA and cuDNN should be installed on your machine (to compile
OpenPose). Choose the version that suit your NVIDIA drivers [on this
webpage](https://developer.nvidia.com/cuda-toolkit-archive), and the
correponding cuDNN version
[here](https://developer.nvidia.com/rdp/cudnn-archive).


### ROS

Install ROS on your machine following [these
instructions](http://wiki.ros.org/ROS/Installation).

From now on, source ROS in the `.bashrc`:

```
source /opt/ros/kinetic/setup.bash
# source /opt/ros/melodic/setup.bash
```


### ROS octomap (Pepper only)

If you plan to use Pepper 1.7 as a platform, the following packages
are required:

```bash
# Adapt for melodic
sudo apt install ros-kinetic-octomap
sudo apt install ros-kinetic-octomap-ros
sudo apt install ros-kinetic-octomap-msgs
sudo apt install ros-kinetic-octomap-mapping
sudo apt install ros-kinetic-naoqi-libqi
sudo apt install ros-kinetic-naoqi-libqicore
sudo apt install ros-kinetic-ddynamic-reconfigure
```


## Folders

We install the dependencies in `${HOME}/perception_deps`:

```bash
mkdir ${HOME}/perception_deps
```

## Install OpenPose

[OpenPose](https://github.com/CMU-Perceptual-Computing-Lab/openpose)
will be installed in `${HOME}/perception_deps/openpose/build/install`

```bash
cd ${HOME}/perception_deps
git clone https://github.com/CMU-Perceptual-Computing-Lab/openpose
cd openpose
mkdir build
cd build
cmake -DCMAKE_INSTALL_PREFIX=${PWD}/install -DDOWNLOAD_BODY_25_MODEL=ON -DDOWNLOAD_BODY_COCO_MODEL=ON -DDOWNLOAD_FACE_MODEL=OFF -DDOWNLOAD_HAND_MODEL=OFF ..
```

Check that the OpenCV version found by cmake is the one shipped with ROS:

```
-- Found OpenCV: /opt/ros/kinetic (found version "3.3.1")
```

Then compile with

```
make
make install
```

Check that the compilation went well by testing the demo code (Press `ESCAPE` to quit the demo):

```bash
cd ${HOME}/perception_deps/openpose
./build/examples/openpose/openpose.bin -net_resolution 256x-1
```

## Install OpenHeadPose

[OpenPose](https://gitlab.idiap.ch/software/openheadpose) will be
installed in `${HOME}/perception_deps/openheadpose/build/install`

```bash
cd ${HOME}/perception_deps
git clone https://gitlab.idiap.ch/software/openheadpose
cd openheadpose
mkdir build
cd build
cmake -DCMAKE_INSTALL_PREFIX=${PWD}/install -DOpenPose_PATH=${DEPS}/openpose/build/install ..
make
make install
```

Check that the compilation went well with the demo code:

```
cd ${HOME}/perception_deps/openheadpose
./build/examples/demo -M ${HOME}/perception_deps/openpose/models/ -E models/ -P BODY_25 --draw-head-pose --draw-openpose-limbs
```

## Install Python dependencies

```bash
cd ${HOME}/perception_deps
git clone https://github.com/idiap/pytopenface.git
git clone https://github.com/idiap/archs.git # Pepper only
git clone https://github.com/idiap/apkit.git # Pepper only
```

## Catkin workspace

The catkin workspace will be in `${HOME}/ihper_ws`.

```bash
PWS="${HOME}/ihper_ws"
mkdir -p ${PWS}/src
catkin_init_workspace ${PWS}/src
```

```bash
git -C ${PWS}/src clone https://github.com/idiap/particle_person_tracker.git
git -C ${PWS}/src clone https://github.com/idiap/perception_msgs.git
git -C ${PWS}/src clone https://github.com/idiap/person_manager.git
git -C ${PWS}/src clone https://github.com/idiap/audio_perception.git # Pepper only
```

And compile it with:

```bash
catkin_make -C ${PWS} -DOpenPose_PATH=${HOME}/perception_deps/openpose/build/install -DOpenHeadPose_PATH=${HOME}/perception_deps/openheadpose/build/install
```
