# yocto_raspberrypi
**Yocto build for Raspberry Pi**  
Version/branch: _Kirkstone_

## Clone it
To clone the repo, pull all the git submodules, while doing so:  
`git clone --recurse-submodules https://github.com/Michal-Sitarz/yocto_raspberrypi.git`

To prepare your host machine to use Yocto build system, follow: https://docs.yoctoproject.org/ref-manual/system-requirements.html  
Most modern Ubuntu distros should have all the basic tools included already.

## Build it

After cloning the repo, build it using Yocto build tools. 

To do that initialize build environment:  
`source poky/oe-init-build-env build-dir`

### _Running the build_

Use bitbake to run the build:  
`bitbake rpi-test-image`  
_Note:_ This may take a long time!

When finished, navigate and list the directory with built images:  
`cd build-dir/tmp/deploy/images/raspberrypi4 && ls`

Upload image to an SD card:  
`bzcat tmp/deploy/images/raspberrypi4/rpi-test-image-raspberrypi4-64.wic.bz2 | sudo dd of=/dev/mmcblk0 bs=1M status=progress conv=fsync && sync`  
This command will unpack the archive with an image on the fly and copy it to the SD card bit-by-bit.

## Use it

### _Accessing the Linux on Raspberry Pi_

Default credentials: _user_ is `root` and there's _no password_ by default!

To utilize UART access, use your favourite serial console client, e.g. picocom:  
`picocom -b 115200 /dev/ttyUSB0`  
`login: root`

To utilize SSH access, use built-in SSH client:  
`ssh root@192.168.0.XXX`  
_Note:_ To find out the IP address of the Raspberry Pi use tools, like: `nmap` or `arp-scan`.



## Process explained

Step-by-step guide to create the whole build from scratch.

_Note:_ for bare minimum support to have a Raspberry Pi running, only those 2 layers are required:  
- poky
- meta-raspberrppi

### _Getting basic ingredients_

Create project directory:  
`mkdir yocto_raspberrypi && cd yocto_raspberrypi`

Get poky reference:  
`git clone git://git.yoctoproject.org/poky -b kirkstone`

Get meta layer for RPi:  
`git clone git://git.yoctoproject.org/meta-raspberrypi -b kirkstone`

Build initial environment:  
`source poky/oe-init-build-env build-dir`

Confirm with:  
`bitbake --version`

### _Adding Raspberry Pi layer_

Add RPi layer to the build configuration (/build-dir/conf/bblayers.conf):  
`bitbake-layers add-layer ../meta-raspberrypi`  
_Note:_ Make sure to run this command from the build directory (build-dir)

Confirm new layer with:  
`bitbake-layers show-layers`

### _Altering build configuration_

Open configuration file in your favourite eidtor (e.g. Nano, Vim, etc.)  
`nano conf/local.conf`

Find this line:  
`MACHINE ??= "qemux86-64"`  
comment it out with hash:  
`#MACHINE ??= "qemux86-64"`  
and add this line below:  
`MACHINE ??= "raspberrypi4-64"`

Also, add those lines to enable UART and SSH access:  
`ENABLE_UART = "1"`  
`EXTRA_IMAGE_FEATURES += "ssh-server-openssh"`

Save file and exit the configuration file (`Ctrl+S` then `Ctrl+X` in Nano editor).

_Note:_ The source code in this repository includes all the steps until now, so all you need to do is clone it and build it. You welcome!

---




