Repo manifest for WW gateway
=============================================
This repository provides Repo manifests to setup the Yocto build system for the WW gateway.

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

    $ repo sync

**4.  Initialize the build environment.**

    $ source ./poky/oe-init-build-env

**5.  Build an image:**

    $ bitbake console-image

**6. Flash your image:**

Be sure to install `bmap-tools`:

    $ sudo apt-get install bmap-tools

Flash SD:

    $ sudo bmaptool copy --bmap image.bmap build/console-image.wiz.gz /dev/sdb
