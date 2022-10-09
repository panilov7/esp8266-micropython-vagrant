# About

This recository contains a `Vagrantfile` which creates a VM for building
MicroPython firmware for the ESP8266. It is inspired by https://github.com/adafruit/esp8266-micropython-vagrant, but uses debian bullseye as the VM OS and follows the build instructions in the the official MicroPython repo (https://github.com/micropython/micropython/blob/master/ports/esp8266/README.md). 

This Vagrantfile does not compile the espressif SDK, but uses a precompiled version from here: https://github.com/jepler/esp-open-sdk/releases/download/2018-06-10/xtensa-lx106-elf-standalone.tar.gz (Also recommended in the official [ESP8266 port README](https://github.com/micropython/micropython/blob/master/ports/esp8266/README.md))

# Dependencies

You must have the following software installed:

*  [VirtualBox](https://www.virtualbox.org/)
*  [Vagrant](https://www.vagrantup.com/)

# Usage

Clone this repository and navigate to it in a command terminal, then run the
following command to bring the Vagrant virtual machine up and provision it for
compiling the tools:

    vagrant up

This will set up your development environment for building MicroPython for the ESP8266.
This setup process follows the instrictions described in the README of the ESP8266 port
found here: https://github.com/micropython/micropython/blob/master/ports/esp8266/README.md

After the virtual machine is brought up, use the following command to enter an SSH session on it:

    vagrant ssh

Once inside the virtual machine you should see the cloned MicroPython git repository in `~/micropython`

## Build process

cd into `~/micropython` directory and execute the following commands:

    make -C ports/esp8266 submodules
    make -C mpy-cross

This will build the ESP8266 submodules and mpy-cross.

Then to compile the ESP8266 firmware:

    cd ports/esp8266
    make -j BOARD=GENERIC

This will produce binary images in the `build-GENERIC/` subdirectory. Substitute the board for whichever board you're using.

# Installing

After a successful build there should be a `firmware-combined.bin` file inside the `build-GENERIC/` subdirectory (depending of course on whichever board you're using).

The `build-GENERIC/firmware-combined.bin` file can be copied to Vagrant's shared directory so it is accessible from your main computer. 
To do this run:

    cp build-GENERIC/firmware-combined.bin /vagrant/

Now on your machine (not on the VM!) look inside the folder with the Vagrantfile and you should see the firmware-combined.bin file.

Now the file can be [flashed](https://docs.micropython.org/en/latest/esp8266/tutorial/intro.html#deploying-the-firmware) to the ESP8266 from your machine:

    esptool.py --port /dev/ttyUSB0 erase_flash

    esptool.py --port /dev/ttyUSB0 --baud 460800 write_flash --flash_size=detect 0 firmware-combined.bin
