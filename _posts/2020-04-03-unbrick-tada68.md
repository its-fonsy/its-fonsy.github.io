---
layout : post
title: Unbrick TADA68 with a Raspberry PI
---

Things needed:

+ bricked TADA68
+ Raspberry PI
+ 6 wire male-female

The model of the Raspberry is irrelevant, this guide will refer to **Raspberry Pi 2 Model B**.
If you use a different model the PINS will be probably different, but will be explained how and why it's use
them so you can adjust accordingly.

*This guide assume familiarity with the basics usage of a Linux system and terminal.*

## What are we going to do ##
When the _tada68_ is bricked means that the microcontroller inside haven't a bootloader to launch so it can't
do anything. Flashing a bootloader would solve this problem and let the keyboard work again. This procedure
need to physically connect some pins of the microcontroller to an ISP programmer. In this case the ISP
programmer will be the RPI. Connecting some pins of the RPI to the _tada68_ will make possible to flash the
new bootloader into the microcontroller.

## Setup of the RPI ##
The `avrdude` package is required to flash the bootloader, so it can be installed from the package manager.
First make sure that the repository and system are updated.

```bash
$ sudo apt-get update && sudo apt-get upgrade
```

Then the package can be installed

```bash
$ sudo apt-get install avrdude
```

### How to choose which PIN to use of the RPI
Six PIN will be used from the Raspberry to establish the connection with the microcontroller of the tada68.
It's a good time to disassemble the keyboard and have a look at the PCB. On the back can be found the
microcontroller and on it's left some pins, like in the figure below.

![PCB of the tada68](/assets/images/tada68_isp_pins.jpeg)

Note that this pins has a name:

+ `MISO`
+ `MOSI`
+ `SCK`
+ `RST`
+ `VCC`
+ `GND`

Since this guide use the Raspberry Pi 2 Model B, from the [official site][rpi_gpio] it's possible to get the
numbering of the pins and it's usage. The figure shows I/O pins of the RPI2 Model B.

[rpi_gpio]: https://www.raspberrypi.org/documentation/usage/gpio/

![GPIO RPI2](/assets/images/gpio_rpi.png)

It's possible to notice that some PINS has the same name from the tada68 PCB. So the pin that will be used
from the RPI will be:

> You can choose to use different PINS, based on the model of the RPI that you have. This guide use this pins

|  NAME  |  GPIO  | PIN NUMBER |
|:------:|:------:|:----------:|
| `MISO` | 9      | 21         |
| `MOSI` | 10     | 19         |
| `SCK`  | 11     | 23         |
| `RST`  | 8      | 24         |
| `VCC`  | -      | 1          |
| `GND`  | -      | 3          |

In practice the **pin number 21** (MISO) will be connected with a wire with the `MISO` of the tada68 PCB.
All the six pins of the RPI must be connected with the respective one of the tada68 PCB.

The GPIO number will be used soon.

### avrdude configuration
With the knowledge of the pin used, it is possible to configure `avrdude`. Create a folder to work in a
clean environment

```bash
$ mkdir ~/tada68 && cd ~/tada68
```

then copy the basic `config` file of `avrdude` in it

```bash
$ cp /etc/avrdude.conf ~/tada68/avrdude_gpio.conf
```

The file copied needs to be edited. Use the text editor of your preferences. Once open, add at the end of the
file the following text

```config
# Linux GPIO configuration for avrdude.
# Change the lines below to the GPIO pins connected to the AVR.
programmer
  id    = "pi_1";
  desc  = "Use the Linux sysfs interface to bitbang GPIO lines";
  type  = "linuxgpio";
  reset = 8;
  sck   = 11;
  mosi  = 10;
  miso  = 9;
;
```

Edit the number of `reset`, `sck`, `mosi`, `miso` with the **GPIO number** of the pins that you have choosen.

### Downloading the new bootloader

To finish the setup, the [bootloader][bootloader] must be downloaded.

```bash
$ wget https://github.com/rwilbur/tada68-bootloader-restore/raw/master/mass_bootloader_tada68.hex
```

Now evrything is setup to flash the TADA68.

[bootloader]: https://github.com/rwilbur/tada68-bootloader-restore/raw/master/mass_bootloader_tada68.hex

## Flashing the bootloader ##
Double check if all the physical connections are correct. Once sure of that, use this command
to see if the device is recognize

```bash
$ sudo avrdude -p atmega32u4 -C ~/tada68/avrdude_gpio.conf -c pi_1 -v
```

The output should give some information and at some point

```
avrdude: AVR device initialized and ready to accept instructions
```

This means that the keyboard is connected correctly. It's possible to proceed with the flash of the
firmaware

```bash
$ sudo avrdude -p atmega32u4 -C ~/tada68/avrdude_gpio.conf -c pi_1 -v -U flash:w:/home/pi/tada68/mass_bootloader_tada68.hex
```

Once it's done, all the wires can be disconnected. The keyboard can now be plug with USB into a PC
and will work like out of the box!

## Reference
[u/banjonovice](https://www.reddit.com/user/banjonovice/):
*Bricked TADA68 and How I Fixed it - A Novice's Tale* **@**
[ [reddit post](https://www.reddit.com/r/MechanicalKeyboards/comments/66sji0/bricked_tada68_and_how_i_fixed_it_a_novices_tale/) ]

[tdicola](https://learn.adafruit.com/users/tdicola):
*Program an AVR or Arduino Using Raspberry Pi GPIO* **@**
[ [guide](https://learn.adafruit.com/program-an-avr-or-arduino-using-raspberry-pi-gpio-pins/overview) ]

[rwilbur](https://github.com/rwilbur):
*tada68-bootloader-restore* **@**
[ [github-repo](https://github.com/rwilbur/tada68-bootloader-restore) ]

