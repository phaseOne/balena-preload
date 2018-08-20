# resin-preload
[![npm](https://img.shields.io/npm/v/resin-preload.svg?style=flat-square)](https://npmjs.com/package/resin-preload)
[![npm license](https://img.shields.io/npm/l/resin-preload.svg?style=flat-square)](https://npmjs.com/package/resin-preload)
[![npm downloads](https://img.shields.io/npm/dm/resin-preload.svg?style=flat-square)](https://npmjs.com/package/resin-preload)

Script for preloading resin.io OS images (`.img`) with a user application container.

Using this will allow images with supervisor version above 1.0.0 to run the user application without connectivity, and without the need to download the container.

## Warning

In order to preload images that use the overlay2 Docker storage driver (like
nvidia jetson tx2 for example), you need to load the `overlay` Linux module:

```sh
sudo modprobe overlay
```

For other images you will need to have the `aufs` module loaded.


## Deprecation

The standalone mode described below (resin-preload) is now deprecated.
It will be removed in a future release.
You should use [resin-cli](https://www.npmjs.com/package/resin-cli) instead.

Install [resin-cli](https://www.npmjs.com/package/resin-cli) and run
`resin help preload`.


## Install via [npm](https://npmjs.com)

```sh
$ npm install --global resin-preload
```

<!-- MarkdownTOC -->

- [Requirements](#requirements)
- [Command Line Usage](#command-line-usage)
    - [Example](#example)
    - [Additional Options](#additional-options)
    - [Custom Splash Screen](#custom-splash-screen)
- [Module usage](#module-usage)
- [Contributing](#contributing)
- [Known Issues](#known-issues)
    - [Speed Issues For Flasher Images on macOS](#speed-issues-for-flasher-images-on-macos)
    - [Version Compatibility](#version-compatibility)
    - [BTRFS Support](#btrfs-support)

<!-- /MarkdownTOC -->


## Requirements

- [Node](https://nodejs.org)
- [Docker](https://www.docker.com) tested on 1.12.6 and up but 17.04 or up is recommended especially on macOS, [docker-toolbox](https://www.docker.com/products/docker-toolbox) is not supported.
Older versions of resin-preload do support docker-toolbox, see [Version Compatibility](#version-compatibility) below.

## Command Line Usage

```
Usage: resin-preload [options]

Options:

  --app            Application ID (required)
  --img            Disk image (or Edison zip file) to preload into (required)
  --api-token      API token (required, or api-key)
  --api-key        API key (required, or api-token)
  --commit         App commit to be preloaded (default: latest)
  --api-host       API host (default: "https://api.resin.io", the TLD will also be used for registry2 requests)
  --splash-image   PNG Image for custom splash screen

  --dont-check-arch          Disables check for matching architecture in image and application

  --help, -h       Display resin-preload usage
  --version, -v    Display resin-preload version

Environment variables:

  The following option flags can also be set
  via the corresponding environment variables:

  --app                               APP_ID
  --img                               IMAGE
  --api-token                         API_TOKEN
  --api-key                           API_KEY
  --commit                            COMMIT
  --api-host                          API_HOST
  --splash-image                      SPLASH_IMAGE
  --dont-check-arch                   DONT_CHECK_ARCH

Example:

  resin-preload --app 123456 --api-token "xxxx..." --img /path/to/resin-os.img

```

If using environment variables, this program requires the following options to be set:

  * `IMAGE`: The path to the OS image you downloaded for the `APP_ID` you want to target.
  * `APP_ID`: ID of the App that will be assigned to the device. It can be extracted from the URL in the Resin dashboard,
     for instance `https://dashboard.resin.io/apps/2167` means the `APP_ID` is `2167`.
  * `API_TOKEN`: Authentication token for Resin, taken from the [preferences page](https://dashboard.resin.io/preferences/details). 


### Example

**NOTE:** Before running the example below, make sure that you have successfully pushed to the app and
that the `Application commit` is the correct version of the code you intend to preload.

Download an OS image from the Resin dashboard and then run:

1) Using option flags for configuration:

```bash
$ resin-preload --app 123456 --api-token "XXX..." --img /path/to/resin.img
```

2) Using environment variables for configuration:

```bash
# Set the environment variables
$ export API_TOKEN=... # copy from dashboard preferences
$ export APP_ID=... # id of your application (you can see it on dashboard URL when you visit your app page)
$ export IMAGE=/path/to/resin.img

# As all required options have been set via environment variables,
# there's no need to pass any options, just run
$ resin-preload
```

The `/path/to/resin.img` file, will now have the latest version of your application preloaded.

### Additional Options

* Alternatively to `API_TOKEN`, you can use an `API_KEY` variable with an API key from a config.json file or the [SDK](https://github.com/resin-io/resin-sdk/blob/master/DOCUMENTATION.md#resin.models.application.getApiKey).
* `API_HOST`: Address of the Resin API. If unsure, use `https://api.resin.io`.
* `SPLASH_IMAGE`: Path to a custom splash image, to copy into the preloaded OS image

### Custom Splash Screen

At this time, the custom splash screen image needs to be a PNG.
For more details on splash images, see:
  - [The Resin.io Notes – What are we working on / Revamping Splash Screens](https://forums.resin.io/t/what-are-we-working-on-the-resin-io-notes/414/7)
  - [Documenting the custom boot splash screen](https://github.com/resin-io/docs/issues/155)
  - [resin-io/resin-plugin-img](https://github.com/resin-io/resin-plugin-img)

## Module usage

Please check bin/resin-preload

## Contributing

### Install from github

 * Clone this repo: `git clone git@github.com:resin-io/resin-preload.git`
 * Change directory: `cd resin-preload`
 * Install dependencies: `npm i`
 * Run resin-preload: `./bin/resin-preload`

### Issues

 If you encounter any problem, you can [open an issue](https://github.com/resin-io/resin-preload/issues)

## Known Issues

### Speed Issues For Flasher Images on macOS

Docker on macOS has [some speed issues with volumes](https://github.com/docker/for-mac/issues/77).
This makes this script slow, especially with Flasher Images.

### Version Compatibility

This version will only work for Resin OS versions 1.2 and later.
For versions earlier than 1.2 you will need to checkout commit `5d6d4607bffc98acdf649ce5328e2079dfb9c3d9` of this repo and then follow the steps below. 

### BTRFS Support

Since Docker for Mac removed support for the BTRFS storage driver (see [docker/for-mac/issues/388](https://github.com/docker/for-mac/issues/388)), preloading images prior to Resin OS 2.0 will require the older [Docker toolbox](https://docs.docker.com/toolbox/toolbox_install_mac/) setup with [VirtualBox](https://www.virtualbox.org/) to function properly.
