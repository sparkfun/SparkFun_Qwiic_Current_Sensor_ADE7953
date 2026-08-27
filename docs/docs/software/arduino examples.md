# Arduino Examples

Now that we've installed the ADE7953 Library lets take a look at a few of the examples included in it.

## Example 01 - Basic Current Reading

The first example demonstrates the simplest way to read current from the ADE7953. Open the example by navigating to **File** > **Libraries** > **SparkFun ADE7953 Arduino Library** > **Example01_BasicCurrentReading**.

The example sets up the ADE7953 to operate with 16x gain and enables the high-pass filter. After initializing the sensor on the I<sup>2</sup>C bus with these settings, the code performs a calibration of the ADE7953 to zero out the sensor's noise floor. Make sure the clamp is **open** during this process and wait for the code to print "Calibration done. Reading current..." before closing the clamp around the load's wire.

After calibration, the example prints out measured current in Amps every 250ms.

## Examle 02 - Dual Channel Current

The second example shows how to measure RMS current on both Channel A and B. The code configures Channel A as the CT clamp input on the TRRS connector and Channel B as a direct measurement.

## Example 03 - Gain Configuration

Example 3 shows how to configure the PGA (Programmable Gain Amplifier) and the digital (fine) gain on Channel A. The PGA gain controls the amplifier sensitivity. The default is 16x for CT clamp use. The fully available PGA settings are:

``` c++
    ADE7953_PGA_GAIN_1   (1x)
    ADE7953_PGA_GAIN_2   (2x)
    ADE7953_PGA_GAIN_4   (4x)
    ADE7953_PGA_GAIN_8   (8x)
    ADE7953_PGA_GAIN_16  (16x) <-- default
    ADE7953_PGA_GAIN_22  (22x, current channels only)
```

The ADE7953 also provides a digital gain to fine-tune readings on top of the PGA. The example configures it with a simple floating-=point multiplier (1.0 = unity) instead of a raw register value. The code initializes the sensor on the bus and prints out the current PGA gain which is set to 16x by `mySensor.begin()`. After printing this out, the code then adjusts the gain to 8x. You can change this to another of the available PGA settings by adjusting the following line:

```c++ 
 mySensor.setGainIA(ADE7953_PGA_GAIN_8);
```


## Example 04 - Peak Detection

The fourth example shows how to read peak values from Channel A and clear out old peak data every two seconds. The ADE7953 continuously tracks the highest instantaneous current sample since the last reset so you can use this to detect transient events like motor startup surges or inrush current.

The code defines the interval between peak resets in milliseconds. The code defaults to do this every two seconds, adjust the line below to change the interval:

``` c++
const unsigned long kPeakInterval = 2000;
unsigned long lastResetTime = 0;
```

In the setup, the code clears any old peak value by performing an initial read and reset:

``` c++
    mySensor.readAndResetPeakIA();
    lastResetTime = millis();
```

The main loop prints the IRMS value for context, then reads and resets the peak readings and prints out the last peak value read over serial.

## Example 05 - Zero Crossing

Example 5 shows how to configure the zero-crossing (ZX) detection from the ADE7953. This requires connecting the ZX_I pin on the board to a digital input on your microcontroller. The options shown in the code are:

``` c++
    setZXISource()  - //Select Channel A or B as the ZX_I source
    setZXEdge()     - //Choose which edges trigger the ZX output
    enableZXLPF()   - //Enable/disable the zero-crossing low-pass filter
    getPeriod()     - //Read the measured line period from the ZX detector
```

The code defaults to use D2. Adjust this line if using another pin or if no pin is used:

``` c++
// Set to -1 if not wired up — the example will still show register-based readings.
const int kZXIPin = 2;
```

The setup selects Channel A for the ZX_I source, sets the edge detection to trigger on both positive and negative zero crossings and enables the zero-crossing low-pass filter. It includes serial prints to confirm all of the settings:

