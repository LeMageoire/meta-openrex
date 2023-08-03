# meta-openrex
FEDEVEL Yocto Project for OpenRex

# How to compile & use software for OpenRex 

Here you will find some basic info about how to start with YOCTO and OpenRex. If you follow the steps below, you should be able to compile the source code. 


In case you would like to know more about YOCTO & How To Use It, for example how to create, modify, compile and use meta-openrex or how to create your own custom layer, have a look at OpenRex website: http://www.imx6rex.com/open-rex/software/

### 1) Install the repo utility
    mkdir ~/bin
    curl http://commondatastorage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
    chmod a+x ~/bin/repo

### 2) Get the YOCTO project
    cd
    mkdir fsl-community-bsp
    cd fsl-community-bsp
    PATH=${PATH}:~/bin
    repo init -u https://github.com/Freescale/fsl-community-bsp-platform -b jethro

### 3) Add openrex support - create manifest 
    cd ~/fsl-community-bsp/
    mkdir -pv .repo/local_manifests/

Copy and paste this into your Linux host machine 

    cat > .repo/local_manifests/imx6openrex.xml << EOF
    <?xml version="1.0" encoding="UTF-8"?>
    <manifest>
    
      <remote fetch="git://github.com/FEDEVEL" name="fedevel"/>
    
      <project remote="fedevel" revision="jethro" name="meta-openrex" path="sources/meta-openrex">
        <copyfile src="openrex-setup.sh" dest="openrex-setup.sh"/>
      </project>
    </manifest>
    EOF

### 4) Sync repositories
    repo sync

### 5) Add OpenRex meta layer into BSP
    source openrex-setup.sh

# Building images
    cd ~/fsl-community-bsp/

### Currently Supported machines <machine name>
Here is a list of 'machine names' which you can use to build OpenRex images. Use the 'machine name' based on the board you have:


    imx6q-openrex
    imx6s-openrex
    
### Setup and Build Console image
    MACHINE=<machine name> source setup-environment build-openrex
    MACHINE=<machine name> bitbake core-image-base

Example:


    MACHINE=imx6q-openrex source setup-environment build-openrex
    MACHINE=imx6q-openrex bitbake core-image-base

### Setup and Build Toolchain    
    MACHINE=<machine name> bitbake core-image-base -c populate_sdk
    
### Setup and Build FSL GUI image
    MACHINE=<machine name> source fsl-setup-release.sh -b build-x11 -e x11
    MACHINE=<machine name> bitbake fsl-image-gui

# Creating SD card
Output directories and file names depend on what you build. Following example is based on running 'bitbake core-image-base':


    umount /dev/sdb?
    gunzip -c ~/fsl-community-bsp/build-openrex/tmp/deploy/images/imx6q-openrex/core-image-base-imx6q-openrex.sdcard.gz > ~/fsl-community-bsp/build-openrex/tmp/deploy/images/imx6q-openrex/core-image-base-imx6q-openrex.sdcard
    sudo dd if=~/fsl-community-bsp/build-openrex/tmp/deploy/images/imx6q-openrex/core-image-base-imx6q-openrex.sdcard of=/dev/sdb
    umount /dev/sdb?
    
# Testing it on OpenRex (initial uBoot runs from SPI)
To test your uBoot on SD card, plug in the card which you have just created into an OpenRex board. Reset the OpenRex board (press "Reset" button), interrupt the uBoot countdown (press "Spacebar" or the "Enter" key) and run following command:

    mw.l 0x020d8040 0x2840; mw.l 0x020d8044 0x10000000; reset

# Updating OpenRex
TODO
        
    
