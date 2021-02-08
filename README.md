# Resources
Follow the [Getting Started Robotis e-Manual](https://emanual.robotis.com/docs/en/platform/turtlebot3/quick-start/) for your particular ROS distro. The below instructions assume ROS Melodic.

We recommend following the Robotis e-Manual as a primary instruction set and referring to this guide for additional information. This guide is intended to supplement the Robotis e-Manual. This guide roughly follows the Robotis e-Manual and offers useful information that may not be obvious in the e-Manual. Links are pasted liberally here to direct the reader to the appropriate spot in the e-Manual.

# Setup
SBC Selection
The Jetson Nano is not listed as a compatible device on the [compatible devices page](https://emanual.robotis.com/docs/en/platform/turtlebot3/compatible_devices/), but other forum participants claim that they have successfully used a Nano for the Turtlebot. We should also be able to use the Xavier NXs with no problem.

## [PC Setup](https://emanual.robotis.com/docs/en/platform/turtlebot3/quick-start/#pc-setup)
Set up your PC according to the directions. We recommend using an Ubuntu machine, but a VM may work instead.

**Make sure you click "Melodic" at the top of the page to get instructions for the appropriate ROS distro!!!** The default is Kinetic, which is not what the instructions use.

## [SBC Setup](https://emanual.robotis.com/docs/en/platform/turtlebot3/sbc_setup/#sbc-setup)
**Make sure you click "Melodic" at the top of the page to get instructions for the appropriate ROS distro!!!** The default is Kinetic, which is not what the instructions use.

### Basic Jetson Nano Setup
First, set up the Jetson Nano:

The Jetson Nano does not come with WiFi out of the box. Enable Wifi by following the [Jetson Nano + Intel Wifi Card Installation Guide](https://www.jetsonhacks.com/2019/04/08/jetson-nano-intel-wifi-and-bluetooth/)

Follow the [Getting Started with Jetson Nano Developer Kit guide](https://developer.nvidia.com/embedded/learn/get-started-jetson-nano-devkit#intro)

Note: there are two recommended power options:
1. Power via the 5V / 2A micro-usb connector. Make sure to remove the jumper on J48. 
2. Power via the J25 barrel plug. Make sure to remove the jumper on J48.

For more info on power options, see the [Jetson Nano Developer Kit User Guide](https://developer.download.nvidia.com/assets/embedded/secure/jetson/Nano/docs/NVIDIA_Jetson_Nano_Developer_Kit_User_Guide.pdf?M8GNYQFtf3wNF9ZLly1Q6ZqwUegxcHjewQG8YI1N2wbT06DMBRedRNK9T6Wqz0vV1Wn2n9GaSWK3QSsdAp1k0YwhLT5V52dWekwktX5p8rKpzBoiahIDRmyK7cF3_oeQoTGUo5Aox_OueoUB_lv7UjzQLN7luT_MDskCT19sfFuUqpwOGTmjvXyItfzjtwiaKzkP0Wjz) or this JetsonHacks article titled [Jetson Nano - Use More Power!](https://www.jetsonhacks.com/2019/04/10/jetson-nano-use-more-power/#:~:text=There%20are%20three%20ways%20to,accept%20up%20to%203%20Amps.)



### SSH Setup
You may wish to SSH into the Jetson Nano to install ROS and other tools as well as operate the Nano/Turtlebot remotely. To do this, perform the basic setup so that the Nano automatically connects to WiFi. Get the Nano's IP address and use PuTTy from your Windows device thereafter to ssh into the Nano (or use whatever SSH client is available on your computer if not PuTTy; from Linux and Mac OS you can do this from the terminal).

### Install ROS and other tools
Next, install ROS and other tools.


Install terminator
```
sudo apt-get update
sudo apt-get install terminator
```

Install geany
```
sudo apt install geany
```

Install ROS1 Melodic
```
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
wget http://packages.ros.org/ros.key -O - | sudo apt-key add -
sudo apt-get update
sudo apt-get install ros-melodic-desktop-full
echo "source /opt/ros/melodic/setup.bash" >> ~/.bashrc
source ~/.bashrc
sudo apt-get install -y chrony ntpdate build-essential python-rosinstall git python-rosdep python-rosinstall python-rosinstall-generator python-wstool build-essential
sudo ntpdate ntp.ubuntu.com
sudo rosdep init
rosdep update
```

Make the catkin_ws
```
mkdir -p catkin_ws/src
cd catkin_ws
rosdep update
rosdep install --from-paths src --ignore-src -r -y
```

### Install Turtlebot packages
```
cd ~/catkin_ws/src
git clone https://github.com/ROBOTIS-GIT/hls_lfcd_lds_driver.git
git clone https://github.com/ROBOTIS-GIT/turtlebot3_msgs.git
git clone https://github.com/ROBOTIS-GIT/turtlebot3.git
```

Install dependent packages
```
sudo apt-get install ros-melodic-rosserial-python ros-melodic-tf
```

Reboot!
```
sudo reboot
```

Build packages
```
source /opt/ros/melodic/setup.bash
cd ~/catkin_ws && catkin_make
source devel/setup.bash
```

### Set USB settings
```
rosrun turtlebot3_bringup create_udev_rules
```

### Network Configuration
Follow the instructions as are documented in both the [PC Setup](https://emanual.robotis.com/docs/en/platform/turtlebot3/quick-start/#pc-setup) and [SBC Setup](https://emanual.robotis.com/docs/en/platform/turtlebot3/sbc_setup/#sbc-setup).


## OpenCR Setup
Following the [Shell Script install instructions](https://emanual.robotis.com/docs/en/platform/turtlebot3/opencr_setup/) for the waffle

1. Connect the OpenCR to the Rasbperry Pi using the micro USB cable.
2. Install required packages on the Raspberry Pi to upload the OpenCR firmware.
```
sudo dpkg --add-architecture armhf
sudo apt-get update
sudo apt-get install libc6:armhf
```
3. Depending on the platform, use either burger or waffle for the OPENCR_MODEL name. We have a waffle so use waffle.
```
export OPENCR_PORT=/dev/ttyACM0
export OPENCR_MODEL=waffle
rm -rf ./opencr_update.tar.bz2
```
4. Download the firmware and loader, then extract the file.
```
wget https://github.com/ROBOTIS-GIT/OpenCR-Binaries/raw/master/turtlebot3/ROS1/latest/opencr_update.tar.bz2 
tar -xvf opencr_update.tar.bz2 
```
5. Upload firmware to the OpenCR.
```
cd ./opencr_update
./update.sh $OPENCR_PORT $OPENCR_MODEL.opencr
```
6. See the robotis page for what the terminal output should look like for a successful firmware upload.
7. If the upload fails, try again and also try resetting using the instructions on the robotis page. Resetting and multiple tries were necessary in our case.

Return to [Basic Operation](https://emanual.robotis.com/docs/en/platform/turtlebot3/basic_operation/#basic-operation) when the Turtlebot is assembled to test the motors.


## Hardware Setup
Follow the [Hardware Assembly instructions](https://emanual.robotis.com/docs/en/platform/turtlebot3/hardware_setup/). 
Notes to make your life easier and avoid headaches: 
- Refer to the video AND the assembly instructions. The instructions alone are not sufficient.
- Take note of the different assembly patterns for the modular pieces that make up each level. Not all levels are assembled with the same pattern.
- Make sure that motor 1 is L and motor 2 is R
- The Jetson Nano can be powered via the GPIO pins. Supply +5V to pin 4 and GND to pin 6. For a pinout of the Jetson Nano, see [jetsonhacks' Nano pinout](https://www.jetsonhacks.com/nvidia-jetson-nano-j41-header-pinout/)

## Test
### OpenCR Test
Follow the [OpenCR Test instructions](https://emanual.robotis.com/docs/en/platform/turtlebot3/opencr_setup/#opencr-test) to test the OpenCR's motor control after assembling the robot.

### Teleop Test
1. ssh into the tbot
```
ssh tbotXX@XXX.XXX.XX.XXX
```
2. On the tbot, run
```
catkin_make 
source devel/setup.bash
export TURTLEBOT3_MODEL=waffle_pi
```
3. Run the bringup launch with
```
roslaunch turtlebot3_bringup turtlebot3_robot.launch
```
4. In a new terminal window, ssh into the tbot and run
```
roslaunch turtlebot3_teleop turtlebot3_teleop_key.launch
```