# Installing ROS2 foxy

Follow the guide on [https://index.ros.org/doc/ros2/Installation/Foxy/Linux-Install-Debians/\#setup-sources](https://index.ros.org/doc/ros2/Installation/Foxy/Linux-Install-Debians/#setup-sources)

**Note \(1\):** at _Setup Sources_ step you might get an error message by curl

To avoid this do upfront

```text
sudo rm -rf /usr/lib/libcurl*
sudo apt install curl
```

**Note \(2\):** at _Install ROS2 package_ step use

```text
sudo apt install ros-foxy-ros-base
```

Desktop is not needed on a headless system!  

## Detailed instrustion for NavQ demo image

### System requirements

```text
~$ sudo apt update
~$ sudo apt install cmake python3-pip gradle python3-colcon-common-extensions gradle
~$ pip3 install --user pyros-genmsg
```

###  Setting up PX4 Ros com for ROS2 Foxy

If you have no _src_ directory yet on NavQ's home directory just create it ~$ mkdir src 

```text
~$ cd src 
~/src$ git clone --recursive https://github.com/eProsima/Fast-RTPS.git -b 1.8.x FastRTPS-1.8.2
~/src$ cd FastRTPS-1.8.2
~/src/FastRTPS-1.8.2$ mkdir build
~/src/FastRTPS-1.8.2$ cd build 
~/src/FastRTPS-1.8.2/build$ cmake -DTHIRDPARTY=ON -DSECURITY=ON .. 
~/src/FastRTPS-1.8.2/build$ make 
~/src/FastRTPS-1.8.2/build$ sudo make install
```

```text
cd ~/src
~/src$ git clone --recursive https://github.com/eProsima/Fast-RTPS-Gen.git -b v1.0.4 Fast-RTPS-Gen
~/src$ cd Fast-RTPS-Gen
~/src/Fast-RTPS-Gen$ unset TERM
~/src/Fast-RTPS-Gen$ ./gradlew assemble
~/src/Fast-RTPS-Gen$ sudo su
~/src/Fast-RTPS-Gen# unset TERM
~/src/Fast-RTPS-Gen# ./gradlew install
~/src/Fast-RTPS-Gen# exit
~/src/Fast-RTPS-Gen$
```

```text
cd ~/
mkdir -p ~/px4_ros_com_ros2/src

git clone https://github.com/PX4/px4_ros_com.git ~/px4_ros_com_ros2/src/px4_ros_com
git clone https://github.com/PX4/px4_msgs.git ~/px4_ros_com_ros2/src/px4_msgs
```

{% hint style="warning" %}
Building px4\_ros\_com requires a lot of ram, enable a swap disk is recommended\# see [https://linuxize.com/post/create-a-linux-swap-file/](https://linuxize.com/post/create-a-linux-swap-file/) Execute building PX4 ROS2
{% endhint %}

```text
~$ ./px4_ros_com_ros2/src/px4_ros_com/scripts/build_ros2_workspace.bash
```

  
 

