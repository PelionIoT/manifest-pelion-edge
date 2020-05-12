# Repository manifest for Pelion Edge

This repository provides repository manifests to set up the Yocto build system for Pelion Edge. The steps helps you generate a Pelion Edge enabled Yocto image in developer mode.

## Prerequisites

Installed programs:

- Ubuntu 18.04.
- [Docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/).
- [build-essential](https://askubuntu.com/questions/398489/how-to-install-build-essential).
- [repo](https://source.android.com/setup/build/downloading#installing-repo).

Before you can begin using this repository, you must:

1. Be sure your user is added to the Docker group.
1. Configure `user.name` and `user.email` because the `repo` tool is built on top of Git:

   ```
   $ git config --global user.name "Mona Lisa"
   $ git config --global user.email "email@example.com"
   ```

1. [Generate your build machine's SSH key and add it to your GitHub account](https://help.github.com/en/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent) because the recipes of various metalayers refer to git@github.com.
1. Download `mbed_cloud_dev_credentials.c` to connect to your Pelion account:

   1. In the [Portal](https://portal.mbedcloud.com/), go to **Device Identity** > **Certificates**.
   1. Create a developer certificate.
   1. Download the C file.

1. [Initialize the manifest tool](https://github.com/ARMmbed/manifest-tool/blob/master/README.md#quick-start) to create `update_default_resources.c`. This authorizes firmware updates to the device.

Note: To unlock the rich node features, such as gateway logs and the gateway terminal in the Pelion web Portal, you will have to do the following -
   * Pass the command line parameter `-V 42fa7b48-1a65-43aa-890f-8c704daade54` to the manifest-tool while generating `update_default_resources.c`, and
   * Also by default the features are not enabled in your Pelion web Portal account. Please reach out to service continuity team at ARM to request them to enable Edge Gateway features in your account.

## Quick start

```
$ mkdir build; cd build
$ repo init -u ssh://git@github.com/armpelionedge/manifest-pelion-edge.git -b <branch>
$ repo sync -j8
$ cd build-env
$ cp ../../mbed_cloud_dev_credentials.c . # Copy the above generated certificates to build-env directory
$ cp ../../update_default_resources.c .
$ make
```

## Detailed steps

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

1. Copy the `mbed_cloud_dev_credentials.c` and `update_default_resources.c` to the `build-env` directory:

   The Make file puts these certificates in the correct spots for the build as long as they are in `build-env`.

1. Start the build with `make`:

   ```
   $ make
   ```

   The built image is in the build directory under `poky/build/tmp/deploy/images/raspberrypi3/`.

1. [Flash your image](https://github.com/armPelionEdge/meta-pelion-edge/blob/master/FLASH.md).

1. Modify the login credentials:

   There is only one login user by default, root. The default password is set to `redmbed`. To modify that, follow these [instructions](https://github.com/armPelionEdge/meta-pelion-edge/blob/master/BUILD.md#root-password).

## Troubleshooting

Please see the meta-pelion-edge [GitHub issues](https://github.com/armPelionEdge/meta-pelion-edge/issues) for solutions to common build errors.

## Contributing

Please make all contributions against our development branch, `dev`. Merged changes will then be pulled into master.
