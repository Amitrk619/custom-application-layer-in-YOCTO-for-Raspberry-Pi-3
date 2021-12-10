# Custom application layer in YOCTO for RaspberryPi-3
Adding custom application layer in YOCTO for Raspberry Pi 3

Prerequisite:

1. Raspberry pi 3 board
2. SD card
3. Ubuntu-based Host system with a minimum 50 GB free space and at least 4GB of RAM with internet connection.

Install Dependencies

$ sudo apt update

$ sudo apt upgrade

$ sudo apt-get install gawk wget git-core diffstat unzip texinfo gcc-multilib build-essential chrpath socat cpio python python3 python3-pip python3-pexpect xz-utils debianutils iputils-ping libsdl1.2-dev xterm

# Donwload Layers

Download the Yocto Project poky reference distribution:

$ git clone git clone git://git.yoctoproject.org/poky -b dunfell

![](https://i.postimg.cc/MpzXrvqq/1.png)


Download Raspberry pi meta layer

$ cd poky

$ git clone https://git.openembedded.org/meta-openembedded -b dunfell

You will need a BSP layer to support the Raspberry Pi boards. So Yocto Project is providing a meta-raspberrypi layer that contains information related to raspberry pi boards that are required during the build.

Download meta-raspberrypi layer from the poky directory

$ git clone https://git.yoctoproject.org/meta-raspberrypi -b dunfell

![](https://i.postimg.cc/3rBw032q/2.png)

# Configure Build

Start by creating a build environment (to give us access to tools like “bitbake”). Note that the oe-init-build-env script will create an initial “build” folder and automatically change directories to it.

Run the following command

$ source oe-init-build-env

![](https://i.postimg.cc/P5fVfQTc/3.png)

Set the target machine

Edit conf/local.conf and change machine value to raspberrypi3

$ nano conf/local.conf

MACHINE ??= “raspberrypi3-64”

MAGE_FSTYPES = "tar.xz ext3 rpi-sdimg"

![](https://i.postimg.cc/66YvJrbT/4.png)

Now, We have to add the meta-raspberrypi layer in the build/conf/bblayers.conf file. This file store information of all meta-layer path which used by the bitbake

$ nano conf/bblayers.conf

meta-raspberrypi 

![](https://i.postimg.cc/9fYfP2vz/5.png)

# Start bitbake to build the image

We are ready to start the bitbake to build the image.core-image-minimal recipe provides the minimal bootable image for the Raspberry pi boards.

$ bitbake core-image-minimal-dev

![](https://i.postimg.cc/YSDCDRCw/6.png)

Bitbake will take several hours to complete the build process. it depends on your internet speed and CPU performance. One big advantage of the Yocto Project is that it builds everything in stages and layers. If you make any changes (e.g. add a layer, change to a different image, tweak kernel settings), subsequent builds will take far less time. This speeds up development process when you are trying to add low-level support in Linux.
Generated image present at the tmp/deploy/images/raspberrypi4-64/ directory.


# Flash SD card with generated image

After complete the build process bitbake will generate the core-image-minimal-raspberrypi4-64.rpi-sdimg file at tmp/deploy/images/raspberrypi4-64/ directory

Now just plug the SD card into your host machine and follow the below steps

To check the SD card partition run sudo fdisk -l and replace x with your sd card partition id.

$ sudo umount /dev/sdx

Boot
Insert SD card into raspberry pi and start on the board.

root is the username and password is not required


# Create a custom application layer in Yocto for Raspberry pi 3

Initialize the build environment: source poky/oe-init-build-env build > /dev/null

![](https://i.postimg.cc/zvjMptqC/7.png)

Run the command 'bitbake-layers create-layer meta-custom' to create a new layer automatically

Change directory and edit the example.bb file or create a new one example one is created automatically. meta-custom/recipes-example/example$ nano example_0.1.bb

![](https://i.postimg.cc/FH45yB3B/10.png)

Add the newly created and dependent layers in conf/bblayer.config

![](https://i.postimg.cc/520jVykJ/8.png)

For making this application part of the image

Add the following line in the build/conf/local.conf file.

IMAGE_INSTALL_append = “custom”

![](https://i.postimg.cc/5tWcmkzr/9.png)

Start bitbake to build the image

$ bitbake core-image-minimal-dev

Flash SD card with generated image as shown above
