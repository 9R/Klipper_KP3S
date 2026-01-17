# Klipper KP3S 3.0
![alt text](/images/klipper%20kp3s_30.png)


##Changes from upstream

* Fork of nehilo's klipper config with adjustments for Titan extruder

* The config is set up for connecting a 3d Touch sensor additionally to the default z endstop.
  The3D touch sensor pin is connected to the Z+ connector on the main board. In this config Homing
  is done with the z -endstop and the 3d-touch sensor is used only for probing.

* This config uses an dropin replacement for the LCD-PCB to drive a ssd1306 display with klipper. 
  See [additional GPIOs](#additional-gpios) for details.

* Tested on a KP3S 3.0 with GD32F303 MCU. If your motion controller has an original STM32F103 you probably
  have to untick "Disable SWD at startup" when building the firmware.

* The KP3S 3.0 running this klipper configuration has received some [hardware modifications](#hardware-modifications). Using this config
  on a stock KP3S 3.0 might require revertig certain commits. See hardware [modifications section] below for details.

# Additional GPIOs

The KP3S' touch screen is of no use when running Klipper firmware. The PCB holding the display 
can be replaced a custom PCB to expose additional GPIOs (including SPI) so more sensors, steppers 
or a Klipper-compatible display and rotary encoder can be attached. Design for a PCB exposing all 
available extra pins in the aperture of the housing where the touch screen resides can be found in 
this [repo](https://github.com/9R/kp3sExpander).

# Firmware build

Follow the default klipper build instructions

```bash
cd ~/klipper
```
```bash
make menuconfig
```

### menuconfig parameters:
```less
                  Klipper Firmware Configuration
[*] Enable extra low-level configuration options
    Micro-controller Architecture (STMicroelectronics STM32)  --->
    Processor model (STM32F103)  --->
[ ] Only 10KiB of RAM (for rare stm32f103x6 variant)
[*] Disable SWD at startup (for GigaDevice stm32f103 clones)
    Bootloader offset (28KiB bootloader)  --->
    Clock Reference (8 MHz crystal)  --->
    Communication interface (Serial (on USART3 PB11/PB10))  --->
(250000) Baud rate for serial port
[*] Optimize stepper code for 'step on both edges'
(!PC6, !PD13) GPIO pins to set at micro-controller startup
```

```bash
make 
```
Make sure to run the following script to add some magic bytes so the firmware can be flashed.

```bash
./scripts/update_mks_robin.py out/klipper.bin out/Robin_nano.bin
```

Copy the resulting file to the SDcard, insert it into the KP3S and switch it on.
After a successful firmware update the file on the SDcard will be renamed to ROBIN_NANO.CUR.

# Hardware modifications

## Enabled stepper UART (since [c581bc7](https://github.com/9R/Klipper_KP3S/commit/c581bc73a7a3341313e8b4799837c19155bdf6f4))

To use stepper driver configuration via UART a bit of light soldering and modifying the stepper jumpers is necessary.

### Close UART pin solder brigde

First close the solder bridges for the stepper drivers you want to configure via UART.

| axis | jumper | gpio |
|------|--------|------|
|  E0  |  JP_M1 | PA10 |
|  E1  |  JP_M2 | PA11 |
|   X  |  JP_M3 | PA5  |
|   Y  |  JP_M4 | PC13 |
|   Z  |  JP_M5 | PC7  |

The solder brigde locations on the motion controller are highlighted below. 

![alt text](/images/stepper_jumpers.jpg)

### Set Jumpers

The following parameters apply to the MKS TMC2225 stepper driver.

Next the stepper config jumpers need to be set correctly

Jumper locations below the stepper driver:

```
========
 (C) ABC        AA = M0
     ABC        BB = M1
========        CC = UART
```

This klipper config is based on the following jumper settings for all active steppers (E0,X,Y,Z):

| jumper | state    |
|--------|----------|
|   AA   |  jumper  |
|   BB   |  jumper  |
|   CC   | no jumper|

<details>
<summary>
Background on jumper config
</summary>

| jumper | stepper driver pin |
|--------|--------------------|
|    AA  |               M0   |
|    BB  |               M1   |
|    CC  |               UART |

| AA | BB | microsteps |
|----|----|------------|
|  0 |  0 |        4   |
|  1 |  0 |        8   |
|  0 |  1 |       16   |
|  1 |  1 |       32   |

|  CC |      UART |
|-----|-----------|
|  0  |   enabled |
|  1  |  disabled |

0 = jumper open
1 = jumper closed

</details>

# Config Files

```bash
 -bed.cfg
 -bltouch.cfg
 -display.cfg
 -extruder.cfg
 -fan.cfg
 -macros.cfg
 -printer.cfg
 -stepper.cfg
 -thermistor.cfg
 -tmc.cfg
```
