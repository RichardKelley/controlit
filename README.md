# ControlIt! - A Whole Body Operational Space Control Middleware #

**Table of Contents**

  * [Introduction](#introduction)
  * [Installation](#installation)
    * [Create a ROS Workspace](#create-a-ros-workspace)
    * [Add ControlIt! to the ROS Workspace](#add-controlit-to-the-ros-workspace)
    * [Enable Gazebo Plugins and Models](#enable-gazebo-plugins-and-models)
    * [Install RBDL](#install-rbdl)
    * [Install YAML 0.3.0](#install-yaml-030)
    * [Install Additional Dependencies](#install-additional-dependencies)
    * [Compile ControlIt!](#compile-controlit)
    * [Configure Shared Memory](#configure-shared-memory)
  * [Run Demonstration Simulations](#run-demonstration-simulations)

## Introduction ##

This is repository contains ControlIt!, a middleware that supports the Whole
Body Operational Space Control algorithm. It employs a plugin-based modular
software architecture so other whole body control algorithms could be
supported as well.

## Installation ##

### Create a ROS Workspace ###

This step is only necessary if you do not already have a ROS Catkin workspace that you want to use for ControlIt! development. The commands below create a workspace called "controlit_workspace". Feel free to customize it to another name.

    $ source /opt/ros/indigo/setup.bash
    $ mkdir -p ~/controlit_workspace/src
    $ cd ~/controlit_workspace/src
    $ catkin_init_workspace
    $ cd ..
    $ catkin_make

Edit ~/.bashrc and add the following line at the bottom:

    # Setup ControlIt! ROS workspace
    source $HOME/controlit_workspace/devel/setup.bash
    # source `rospack find controlit_environment_config`/setup.sh
    # source `rospack find controlit_models`/setup.sh
    # source `rospack find controlit_configs`/setup.sh

Note that all but one of the lines above are commented out. The bottom three lines will be uncommented after checking out all of the repositories.

### Add ControlIt! to the ROS Workspace ###

The following instructions assume you are using a ROS workspace located in ~/controlit_workspace.

Using your favorite text editor, create the text file ~/controlit_workspace/.rosinstall containing the following text (Note: to avoid permission errors, you need to upload your public SSH key to your [github](https://github.com/settings/ssh "github SSH settings") and [bitbucket](https://confluence.atlassian.com/display/BITBUCKET/Add+an+SSH+key+to+an+account "Instructions for adding SSH key to bitbucket account") accounts).

    - setup-file: {local-name: /opt/ros/indigo/setup.sh}
    - git: {local-name: src/controlit, uri: 'git@github.com:liangfok/controlit.git', version: master}
    - git: {local-name: src/controlit_models, uri: 'git@github.com:liangfok/controlit_models.git', version: master}
    - git: {local-name: src/controlit_configs, uri: 'git@github.com:liangfok/controlit_configs.git', version: master}
    - hg: {local-name: src/rbdl2, uri: 'ssh://hg@bitbucket.org/cfok/rbdl2'}
    - git: {local-name: src/ros_shared_memory_interface, uri: 'git@github.com:liangfok/ros_shared_memory_interface.git', version: master}

If you are unable or unwilling to upload your public SSH key to your github and bitbucket accounts, use the following settings to use HTTP rather than SSH to download the software:

    - setup-file: {local-name: /opt/ros/indigo/setup.sh}
    - git: {local-name: src/controlit, uri: 'https://github.com/liangfok/controlit.git', version: master}
    - git: {local-name: src/controlit_models, uri: 'https://github.com/liangfok/controlit_models.git', version: master}
    - git: {local-name: src/controlit_configs, uri: 'https://github.com/liangfok/controlit_configs.git', version: master}
    - hg: {local-name: src/rbdl2, uri: 'https://bitbucket.org/cfok/rbdl2'}
    - git: {local-name: src/ros_shared_memory_interface, uri: 'https://github.com/liangfok/ros_shared_memory_interface.git', version: master}

Exit the text editor and update your workspace:

    $ cd ~/controlit_workspace
    $ wstool update

You can check the status of your workspace by executing:

    $ cd ~/controlit_workspace
    $ wstool info

### Enable Gazebo Plugins and Models ###

Now that you have checked out all of the repositories, you can enable the Gazebo plugins and models that are used by ControlIt!. Edit ~/.bashrc and update the section related to ControlIt! to be the following:

    # Setup ControlIt! ROS workspace
    source $HOME/controlit_workspace/devel/setup.bash
    source `rospack find controlit_environment_config`/setup.sh
    source `rospack find controlit_models`/setup.sh
    source `rospack find controlit_configs`/setup.sh

Close and re-open your terminals for the settings to apply.

### Install RBDL ###

RBDL provides various dynamics and kinematics algorithms that are used by
ControlIt! in the robot model. Install it by executing the following commands:

    $ cd ~/controlit_workspace/src/rbdl2
    $ mkdir build && cd build
    $ cmake ../ -DRBDL_STORE_VERSION=ON -DCMAKE_BUILD_TYPE=Release
    $ make && sudo make install

### Install YAML 0.3.0 ###

ControlIt! currently uses yaml-cpp 0.3.0. If you're using Ubuntu 14.04 or higher, you will need to downgrade to this version. Here's how to do that:

First download yaml-cpp's source code from here: https://code.google.com/p/yaml-cpp/downloads/list

Then install it:

    $ tar zxvf yaml-cpp-0.3.0.tar.gz
    $ cd yaml-cpp
    $ mkdir build
    $ cd build
    $ cmake -DBUILD_SHARED_LIBS=ON ..
    $ make
    $ sudo make install

### Install Additional Dependencies ###

There are several additional packages that need to be installed using apt-get:

    $ sudo apt-get install python-simplejson libmuparser-dev libzmq-dev libfltk1.1-dev libfltk1.1

## Compile ControlIt! ##

To compile ControlIt!, execute:

    $ cd ~/controlit_workspace
    $ rm -rf build devel              // optional, for a clean re-compilation of everything
    $ catkin_make

## Configure Shared Memory ##

Many of ControlIt!'s demos use shared memory to communicate between the whole body controller and the simulator. To prevent needing to allocate this shared memory each time you restart your computer (and having to type your sudo password), we recommend that you permanently allocate sufficient shared memory by executing the command below:

    $ rosrun shared_memory_interface set_shared_memory_size_persistent 536870912

# Run Demonstration Simulations #

For tutorials on how to use ControlIt!, see: http://robotcontrolit.com/tutorials