+++
title = "LCD FeatherWing"
description =  ""
+++
![A rendering of the LCD FeatherWing](/products/lcdwing/lcdwing.png)

The LCD FeatherWing is a low-power display compatible with [Adafruit's Feather line of development boards](https://www.adafruit.com/category/830). It uses the BU9796 segment LCD controller to drive a custom liquid crystal display glass with 48 segments, including: 

* Five indicator icons: Bell, Wifi, Moon, Arrows and Battery
* Five 7-segment digits, plus a sign indicator
* Four decimal points, one between each pair of digits
* AM and PM indicators, plus a colon for displaying the time

{{< figure src="/products/lcdwing/display.svg" >}}

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

#### Character Set Notes

The character set in the LCD FeatherWing library displays characters in mixed case in order to give each character a unique representation. Both uppercase and lowercase letters in your string display in the case chosen by the library; there is no difference between `display.print("ABCDE")` and `display.print("abcde")`. `A` appears as uppercase, but `B` and `D` appear as lowercase to distinguish them from the `8` and `0` characters:

{{< figure src="/products/lcdwing/display-abcde.svg" >}}

Similarly, `C` appears in lowercase to distinguish it from the open parenthesis `(`, as seen here when we `display.print("(789)")`:

{{< figure src="/products/lcdwing/display-789.svg" >}}

Some characters simply don't work well as a seven-segment digit. In this example, we've called `display.print("Wifi")` and `display.print("Main")`. The `W` appears as an upside down `A`, and the `M` appears as a tall `N`:

{{< figure src="/products/lcdwing/display-aifi.svg" >}}

{{< figure src="/products/lcdwing/display-nain.svg" >}}

If you are willing to sacrifice two characters, you can display these letters using some two character codes: `$J` displays a `W`, and `&7` displays an `M`. So you could display "Wifi" by calling `display.print("$Jifi")`, or "Main" by calling `display.print("&7ain")`:

{{< figure src="/products/lcdwing/display-wifi.svg" >}}

{{< figure src="/products/lcdwing/display-main.svg" >}}

Finally, there's one character that doesn't do what you would expect. Since the `#` symbol doesn't translate well to a 7-segment display, the library uses this character to display the degree symbol. So if you wanted to display a temperature of 72.4° F, you would do it like so: `display.print("72.4#F")`.

{{< figure src="/products/lcdwing/display-temperature.svg" >}}

#### The Colon

When you call `display.print`, the LCD FeatherWing library automatically sets the colon if there is a colon in the appropriate spot (usually the third character in a string). So, for example, calling `display.print("12:34")` will set the colon. 

{{< figure src="/products/lcdwing/display-12_34.svg" >}}

However, calling `display.print("1:23")` will not set the colon.

{{< figure src="/products/lcdwing/display-123.svg" >}}

Instead, add a space before: `display.print(" 1:23")`. The additional space at the beginning of the string pushes the colon to the correct spot.

{{< figure src="/products/lcdwing/display-1_23.svg" >}}

There is also a special method to toggle the colon. Call `display.toggle_colon()` and it will set the colon to the opposite of its current state: if it was on, it turns off, and if it was off it turns on. Calling this method twice a second is a good way to display a clock ticking.

#### Sign Indicator and Decimal Points

If the first character in your string is a minus sign (`-`), the LCD FeatherWing library will set the sign indicator to the left of the digits instead of putting the sign in the first digit. This means that you can display any number from -99999 to 99999. 

{{< figure src="/products/lcdwing/display-neg99999.svg" >}}

{{< figure src="/products/lcdwing/display-99999.svg" >}}

It also means that if you are using the colon, it will be expected in the fourth position instead of the third, as in this countdown clock: `display.print("-10:00.0")`.

{{< figure src="/products/lcdwing/display-countdown.svg" >}}

There are four decimal points, one between each digit. The LCD FeatherWing library will automatically set the appropriate decimal point if it is included in your string, unless it occurs before the first digit or after the last one. This is because there is no decimal point to the left of the first digit or the right of the last one. So, for example, `display.print(".1234")` will display incorrectly, but `display.print("0.1234")` will work. Similarly, `display.print("Thurs.")` will omit the period at the end, but `display.print("Thur.")` will display it.

{{< figure src="/products/lcdwing/display-01234.svg" >}}

{{< figure src="/products/lcdwing/display-thur.svg" >}}

#### Indicator Icons

There are seven indicators on the LCD, including five icons as well as the AM and PM indicators on the right.

{{< figure src="/products/lcdwing/display-indicators.svg" >}}

You can control these indicators using the `set_indicator` and `clear_indicator` methods. The indicators are defined in the `Indicator` class in the `lcdwing_lite` module. Their definitions, clockwise from top left:

* `Indicator.BELL`
* `Indicator.WIFI`
* `Indicator.AM`
* `Indicator.PM`
* `Indicator.BATTERY`
* `Indicator.DATA` (the paired arrows)
* `Indicator.MOON`

There is also a special value, `Indicator.ALL`, that is useful to clear all indicators at once: `display.clear_indicator(Indicator.ALL)`.

#### Putting It All Together: Simple Clock App

{{< figure src="/products/lcdwing/display-clock.svg" >}}

This Python script is available in the examples folder of the LCD FeatherWing CircuitPython library. It's a simple clock that uses CircuitPython's built-in RTC module to display the current time. 

{{< highlight python >}}
import board
import time
import rtc
from oso_lcd.lcdwing_lite import LCDWingLite, Indicator

display = LCDWingLite(board.I2C())
minute = None
clock = rtc.RTC()
clock.datetime = time.struct_time((2022, 6, 30, 11, 59, 55, 0, -1, -1))

while True:
    if minute != clock.datetime.tm_min:
        dt = clock.datetime
        hour = dt.tm_hour % 12
        minute = dt.tm_min
        display.clear_indicator(Indicator.ALL)
        display.print("{:2d}:{:02d}".format(hour if hour else 12, minute))
        if dt.tm_hour < 12:
            display.set_indicator(Indicator.AM)
        else:
            display.set_indicator(Indicator.PM)
    display.toggle_colon()
    time.sleep(0.5)
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
