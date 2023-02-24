# ASUS Tinker Board 2 Linux Manifest

## TLDR: The Tinker Board 2 is a *completely useless piece of shit* because of a severe lack of software support - buy an alternative board - This project is my attempt at building a stable minimal OS image by cobbling together and fixing the crap that has been put out by ASUS

Having recently accquired a Tinker Board 2 thinking it would be a good replacement for a Pi 4 (given the lack of availability),
to say I am properly pissed off by ASUS' complete lack of software support is very much an understatement, my own fault for not doing adequate enough research and making assumptions based on spec sheets I guess, on paper it looked good.

This project has two simple goals (Whether I meet them or not will depend on the state of what has been open sourced and the limited time I have to spend on it):

1. Fix the absolute shite that ASUS have published on the 'TinkerBoard2' project on github so that people can actually build images, as unsurprisingly what they have put out doesn't even work.
2. Produce a minimal Debian image without all the bloat that the TinkerOS images come with - most of us using SBCs do not want a GUI and a load of desktop focussed crap, nor do we want to spend time having to remove packages we don't use - my goal is to have a barebones image that can be built upon.

## Current status

I have only just started, so will update this when I have some progress.

I don't have a Tinker Board 2S to test this on, only a standard 2 so I haven't tested any of the eMMC related stuff...

I was hoping that producing a minimal image would be as simple as adding another target in debian/ubuntu-build service with less package selections, I was wrong - there is a lot of inherited stuff and hacky installs of things all over the place.

Turns out that switching more or less everything off in the buildroot does not actually produce images without the packages present, I am not really sure why buildroot is involved at all to be honest as it is not being utilised as designed. 

There is a bash script that does a bunch of manual installs during the build (https://github.com/nickpack/tinkerboard2_debian/blob/linux4.19-rk3399-debian10/mk-rootfs-buster.sh#L108), yocto is next on the investigation list.

So far, I have managed to get the base image down to a couple of hundred MB (though it is unstable AF so I won't publish the changes yet) by doing the following:

* Remove includes that pull in packages that aren't required from the buildroot board config (https://github.com/nickpack/tinkerboard2_buildroot/blob/linux4.19-rk3399-debian10/configs/rockchip_tinker_board_2_defconfig)
* Switch off all non-essential packages in the buildroot config for the board (BR2_PACKAGE_ variables in https://github.com/nickpack/tinkerboard2_buildroot/blob/linux4.19-rk3399-debian10/configs/rockchip_tinker_board_2_defconfig)
* Hack out all of the apt installs from the script https://github.com/nickpack/tinkerboard2_debian/blob/linux4.19-rk3399-debian10/mk-rootfs-buster.sh#L108
* Delete all non-essential package lines from https://github.com/nickpack/tinkerboard2_debian/blob/linux4.19-rk3399-debian10/ubuntu-build-service/buster-desktop-arm64/customization/package-lists/linaro.list.chroot
* Comment out the includes in https://github.com/nickpack/tinkerboard2_yocto-build-conf/blob/linux4.19-rk3399-debian10/rockchip-rk3399-tinker_board_2.conf

More investigation required, I haven't come across yocto before so I need to go further down this path.

### Changes/Fixed issues
* Fixed build.sh issues so that a full image can be built with the default 'desktop' target, all working as expected on a standard Tinker Board 2.

## Building an image

### Source code gathering

```bash
repo init -u https://github.com/nickpack/tinkerboard2_manifest.git -b linux4.19-rk3399-debian10
```

```bash
repo sync
```

Wait for ages... Coffee time!

### Building an image in the provided docker builder

```bash
./docker_builder/docker-builder-run.sh
```

Once the container build is complete, a docker exec session is opened to the chroot in the container, from there you can run:

```bash
./build.sh
```

That should build everything, there are a miriad of options you can give the build script, I will document these later, for now you'll have to root around in the source.

Once your build is complete, you will find the output images in ./rockdev - if you want to flash a complete image, it is called *sdboot.img*

## Maintainer

Nick Pack <nick@nickpack.com>
