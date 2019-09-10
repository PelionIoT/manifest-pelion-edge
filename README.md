# Repository manifest for Pelion Edge

This repository provides repository manifests to set up the Yocto build system for Pelion Edge. Please make all contributions against our development branch, `dev`. Merged changes will then be pulled into master.

## Requirements

- Ubuntu 18.04.
- [Docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/).
- [build-essential](https://askubuntu.com/questions/398489/how-to-install-build-essential).
- Be sure your user is added to the Docker group.

## Quickstart

```
$ mkdir build; cd build
$ repo init -u ssh://git@github.com/armpelionedge/manifest-pelion-edge.git -b <branch>
$ repo sync -j8
$ cd build-env
$ cp <needed credentials>. #See Step 4 below for creation.
$ make
```

## Detailed steps

1. Install the repository.

   Download the repository script:
   
   ```
   $ mkdir ~/bin
   $ PATH=~/bin:${PATH}
   $ curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
   $ chmod a+x ~/bin/repo
   ```
   
1. Initialize a repository client:
   
   1. Create an empty directory to hold the build directory:
      
      ```
      $ mkdir build
      $ cd build
      ```
   
   1. Download the manifest in this repository:
      
      ```
      $ repo init -u ssh://git@github.com/armpelionedge/manifest-pelion-edge.git
      ```
   
   Your directory now contains a `.repo` directory.

1. Fetch all the repositories:

   ```
   $ repo sync -j8
   ```
   
1. Create and copy the [following certificates](https://github.com/armPelionEdge/meta-pelion-edge/blob/dev/BUILD.md#credentials-keys-and-certificates) to the `build-env` directory:

   1. `mbed_cloud_dev_credentials.c` – Used to connect to your Pelion account. Create a developer certificate in the [Portal](https://portal.mbedcloud.com/) under **Device Identity** > **Certificates**, and download the C file.
   1. `Update_default_resources.c` – Used to authorize firmware updates to device and created by [initializing the manifest tool](https://github.com/ARMmbed/manifest-tool/blob/master/README.md#quick-start).
   1. `upgradeCA.cert` – [Created with OpenSSL](https://github.com/armPelionEdge/meta-pelion-edge/blob/dev/BUILD.md#upgrade-ca-certificate).
   1. `rot_key.pem` – [Created with OpenSSL](https://github.com/armPelionEdge/meta-pelion-edge/blob/dev/BUILD.md#secure-boot-trusted-world-root-of-trust).
   1. `mbl-fit-rot-key.key` – [Created with OpenSSL](https://github.com/armPelionEdge/meta-pelion-edge/blob/dev/BUILD.md#u-boot-verified-boot-fit-image-signing-key-and-certificate).
   1. `mbl-fit-rot-key.crt` – [Created with OpenSSL](https://github.com/armPelionEdge/meta-pelion-edge/blob/dev/BUILD.md#u-boot-verified-boot-fit-image-signing-key-and-certificate).

   The Make file puts these certificates in the correct spots for the build as long as they are in `build-env`.

1. Start the build with `make`:
   
   ```
   $ make
   ```
   
1. When it fails due to the private repository (mbedtls-psa / crypto), access the docker bash shell, and apply the following patch:

   ```
   $ make bash
   ```
   
   This gives you access to the bash shell in the docker build.

   1. Edit the following:

      ```
      $ vi poky/meta-pelion-edge/recipes-wigwag/mbed-edge-examples/mbed-edge-examples.bb
      ```
   
      1. Change the version: SRCREV = "0.9.0"
      1. Remove this file reference: file://0003-examples-0.8.0-should-use-mbed-edge-0.8.0.patch
  
   1. Edit the following: 
   
      ```
      $ vi ~/poky/meta-pelion-edge/recipes-wigwag/mbed-edge-core/mbed-edge-core.inc
      ```
      
      1. Change the version: SRCREV = "0.9.0"
      1. Remove this file reference: file://0003-fix-compile-error-when-building-for-Release.patch
   
   1. Clean the state of the module with bitbake:
   
      ```
      $ source ~/poky/oe-init-build-env
      $ bitbake -c cleansstate mbed-edge-core-rpi3
      $ exit
      ```

1. After you return to your normal shell, you can complete the build by executing `make`:
   
   ```
   $ make
   ```

1. Flash your image:

   To flash console-image-raspberry3.wic:
   
   - To flash on macOS, use `dd`. This example assumes the SD card is enumerated as `/dev/diskX`, and you should verify your device's path:
   
      ```
      $ gunzip -c console-image-raspberrypi3.rootfs.wic.gz | sudo dd bs=4m of=/dev/diskX iflag=fullblock oflag=direct conv=fsync status=progress
      ```
      
      Note: To use on Windows, you can use the [Etcher](https://www.balena.io/etcher/) application. (The UI is self explanatory: Choose the file to flash and the destination SD card, and then click **Flash**.) In some cases, using Etcher results in significant time savings over using `dd`.
      
   - To flash on Linux, use `dd`. You can use `lsblk` to find the name of your SD card block device:
   
      ```
      $ gunzip -c console-image-raspberrypi3.rootfs.wic.gz | sudo dd bs=4M of=/dev/mmcblkX conv=sync
      ```

## Troubleshooting

Please see the `build-pelion-edge` [README](https://github.com/armpelionedge/build-pelion-edge/blob/master/README.md) for solutions to common build errors.
