Repo manifest for WW gateway
=============================================
This repository provides Repo manifests to setup the Yocto build system for the WW gateway.

Quickstart
----------
    $ mkdir build; cd build
    $ repo init -u ssh://git@github.com/armmbed/manifest-gateway-ww.git -b <branch>
    $ repo sync -j8
    $ cd build-env
    $ cp <path_to_file>/mbed_cloud_dev_credentials.c .
    $ make

Getting Started
---------------
**1.  Install Repo.**

Download the Repo script:

    $ mkdir ~/bin
    $ PATH=~/bin:${PATH}
    $ curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
    $ chmod a+x ~/bin/repo

**2.  Initialize a Repo client.**

Create an empty directory to hold the build directory:

    $ mkdir gateway-ww-build
    $ cd gateway-ww-build

Tell Repo to download the manifest in this repo:

    $ repo init -u ssh://git@github.com/armmbed/manifest-gateway-ww.git

Your directory should now contain a .repo directory.

**3.  Fetch all the repositories:**

    $ repo sync -j8

**5.  Build an image:**

There are two ways to build the image depending on how much manual effort and time you want to invest: 1. by manually setting up your host environment for Yocto and calling the usual bitbake commands, or 2. installing Docker and using the included Makefile which loads a Docker image and runs bitbake inside the Docker image.  Beyond the automation, an additional benefit of 2 is that the Docker image contains a known working host environment with all the necessary build tools preinstalled.

There's no magic in the Docker image or the Makefile, rather it implements a known working environment and scripted set of build steps to produce a viable firmware image.  The Makefile still calls bitbake to perform the build, it just does so inside the Docker container.

Manual Steps:

    a. [Install Yocto system requirements](https://www.yoctoproject.org/docs/2.6.1/ref-manual/ref-manual.html#ref-manual-system-requirements)
    b. [Install additional requirements]
       1. sudo dpkg --add-architecture i386
       2. sudo apt-get update
       3. sudo apt-get install -y --no-install-recommends g++-multilib libssl-dev:i386 libcrypto++-dev:i386 zlib1g-dev:i386
       4. sudo dpkg-reconfigure dash
          i.  This reconfigures Ubuntu/Debian to use bash as the non-interactive shell.  At the prompt, select No.
    c. run bitbake
    $ cd poky
    $ TEMPLATECONF=meta-gateway-ww/conf source oe-init-build-env
    $ cp <path_to_file>/mbed_cloud_dev_credentials.c meta-gateway-ww/recipes-wigwag/mbed-edge-core/files/
    $ bitbake console-image

With Docker and Make:

    a. [Install Docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
    b. run make
    $ cd build-env
    $ cp <path_to_file>/mbed_cloud_dev_credentials.c .
    $ make

The built image will be located in the build directory (`gateway-ww-build` in this example), under `poky/build/tmp/deploy/images/raspberrypi3/`. The file name will be `console-image-raspberry3.SOMETHING` (the ending will vary based on the value of IMAGE_FSTYPES in your local.conf).

**6. Flash your image:**

To flash console-image-raspberry3.wic:

To flash on Mac OS X, use dd.  This example assumes the SD card is enumerated as /dev/diskX and you should verify your device's path.

        $ sudo dd bs=4m if=console-image-raspberrypi3.wic of=/dev/diskX conv=sync

Alternatively, you can use the [Etcher](https://www.balena.io/etcher/) app (the UI is self explanatory - simply choose the file to flash, the destination SD card, and then click Flash). In some cases, using Etcher results in significant time savings over using dd.

Troubleshooting
---------------
1. See the wigwag-build-env [README](https://github.com/ARMmbed/wigwag-build-env/blob/master/README.md) for solutions to some common build errors.
