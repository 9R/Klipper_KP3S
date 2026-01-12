# Klipper KP3S 3.0
![alt text](/images/klipper%20kp3s_30.png?raw=true)


##Changes from upstream

* Fork of nehilo's klipper config with adjustments for Titan extruder

* The config is set up for connecting a 3d Touch sensor additionally to the default z endstop.
  The3D touch sensor pin is connected to the Z+ connector on the main board. In this config Homing
  is done with the z -endstop and the 3d-touch sensor is used only for probing.

* This config uses an dropin replacement for the LCD-PCB to drive a ssd1306 display with klipper. 
  See [additional GPIOs](#additional-gpios) for details.

* Tested on a KP3S 3.0 with GD32F303 MCU. If your motion controller has an original STM32F103 you probably
  have to untick "Disable SWD at startup" when building the firmware.


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

# Config Files Folder

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
