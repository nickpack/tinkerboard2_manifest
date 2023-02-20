# ASUS Tinker Board 2 Linux Manifest

## TLDR: The Tinker Board 2 is a *completely useless piece of shit* because of a severe lack of software support - buy an alternative board - This project is my attempt at building a stable minimal OS image by cobbling together and fixing the crap that has been put out by ASUS

Having recently accquired a Tinker Board 2 thinking it would be a good replacement for a Pi 4 (given the lack of availability),
to say I am properly pissed off by ASUS' complete lack of software support is very much an understatement, my own fault for not doing adequate enough research and making assumptions based on spec sheets I guess, on paper it looked good.

This project has two simple goals (Whether I meet them or not will depend on the state of what has been open sourced and the limited time I have to spend on it):

1. Fix the absolute shite that ASUS have published on the 'TinkerBoard2' project on github so that people can actually build images, as unsurprisingly what they have put out doesn't even work.
2. Produce a minimal Debian image without all the bloat that the TinkerOS images come with - most of us using SBCs do not want a GUI and a load of desktop focussed crap, nor do we want to spend time having to remove packages we don't use - my goal is to have a barebones image that can be built upon.

## Current status

I have only just started, so will update this when I have some progress, for now all I have fixed is the broken build script.

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

## Maintainer

Nick Pack <nick@nickpack.com>
