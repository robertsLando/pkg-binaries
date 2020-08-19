# Pkg Binaries

[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com)

<a href="https://www.buymeacoffee.com/MVg9wc2HE" target="_blank"><img src="https://www.buymeacoffee.com/assets/img/custom_images/orange_img.png" alt="Buy Me A Coffee" style="height: 41px !important;width: 174px !important;box-shadow: 0px 3px 2px 0px rgba(190, 190, 190, 0.5) !important;-webkit-box-shadow: 0px 3px 2px 0px rgba(190, 190, 190, 0.5) !important;" ></a>

Collection of NodeJS precompiled binaries to use with [pkg][207006e9]. Please submit a pull request if you have others that are not present here.

**ALL BINARIES HAVE BEEN MOVED TO RELEASES ASSETS. YOU WILL FIND ALL BINARIES [HERE](https://github.com/robertsLando/pkg-binaries/releases/tag/v1.0.0)**

## Usage

Just download the desired binary and copy it into the correct `.pkg-cache/vMAJOR.MINOR` version subfolder. After this run your `pkg` command and now it will find the desired binary in cache.

Example with `arm64` `node v8.11.3` `linux`:

```bash
# Must set -O to preserve the correct file metadata
wget https://github.com/robertsLando/pkg-binaries/releases/download/v1.0.0/fetched-v14.0.0-linux-armv6 -O fetched-v8.11.3-linux-arm64
sudo mv fetched-v8.11.3-linux-arm64 ~/.pkg-cache/v2.6
```

**Note:** Finding the correct `.pkg-cache/vMAJOR.MINOR` folder

The naming of the cache folder is dependent on the version of `pkg-fetch` that is installed. Usually the naming convention is `vMAJOR.MINOR`. You can easily find out which version is installed by running `npm ls pkg-fetch` (or `yarn why pkg-fetch` for yarn users).

It will print something like the following to the terminal:

````
some@space:~/project$ npm ls pkg-fetch
project@1.10.2 /Users/laurin/project
└─┬ pkg@4.4.9
  └── pkg-fetch@2.6.9
 ````
 
 According to the output you must download the binary into `~/.pkg-cache/v2.6`.

> **ATTENTION**

Once you have placed the file in `.pkg-cache` folder check that the output of `file` command gives you the correct interpreter:

```bash
pi@NanoPi-NEO-Plus2:~/.pkg-cache/v2.6$ file fetched-v8.11.3-linux-arm64
fetched-v8.11.3-linux-arm64: ELF 64-bit LSB shared object, ARM aarch64, version 1 (GNU/Linux), dynamically linked, interpreter /lib/ld-, for GNU/Linux 3.7.0, BuildID[sha1]=02bf3444ecc520c4da40e89cbfbf6831e3a205ea, not stripped
```

Sometimes when using `wget` it could be downloaded as `HTML text` file and it wouldn't work.

## Utils

In utils folder you will find a bash script `package.sh` that I have created to package my nodejs application.

Copy the script in your app directory and edit it with your appName and the destination folder of the pkg compiled output.

```bash
# EDIT THIS WITH YOUR VALUES
APP="appName"
PKG_FOLDER="pkg"
```

It also automatically scan `node_modules` folder and adds required `.node` files that pkg is not able to package.

You will find all your files inside the destination folder and they will be also packaged inside a .zip file with appName and version choosed.

If you want to make things more easy add a script in your `package.json` file:

```json

"scripts": {
  "start": "sudo node ./bin/www", //default to start the application
  "package": "sudo chmod +x package.sh && ./package.sh"
}

```

Than simply run `npm run package` to start the script

## Compilation guide

### Using Docker

This allows to compile nodejs pkg binary for `arm32` (armv7l, armv6) and `arm64` archs from a `x86_64`.

Steps

```bash
# Clone this repo
git clone https://github.com/robertsLando/pkg-binaries.git
cd pkg-binaries
# Build the required binary
chmod +x build.sh
./build.sh
# Follow build steps and wait for nodejs to be compiled (WILL TAKE AROUND 24 HOURS!)
```

### Manually

This requires to run commands in a CPU with the required build ARCH

Here is a guide to manually compile a nodejs binary from source.
Usually pkg automatically compiles this binary if it doesn't find them in his resources but this process may fail and this is how to do it by your self. In this example I'm compiling nodejs 8 LTS using a device with Linux with architecture arm64 (to check the information about your device run the comand `uname -a`).

1. Install required build tools:

    `sudo apt-get install build-essential`

2. Than clone node:

    `git clone https://github.com/nodejs/node.git`

3. Checkout to the desired version:

    `cd node`
    `git checkout v8.11.3`

4. Create the patch file inside the node dir and paste the content from the patch file you find on pkg-fetch github inside [patch][a9bdf3ee] directory (<https://raw.githubusercontent.com/zeit/pkg-fetch/master/patches/node.v8.11.3.cpp.patch)>

    `sudo nano node.v8.11.3.cpp.patch` (Ctrl+Maiusc+V - Ctrl+X - Y)

    `git apply node.v8.11.3.cpp.patch`

    `./configure`

    `make` (this takes many minutes, even hours in some devices)

5. Finally copy the binary:

    `cp node ~/.pkg-cache/v2.6/fetched-v8.11.3-linux-arm64`

[207006e9]: https://github.com/zeit/pkg "Zeit Pkg"

[a9bdf3ee]: https://github.com/zeit/pkg-fetch/tree/master/patches "Patch"
