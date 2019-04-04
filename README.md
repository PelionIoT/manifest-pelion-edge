Repo manifest for Pelion gateway
=============================================
This repository provides Repo manifests to setup the Yocto build system for the Pelion gateway.

Quickstart
----------
    $ mkdir build; cd build
    $ repo init -u ssh://git@github.com/armmbed/manifest-gateway-ww.git -b <branch>
    $ repo sync -j8
    $ cd build-env
Follow the [automated build instructions](https://github.com/ARMmbed/wigwag-build-env) of the build environment repository.

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

**4.  Build an image:**

There are two ways to build the image depending on how much manual effort and time you want to invest.

 1. By manually setting up your host environment for Yocto and calling the usual bitbake commands.  Those instructions are located [here](https://github.com/ARMmbed/meta-gateway-ww/blob/master/BUILD.md). 
 or 
 2. Installing Docker and using the included build-env/Makefile which loads a Docker image and runs bitbake inside the Docker image. Those instructions are located [here](https://github.com/ARMmbed/wigwag-build-env). 

 Beyond the automation, an additional benefit of 2 is that the Docker image contains a known working host environment with all the necessary build tools preinstalled. There's no magic in the Docker image or the Makefile, rather it implements a known working environment and scripted set of build steps to produce a viable firmware image.  The Makefile still calls bitbake to perform the build, it just does so inside the Docker container.


**5. Flash your image:**

Instructions for flashing the image to an SD card can be found [here](https://github.com/ARMmbed/meta-gateway-ww/blob/master/FLASH.md).



Troubleshooting
---------------
1. See the wigwag-build-env [README](https://github.com/ARMmbed/wigwag-build-env/blob/master/README.md) for solutions to some common build errors.
