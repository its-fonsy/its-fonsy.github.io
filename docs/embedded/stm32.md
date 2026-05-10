# ChibiOs on Blackpill

Installed toolchain, compiler and flash tool

    pacman -Syy arm-none-eabi-binutils arm-none-eabi-gcc arm-none-eabi-newlib stlink

Then cloned ChibiOs and compiled the project "demos/STM32/RT-STM32F401D-BLACK-PILL".
Just go into the directory and run

    make

then to flash the MCU

    st-flash --reset write build/ch.bin 0x8000000

# Wire the STLink/V2

Search on google "stlink/v2 pinout".

The pin "1" is, looking the connector females holes and the red line on top, on
top-right column.
