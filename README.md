<img src="./doc/banner.jpg" alt="" width="100%"/>

# Idiap Human Perception system (IHPER)

These instructions describe how to install the Idiap Human Perception
system. Some instructions are redundant with other README files from
other repositories: disregard them and only follow these instructions.

Here is an illustration of the functionnalities of the system:

<div align="center">

[![](http://img.youtube.com/vi/Cfsc0zXAMVU/0.jpg)](http://www.youtube.com/watch?v=Cfsc0zXAMVU "")

</div>

## Installation and usage

- [How to install IHPER](./doc/installation.md)
- [The IHPER user guide](./doc/usage.md)

This code was tested on:

- Ubuntu 16.04 + ROS Kinetic (Audio + Video parts)
- Ubuntu 18.04 + ROS Melodic (Video part)
- Debian 9 (stretch) + ROS Melodic (Video part)

## Some remarks

- The sound source localization system only works on ROS Kinetic (the audio of Pepper does not work on Melodic due to incompatible Boost version, see [this issue](https://github.com/ros-naoqi/pepper_robot/issues/51#issuecomment-510919412))
- This code works with Python 2.7 (due to ROS Kinetic and Melodic)
- We use absolute paths in these instructions for simplicity, but feel free to adapt them to your installation
- "Pepper only" is indicated when an instruction applies for the sound source localization on Pepper. If you plan to only use the visual part, you don't need to install the audio part.

## Citation

The system was described in the following paper:

```
@article{canevet2020mummer,
  author = {Olivier Can{\' e}vet and Weipeng He and Petr Motlicek and Jean-Marc Odobez},
  title = {The {MuMMER} data set for Robot Perception in Multi-party {HRI} Scenarios},
  booktitle = {29th {IEEE} International Conference on Robot and Human Interactive Communication, {RO-MAN} 2020},
  publisher = {{IEEE}},
  year = {2020}
}
```
