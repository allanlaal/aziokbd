# DISCLAIMER #
I'm not the author of this driver, all credits go to [Colin Svingen](https://bitbucket.org/%7Bb2074096-37da-4f83-b574-319a9bb048d5%7D/)
This is a modified version to fix some key mappings (NumPad dot, left and right braces, semicolon, backslash) in "Dexcom Game Keyboard"
with Microdia chipset and pt-BR layout.

# Linux Microdia Keyboard Chipset Driver #

For Chipset `0x0c45`:`0x7603`
The kernel reports the chipset as `SONiX USB Keyboard`

Written for the Azio L70 USB Keyboard: [L70 USB Backlit Gaming Keyboard (KB501)](http://www.aziocorp.com/webe/html/products/index2.aspx?num=50)

> NOTE: Makefile and instructions are only tested on Ubuntu, however they are known to work on Debian, Arch, Fedora, and Manjaro.

Reports suggest it supports the following keyboards as well:

 * SL-6432-BK - Speedlink LUCIDIS Comfort Illuminated Keyboard
 * COUGAR 200K Scissor Gaming Keyboard
 * GAMDIAS USB Keyboard (unspecified model but will report as Microdia chipset)
 * Avazz USB Keyboard (unspecified model but will report as Microdia chipset)
 * Perixx P1800
 * Modecom MC800-Volcano 
 * Serioux Radiant KBL-003
 * SEISA DN-V370 multimedia keyboard

# Installation ##
## DKMS ##

    # debian-based:
    sudo apt-get install git build-essential linux-headers-generic dkms
    
    # fedora:
    sudo dnf install kernel-devel kernel-headers
    sudo dnf groupinstall "Development Tools" "Development Libraries"
    
    git clone https://github.com/teitss/aziokbd
    cd aziokbd
    sudo ./install.sh dkms
    
    

## Manual Install ##

    sudo apt-get install git build-essential linux-headers-generic
    hg clone https://github.com/teitss/aziokbd
    cd aziokbd
    sudo ./install.sh

# Blacklisting #

**NOTE: install.sh attempts to blacklist the driver for you. You shouldn't need to do anything manually. These instructions are to explain the process, in the event something goes wrong.**

You need to blacklist the device from the generic USB hid driver in order for the aziokbd driver to control it.

## Kernel Module ##
If the USB hid driver is compiled as a kernel module you will need to create a quirks file and blacklist it there.

You can determine if the driver is a module by running the following:

    lsmod | grep usbhid

If `grep` finds something, it means that the driver is a module.

Create a file called `/etc/modprobe.d/usbhid.conf` and add the following to it:

    options usbhid quirks=0x0c45:0x7603:0x0004

If you find that the generic USB driver is still taking the device, try changing the `0x0004` to a `0x0007`.

## Compiled into Kernel ##
If the generic USB hid driver is compiled into the kernel, then the driver is not loaded as a module and setting the option via `modprobe` will not work. In this case you must pass the option to the driver via the grub boot loader.

Create a new file in `/etc/default/grub.d/`. For example, you might call it `aziokbd.conf`. (If your grub package doesn't have this directory, just modify the generic `/etc/default/grub` configuration file):

    GRUB_CMDLINE_LINUX_DEFAULT='usbhid.quirks=0x0c45:0x7603:0x4'

Then run `sudo update-grub` and reboot.

Again, if you find that `0x4` doesn't work, try `0x7`.