``` c++
    // Select Channel A as the ZX_I source (false = Channel A, true = Channel B).
    mySensor.setZXISource(false);
    Serial.println("ZX_I source: Channel A");

    // Set edge detection to trigger on both positive and negative zero crossings.
    mySensor.setZXEdge(ADE7953_ZX_EDGE_BOTH);
    Serial.println("ZX edge: Both (positive and negative)");

    // Enable the zero-crossing low-pass filter for cleaner detection.
    mySensor.enableZXLPF(true);
    Serial.println("ZX LPF: Enabled");
```

The main loop then reads the period register. This register represents the line period measured by the zer-crossing detector in the units of the internal clock (223,750 Hz) and prints it out:

``` c++
    uint16_t period = mySensor.getPeriod();

    Serial.print("Period register: ");
    Serial.print(period);
```

It then converts this to an approximate line frequency and measures a full cycle (two zero crossings). Lastly, if the ZX_I pin is connected to a digital input, it prints out the number of crossings counted.

## Example 06 - Interrupt Pin

This example demonstrates how to configure and use the ADE7953's interrupt pin. The Interrupt pin is active LOW and triggers for a variety of events such as overcurrent, zero-crossing or cycle completion without polling. The example defaults to configure an overcurrent threshold on Channel A to trigger the interrupt. 

The code defaults to use D3 on a connected microcontroller. If your microcontroller does not support external interrupts on D3, adjust the line below to a [compatible pin](https://docs.arduino.cc/language-reference/en/functions/external-interrupts/attachInterrupt/):

``` c++
const int kIRQPin = 3;
```

The setup configures the overcurrent threshold in raw ADC counts. Adjust this value depending on your expected current range:

``` c++
    uint32_t overcurrentThreshold = 0x100000;
```

It then enables the overcurrent interrupt on Channel A. This function can be adjusted to enable multiple interrupts using `OR`:

``` c++
    mySensor.setInterruptEnableA(ksfADE7953IrqOI);
```

Finally the setup configures the external IRQ pin to trigger on the falling edge:

``` c++
    pinMode(kIRQPin, INPUT_PULLUP);
    attachInterrupt(digitalPinToInterrupt(kIRQPin), irqISR, FALLING);
```

The main loop then reads and prints the current IRMS value and then prints out whenever an interrupt is triggered as well as the type of interrupt.

## Example 07 - Calibration Offset

Example 7 shows how to use the IMRS offset register to zero out no-load readings on Channel A. This is useful because even with no current flowing, the ADC may report a small IMRS value due to noise. The offset register lets you suptract the baseline noise value for more accurate low-current measurements. Make sure to run this example with NO LOAD on Channel A during the calibration phase.

The setup clears any existing offset before performing calibration and then reads the offset to confirm:

``` c++
    mySensor.setIRmsOffsetA(0);

    
    // Read the current offset to confirm it is zero.
    int32_t currentOffset = mySensor.getIRmsOffsetA();
    Serial.print("Current IRMS offset: ");
    Serial.println(currentOffset);
```

Next, the code performs the calibration phase and prints out the average of the samples:

``` c++
    Serial.println();
    Serial.println("=== CALIBRATION PHASE ===");
    Serial.println("Ensure no load is connected to Channel A.");
    Serial.print("Averaging ");
    Serial.print(kCalSamples);
    Serial.println(" samples...");
    Serial.println();
```

After letting the readings stabilize, it collects the samples, computes the average baseline and then gives an average no-load reading:

``` c++
    uint32_t sum = 0;
    for (int i = 0; i < kCalSamples; i++)
    {
        uint32_t sample = mySensor.getIRmsA();
        Serial.print("  Sample ");
        Serial.print(i + 1);
        Serial.print(": ");
        Serial.println(sample);
        sum += sample;
        delay(100);
    }

    uint32_t average = sum / kCalSamples;
    Serial.println();
    Serial.print("Average no-load reading: ");
    Serial.println(average);
```

The final stage of calibration writes the negative of the average readings as the offset for the ADE7953 to subtract from the squared signal before taking the root to essentially remove the noise floor:

``` c++
    currentOffset = mySensor.getIRmsOffsetA();
    Serial.print("IRMS offset is now: ");
    Serial.println(currentOffset);
```

Once the calibration process completes, the code prints out "CALIBRATION COMPLETE". Now connect a load and the code will print out IMRS on Channel A with the calibrated value every 500ms.