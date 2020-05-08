# Repository manifest for Pelion Edge

This repository provides repository manifests to set up the Yocto build system for Pelion Edge. The steps helps you generate a Pelion Edge enabled Yocto image in developer mode.

## Requirements

- Ubuntu 18.04.
- [Docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/).
- [build-essential](https://askubuntu.com/questions/398489/how-to-install-build-essential).
- Be sure your user is added to the Docker group.
- [repo](https://source.android.com/setup/build/downloading#installing-repo)
- As the recipes of various meta layers refer to git@github.com, it is essential your build machine's SSH key is saved in your GitHub account. Follow these [instructions](https://help.github.com/en/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent) on how to generate SSH key and add to Github account.
- `mbed_cloud_dev_credentials.c` – Used to connect to your Pelion account. Create a developer certificate in the [Portal](https://portal.mbedcloud.com/) under **Device Identity** > **Certificates**, and download the C file.
- `update_default_resources.c` – Used to authorize firmware updates to device and created by [initializing the manifest tool](https://github.com/ARMmbed/manifest-tool/blob/master/README.md#quick-start).

Note: To unlock the rich node features, such as gateway logs and gateway terminal in the Pelion web portal, please pass the command line parameter `-V 42fa7b48-1a65-43aa-890f-8c704daade54` to manifest-tool while generating the update_default_resources.c.

## Quickstart

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

1. Install the repository.

   Download the `repo` tool:

   ```
   $ mkdir ~/bin
   $ PATH=~/bin:${PATH}
   $ curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
   $ chmod a+x ~/bin/repo
   ```
   Because the `repo` tool is built on top of Git, you must configure `user.name` and `user.email`:

   ```
   $ git config --global user.name "Mona Lisa"
   $ git config --global user.email "email@example.com"
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

1. Copy the `mbed_cloud_dev_credentials.c` and `update_default_resources.c` to the `build-env` directory:

   The Make file puts these certificates in the correct spots for the build as long as they are in `build-env`.

1. Start the build with `make`:

   ```
   $ make
   ```

   Once it successfully completes, the built image will be located in the build directory under `poky/build/tmp/deploy/images/raspberrypi3/`

1. Flash your image:

   Follow these [instructions](https://github.com/armPelionEdge/meta-pelion-edge/blob/master/FLASH.md).

1. Login credentials:

   There is only one login user by default, root. The default password is set to 'redmbed'. If you wish to modify that then follow these [instructions](https://github.com/armPelionEdge/meta-pelion-edge/blob/master/BUILD.md#root-password).

## Troubleshooting
Please see the meta-pelion-edge [GitHub issues](https://github.com/armPelionEdge/meta-pelion-edge/issues) for solutions to common build errors.

## Contributing
Please make all contributions against our development branch, `dev`. Merged changes will then be pulled into master.
