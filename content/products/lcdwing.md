+++
title = "LCD FeatherWing"
description =  ""
+++
![A rendering of the LCD FeatherWing](/products/lcdwing/lcdwing.png)

The LCD FeatherWing is a low-power display compatible with [Adafruit's Feather line of development boards](https://www.adafruit.com/category/830). It uses the BU9796 segment LCD controller to drive a custom liquid crystal display glass with 48 segments, including: 

* Five indicator icons: Bell, Wifi, Moon, Arrows and Battery
* Five 7-segment digits, plus a sign indicator
* Four decimal points, one between each pair of digits.
* AM and PM indicators, plus a colon for displaying the time

![A diagram of the display](/products/lcdwing/display.png)

## Assembly Instructions

First, insert the LCD glass on the front of the wing (the side with the big white box). Then flip the wing over, and solder the LCD pins into the through holes on the back.

![Animation: inserting the LCD](/products/lcdwing/step1.gif)

Next, insert the Feather headers from the back, and solder them in on the front.

![Animation: inserting the LCD](/products/lcdwing/step2.gif)

## Usage

At this time, only CircuitPython is supported.

### CircuitPython Usage

1. Download the the [Adafruit library and driver bundle](https://github.com/adafruit/Adafruit_CircuitPython_Bundle), as well as the [CircuitPython driver for LCD FeatherWing](https://github.com/joeycastillo/OSO_CircuitPython_LCD). 
2. Plug in your Feather board running CircyutPython, and ensure that you can see the `CIRCUITPY` drive on your computer.
3. Copy both the `adafruit_bus_device` folder and the `oso_lcd` folder to your board's `lib` folder.

You're done! Now you can instantiate the LCD driver and write to the display like so: 

{{< highlight python >}}
import board
from oso_lcd.lcdwing_lite import LCDWingLite

display = LCDWingLite(board.I2C())
display.print("HELLO")
{{< /highlight >}}

## Resources

* [LCD FeatherWing CircuitPython library](https://github.com/joeycastillo/OSO_CircuitPython_LCD)
* [Design files for LCD wing and custom LCD](https://github.com/joeycastillo/LCD-FeatherWing)
* [BU9796 data sheet](bu9796axxx-e.pdf)

---

# Advanced Configuration

There are two solder jumpers on the back of the circuit board that allow for advanced configuration. In general, you don't need to do anything to them, but they can be useful in some specific cases.

### JP1: Software Contrast Control

JP1 allows you to control the LCD driving voltage using the DAC pin on an ESP32 Feather or a Feather M4. This can be useful to lower the contrast of the display if you intend to view it from an extreme angle. 

To enable this functionality, use an Exacto knife to cut the bridge connecting JP1 to the pad on the right (GND). Then use a blob of solder to bridge the center pad to the left pad (A0).

Now, the voltage on A0 will define the LCD voltage according to the following formula: `VLCD = 3.3 - A0`. Setting A0 to 0 V will drive the LCD at 3.3 volts; setting A0 to 0.3 V will drive it at 3.0 volts, etc. 

### SDA and SCL jumpers: alternate I²C bus

The BU9796 has no address pins, so you can only drive one display on an I²C bus. However, some Feather boards can support two or even more I²C buses! By soldering wires to these pads and rerouting some signals, you can drive multiple LCD FeahterWings from one Feather.

Please note: **THIS IS AN ADVANCED HACK** and it's easy to inadvertently cut a trace, pull a pad or knock off a surface mount resistor while doing this. Please only attempt this if you feel comfortable working in a very small area.

To do this hack, fully cut the bridge connecting the top and bottom of the SDA and SCL jumpers. Then solder a wire to the BOTTOM half of the jumper. Connect the other side of the wire to an alternate SDA or SCL pin. Some guidance on which pins work: 

#### ESP32/S2/S3 and NRF52840 Feathers

These devices have two I²C peripherals. In addition to the main I²C bus on SDA and SCL, any two pins can function as a second I²C bus.

#### Feather M0 and M0 Express

These devices can support up to four I²C buses. In addition to the main I²C bus on SDA and SCL, you can use the following pins to create three additional I²C buses:

* A1 (SDA) and A2 (SCL) \*
* A3 (SDA) and A4 (SCL)
* 11 (SDA) and 13 (SCL)

\* Note that in this configuration, you will be unable to use the SPI pins as an SPI bus, although they'll still work fine as GPIO.

#### Feather M4 Express

This device can support up to *SIX* I²C buses! In addition to the main I²C bus on SDA and SCL, you can use the following pins to create five additional I²C buses:

* A2 (SDA) and A3 (SCL)
* A4 (SDA) and A1 (SCL)
* 12 (SDA) and 13 (SCL)
* TX (SDA) and RX (SCL)
* 5 (SDA) and SCK (SCL)
