# LA66

:warning: This is a fork of the Dragino firmware project.  This fork will add minor improvements to the codebase as we need them, with my intention of releasing the fixes and updates back to the original repository.

## Linux
These instructions assume you are on a Ubuntu 22.04 system,  other distributions might require a different set of instructions.  The main depencies are:
- `python3`
- `python3-serial`
- [Arm GNU Toolchain](https://developer.arm.com/downloads/-/gnu-rm), installation instruction might vary
- `libncurses5`
- this [Dragino-LRWAN-AT](https://github.com/faijdherbe/LA66) Project.


### Arm libs
To install the arm libs you'll need to download the latest toolchain from the [Arm website](https://developer.arm.com/downloads/-/gnu-rm), and install them manually as arm no longer uses Launchpad for their releases.

[This answer](https://askubuntu.com/a/1243405) describes the installation process, which boils down to:
``` shell
# Unpack the files
sudo tar xjf gcc-arm-none-eabi-YOUR-VERSION.bz2 -C /usr/share/

# Create links so that binaries are accessible system-wide
sudo ln -s /usr/share/gcc-arm-none-eabi-YOUR-VERSION/bin/arm-none-eabi-gcc /usr/bin/arm-none-eabi-gcc 
sudo ln -s /usr/share/gcc-arm-none-eabi-YOUR-VERSION/bin/arm-none-eabi-g++ /usr/bin/arm-none-eabi-g++
sudo ln -s /usr/share/gcc-arm-none-eabi-YOUR-VERSION/bin/arm-none-eabi-gdb /usr/bin/arm-none-eabi-gdb
sudo ln -s /usr/share/gcc-arm-none-eabi-YOUR-VERSION/bin/arm-none-eabi-size /usr/bin/arm-none-eabi-size
sudo ln -s /usr/share/gcc-arm-none-eabi-YOUR-VERSION/bin/arm-none-eabi-objcopy /usr/bin/arm-none-eabi-objcopy
```

Install `libncurses5`. Which is not available in the latest Ubuntu repositories (the current version is at `libncurses6`), so you'll probably need to add the universe repository first.

```
sudo add-apt-repository universe
sudo apt-get install libncurses5 libncurses5:i386
```

Verify your installation by running these commands.
```shell
arm-none-eabi-gcc --version
arm-none-eabi-g++ --version
arm-none-eabi-gdb --version
arm-none-eabi-size --version
```

### Building
To build the new firmware run the following  (starting at project root).
```
source build/envsetup.sh
cd Projects/Applications/DRAGINO-LWAN-AT
make clean
make
```

### python3-serial
To flash the device you'll need the `serial` python package. 
``` shell
sudo apt install python3-serial
```

### Serial port
Adjust the [`Makefile`](./build/Makefile) to match the correct serial device on your local machine.  You can find the correct device by running `dmesg -W`, **BEFORE** inserting the device.
``` shell
sudo dmesg -W
```

which will result in something like this:
``` shell
$ sudo dmesg -W
[ 7126.064284] usb 3-3: new full-speed USB device number 22 using xhci_hcd
[ 7126.215414] usb 3-3: New USB device found, idVendor=10c4, idProduct=ea60, bcdDevice= 1.00
[ 7126.215426] usb 3-3: New USB device strings: Mfr=1, Product=2, SerialNumber=3
[ 7126.215429] usb 3-3: Product: CP2102 USB to UART Bridge Controller
[ 7126.215431] usb 3-3: Manufacturer: Silicon Labs
[ 7126.215434] usb 3-3: SerialNumber: 0001
[ 7126.219965] cp210x 3-3:1.0: cp210x converter detected
[ 7126.223307] usb 3-3: cp210x converter now attached to ttyUSB0
```

In this output you can see the last line, which says that it is attached to `ttyUSB0`, which means it can be found at `/dev/ttyUSB0`.

### Flashing

First make sure you've put the device in program mode, by either shorting the last two pins (`BOOT` + `RX`) on the USB LoRaWan Adapter, or by setting `SW1` to `ISP` and  on the LA66 LoRaWan shield. 

Press the reset button to restart the device, now you should be able to flash the firmware (starting at project root):

``` shell
source build/envsetup.sh
cd Projects/Applications/DRAGINO-LWAN-AT
make flash
```


