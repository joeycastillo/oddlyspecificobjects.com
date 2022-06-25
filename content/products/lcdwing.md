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

TODO

## Usage

At this time, only CircuitPython is supported. An Arduino library is in progress, and should be available soon.

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
* [BU9796 data sheet](bu9796axxx-e.pdf)
* TODO: KiCad design files
