[![License](https://img.shields.io/badge/license-MIT-blue.svg)](http://choosealicense.com/licenses/mit/)

# BeagleBone Black CAN bus communication via socketcan
This tutorial is based on a Debian Stretch distribution from 2017-12-12 on the Beaglebone Black. The description is independent of the used CAN cape.
I used this CAN cape: [BeagleBone Serial Cape from Logic Supply](http://www.logicsupply.com/de-de/cbb-serial)

## Setup Debian image
* Download latest BeagleBone Black image from [BeagleBone Images](https://beagleboard.org/latest-images), e.g. the Debian Stretch IoT (non-GUI).
* Following the [software update guide](https://beagleboard.org/getting-started#update).
  * For short: Program image to the sd card by using a sd card programming utility.
  * Hold the user/reset button pressed, while powering the board.
* Connect to the BeagleBone Black via ssh.
  * hostname: **beaglebone**
  * user: **debian**
  * password: **temppwd**
* Next important step is to change the password via ```passwd```.
* Update the packages: ```sudo apt-get update```
* Upgrade the distribution: ```sudo apt-get upgrade```

## SocketCAN drivers
The BeagleBone should already have the SocketCAN drivers installed, so to use the CAN bus you just need to set the bitrate 125 kBit/s and bring up the CAN0 interface with:
```
sudo ip link set can0 up type can bitrate 125000
ifconfig
```
You should see now the can0 interface, similar as shown in the following example.
```
can0: flags=193<UP,RUNNING,NOARP>  mtu 16
        unspec 00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00  txqueuelen 10  (UNSPEC)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
        device interrupt 166
```
## CAN utilities
if ```candump``` is not available, it looks like the CAN utilities are not installed. In this case follow the instructions, otherwise just skip this chapter.
* ```git clone https://github.com/linux-can/can-utils```
* ```cd can-utils```
* ```apt-get install automake```
* ```./autogen.sh```
* ```./configure```
* ```make```
* ```make install```

## Network interface
* Update the network interface for CAN, to start it automatically during startup: ```sudo nano /etc/network/interfaces```
* Add the following at the end:
```
allow-hotplug can0
iface can0 can static
    bitrate 125000
```

## Test
To test whether its working or not, lets dump all CAN messages to the console: ```candump can0```
Abort it with strg + c.
