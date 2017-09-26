OpenPTrack fork for the paper:
Real-time marker-less multi-person 3D pose estimation in RGB-Depth camera networks.

This branch in unstable - coming soon the stable version!
This is for Ubuntu 16.04, if you are interested in the 14.04 please have a look [here](https://github.com/marketto89/open_ptrack/tree/14.04)

INSTALLATION INSTRUCTIONS:

REQUIREMENTS: 
* ROS Kinetic
* CUDA 8 
* cuDNN 5.1

Installation (for now, just for Kinect v2 sensors, I am assuming ROS workspace is workspace/ros/src inside your home folder):

1. install libfreenect2 & iai_kinect2
```bash
cd
cd workspace 
git clone https://github.com/openptrack/libfreenect2
cd libfreenect2
git checkout 1604
cd depends/
sudo apt-get install -y git cmake cmake-curses-gui libxmu-dev libxi-dev libgl1-mesa-dev dos2unix xorg-dev libglu1-mesa-dev libtool automake libudev-dev libgtk2.0-dev pkg-config libjpeg-turbo8-dev libturbojpeg libglewmx-dev
./install_ubuntu.sh
sudo ln -s /usr/lib/x86_64-linux-gnu/libturbojpeg.so.0.1.0 /usr/lib/x86_64-linux-gnu/libturbojpeg.so
cd ../examples/protonect/
mkdir build && cd build
cmake ..
make -j4
sudo make install
echo '# ATTR{product}=="Kinect2"
SUBSYSTEM=="usb", ATTR{idVendor}=="045e", ATTR{idProduct}=="02c4", MODE="0666"
SUBSYSTEM=="usb", ATTR{idVendor}=="045e", ATTR{idProduct}=="02d8", MODE="0666"
SUBSYSTEM=="usb", ATTR{idVendor}=="045e", ATTR{idProduct}=="02d9", MODE="0666"' > ~/90-kinect2.rules
sudo mv ~/90-kinect2.rules /etc/udev/rules.d/90-kinect2.rules
cd /home/$USER/workspace/ros/src
git clone https://github.com/openptrack/iai_kinect2
cd iai_kinect2
git checkout 1604
```
2. install ceres solver
```bash
sudo apt-get install cmake libgoogle-glog-dev libatlas-base-dev libeigen3-dev libsuitesparse-dev -y --force-yes
mkdir /tmp/ceres_install
cd /tmp/ceres_install
git clone https://ceres-solver.googlesource.com/ceres-solver
cd ceres-solver
git fetch --tags
git checkout tags/1.9.0
cd ..
mkdir ceres-bin
cd ceres-bin
cmake ../ceres-solver
make -j8
make test
sudo make install
sudo rm -R /tmp/ceres_install
```
3. install calibration_toolkit
``` bash
cd ~/workspace/ros/src
git clone https://github.com/iaslab-unipd/calibration_toolkit
 cd calibration_toolkit
 git fetch origin --tags
git checkout tags/v0.2
```
4. install rtpose_wrapper (wrapper for openpose)
```bash
cd ~/workspace/ros/src
git clone https://bitbucket.org/mcarraro/rtpose_wrapper
cd rtpose_wrapper
git checkout integration
sudo apt-get --assume-yes install libprotobuf-dev libleveldb-dev libsnappy-dev libhdf5-serial-dev protobuf-compiler libboost-all-dev libgflags-dev libgoogle-glog-dev liblmdb-dev
make all -j8
```
5. install this repository
```bash
cd ~/workspace/ros/src
git clone https://github.com/marketto89/open_ptrack
git checkout 16.04
sudo apt-get install -y ros-kinetic-qt-build
source ~/.bashrc
cd open_ptrack && git checkout 16.04
cd ../..
```
6. compile everything!
```
cd
cd workspace/ros
catkin_make
```
7. follow the normal OpenPTrack wiki to set-up the network (taking care about setting to true the enable_pose flag in the .bashrc)
* [Time Synchronization](https://github.com/OpenPTrack/open_ptrack/wiki/Time-Synchronization)
* [Pre-calibration](https://github.com/OpenPTrack/open_ptrack/wiki/Pre-Calibration-File-Configuration) - be careful to leave enable_pose: true in the camera_network.yaml


POSE ESTIMATION FROM SINGLE CAMERA
```bash
roslaunch tracking single_camera_tracking_node.launch enable_pose:=true 
```

POSE ESTIMATION FROM CAMERA NETWORK
```bash
<calibrate the network>
<from master computer>: roslaunch tracking tracking_node.launch
<from each detector>: roslaunch detection detection_node_<camera_name>.launch enable_pose:=true
```


