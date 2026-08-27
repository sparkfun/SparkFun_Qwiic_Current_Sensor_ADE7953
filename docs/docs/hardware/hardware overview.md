# Hardware Overview

## ADE7953 Single Phase Metering IC

The ADE7953 is a single phase energy measurement IC. It measures both voltage and current along with active and reactive energy. It can also measure instantaneous rms (IRMS) voltage and current. It operates using three high-accuracy sigma-delta ADCs with two current channels. Both channels have a Programmable Gain Amplifier which can also be fine tuned with a digital gain. The ADE7953 communicates over I<sup>2</sup>C, UART and SPI (the SPI pins are *not* broken out on this Qwiic breakout).

The ADE7953 also has dedicated pins for zero-crossing for voltage (not available on this Qwiic breakout) and current to measure line frequency, cycles or to synchronize measurements with the line frequency. It supports EN 50470-1, EN 50470-3, IEC 62053-21, IEC 62053-21, IEC 62053-22 and IEC 62053-23 standards. It accepts a supply voltage up to 3.7V so it works perfectly at the 3.3V of the Qwiic ecosystem. For complete information on the ADE7953, refer to the [datasheet](/ref/ADE7953.pdf).

## Connectors

### Qwiic

The board has a pair of Qwiic connectors for easy integration into a Qwiic circuit. These 4-pin connectors are tied to the board's 3.3V, Ground, SDA and SCL pins to power and communicate over a Qwiic connection by default.

### Current Sensing Connections

The board routes the ADE7953's Channel A current input to a 3.5mm TRRS jack for use with a compatible current transformer/current clamp. The board design is intended to work with a current transformer with a ratio of 2000:1. Channel A and Channel B are also routed to plated through-holes on either side of the TRRS jack for those who prefer a soldered connection or want to use Channel B. Note, the through-hole connections to the ADE7953's current channels operate at a 1:1 ratio and cannot safely measure currents above 100mA without adding in a current transformer with a compatible CT ratio. 

### Plated Through-Hole Header

The 0.1"-spaced plated through-hole (PTH) header on the bottom of the board breaks out the power pins (3.3V and Ground), communication (SDA/TX and SCL/RX) pins, Interrupt and Zero Cross signal pins. 

The Interrupt pin is an active LOW digital output. It can be configured to fire on a variety of events such as overcurrent, zero-crossing or cycle completion without polling. It can be configured to trigger on single events or multiple events at the same time.

The Zero Cross signal (labeled ZX_I) outputs a pulse each time the current waveform crosses zero. This pulse is great for determining line frequency and syncrhonizing measurements to the AC cycle of a connected load.

## LED

This board has a single LED labeled **PWR**. This red LED is a simple status LED to indicate when the board has power.

## Solder Jumpers

The Non-Invasive Current Sensor - ADE7953 has four solder jumpers labeled **SCLK** (some boards may have this jumper mislabeled as SLCK), **CS**, **I<sup>2</sup>C** and **LED**. The list below outlines their functionality, default state and any notes on their use.

* **SCLK** - This three-way jumper works with the CS jumper to set the communication interface used by the ADE7953. The jumper pulls the SCLK pin HIGH/3.3V by default to use the I<sup>2</sup>C interface. Sever the trace between the "top" and center pad and connect the center pad to the "bottom" to pull SCLK LOW/GND to enable UART. Leave the center pad disconnected from the other two pins to enable SPI communication. **NOTE**: This board does *not* route the ADE7953's SCLK and CS pins to dedicated PTHs so SPI is not *technically* supported on this board. Refer to the Troubleshooting section for more information on SPI.
* **CS** - This jumper works with the SCLK jumper to set the ADE7953's communication interface. The jumper pulls the CS pin HIGH to enable I<sup>2</sup>C and UART. Open this jumper to disable both interfaces and to enable SPI. **NOTE**: This board does *not* route the ADE7953's SCLK and CS pins to dedicated PTHs so SPI is not *technically* supported on this board. Refer to the Troubleshooting section for more information on SPI.
* **I<sup>2</sup>C** - This three-way jumper pulls the ADE7953's SDA and SCL lines to 3.3V through a pair of **2.2k&ohm;** resistors. It is CLOSED by default. Completely open this jumper to disable the pull-ups on the data and clock pins.
* **LED** - The LED jumper completes the power LED circuit. It is CLOSED by default. Open this jumper to disable the power LED.

## Board Dimensions

This Qwiic breakout matches the standard Qwiic form-factor and measures 1" x 1" (22.5mm x 22.5mm) and has four mounting holes that fit a 4-40 screw.