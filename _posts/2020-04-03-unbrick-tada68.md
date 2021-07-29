---
layout : post
title: Unbrick TADA68 with a Raspberry PI
---

Things needed:

+ bricked TADA68
+ Raspberry PI
+ 6 wire male-female

The model of the Raspberry Pi is irrelevant, this guide will refer to
**Raspberry Pi 2 Model B**. If you use a different Pi model the pins will be
probably different, but you can easily adjust accordingly since its usage will
be explained.

**WARNING** : *This guide assume familiarity with the basics usage of a Linux
system and terminal.*

## What are we going to do
A bricked _tada68_ means that the microcontroller inside haven't a bootloader
that launch the main program that let the keyboard act like one. Flashing a
bootloader solve this problem.

In this procedure we need to physically connect some pins of the microcontroller to
an ISP programmer. An ISP programmer is a device that let communicate the
microcontroller with the computer. In this case the RPI act as an ISP Programmer.

## Setup of the RPI
We first need to prepare the RPI. Access it how you prefers (SSH or with a
monitor) and make sure that the repository and system are updated

```
$ sudo apt-get update && sudo apt-get upgrade
```

then download `avrdude`

```
$ sudo apt-get install avrdude
```

### How to choose which PIN to use of the RPI

Six pin will be used from the Raspberry to establish the connection with the
microcontroller of the tada68. It's a good time to disassemble the keyboard
and have a look at the PCB. On the back can be found the microcontroller and on
it's left some pins, like in the figure below.

![PCB of the tada68](/assets/images/tada68_isp_pins.jpeg)

Note that this pins has a name: `MISO`, `MOSI`, `SCK`, `RST`, `VCC`, `GND`.

Since this guide use the Raspberry Pi 2 Model B, from the [official
site][rpi_gpio] it's possible to get the numbering of the pins and it's usage.
The figure shows I/O pins of the RPI2 Model B.

[rpi_gpio]: https://www.raspberrypi.org/documentation/usage/gpio/

![GPIO RPI2](/assets/images/gpio_rpi.png)

Notice that some pins on the RPI has the same name from the tada68 PCB.

> Based on the model of your RPI choose the pins accordingly

So the pin that will be used from the RPI will be:

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
With the knowledge of the pin used, it is possible to configure `avrdude`.
Create a folder to work in a clean environment

```
$ mkdir ~/tada68 && cd ~/tada68
```

then copy the basic `config` file of `avrdude` in it

```
$ cp /etc/avrdude.conf ~/tada68/avrdude_gpio.conf
```

The file copied needs to be edited. Use the text editor of your preferences.
Once open, add at the end of the file the following text

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

Edit the number of `reset`, `sck`, `mosi`, `miso` with the **GPIO number** of
the pins that you have chosen.

### Downloading the new bootloader

To finish the setup, download the original [bootloader][bootloader] in the
working directory

```
$ wget https://github.com/rwilbur/tada68-bootloader-restore/raw/master/mass_bootloader_tada68.hex
```

Now everything is ready to flash the TADA68.

[bootloader]: https://github.com/rwilbur/tada68-bootloader-restore/raw/master/mass_bootloader_tada68.hex

## Flashing the bootloader ##
Double check if all the physical connections are correct. Once sure of that,
use this command to see if the device is recognize

```
$ sudo avrdude -p atmega32u4 -C ~/tada68/avrdude_gpio.conf -c pi_1 -v
```

The output should give some information and at some point show this message

```
avrdude: AVR device initialized and ready to accept instructions
```

This means that the keyboard is connected correctly. It's possible to proceed
with the flash of the firmware

```
$ sudo avrdude -p atmega32u4 -C ~/tada68/avrdude_gpio.conf -c pi_1 -v -U flash:w:/home/pi/tada68/mass_bootloader_tada68.hex
```

Once it's done, all the wires can be disconnected. The keyboard can now be
plugged in with USB into a PC and will work like out of the box!

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

