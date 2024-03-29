<!-- Please do not change this html logo with link -->
<a href="https://www.microchip.com" rel="nofollow"><img src="images/microchip.png" alt="MCHP" width="300"/></a>

# PWM LED Current Regulator using PIC18F16Q41
This code example implements a linear low-side current regulator for LEDs using the PIC18F16Q41's digital-to-analog converter (DAC), comparator, and a handful of external components. The operational amplifier (OPA) module on the PIC18F16Q41 can be used in this demo to reduce the value of the shunt resistor or to reduce the current output range of the circuit.

## Related Documentation

[TB3280: Using Operational Amplifiers in PIC16 and PIC18](https://www.microchip.com/en-us/application-notes/tb3280)  
[TB3279: Optimizing Internal Operational Amplifiers for Analog Signal Conditioning](https://www.microchip.com/en-us/application-notes/tb3279)  
[AN3521: Analog Sensor Measurement and Acquisition](https://www.microchip.com/en-us/application-notes/an3521)  
[Code Example: Linear LED Current Regulator using PIC18F16Q41](https://mplab-discover.microchip.com/com.microchip.mcu8.mplabx.project.pic18f16q41-linear-led-current-regulator)  

## Software Used

* <a href="https://www.microchip.com/en-us/tools-resources/develop/mplab-x-ide?utm_source=GitHub&utm_medium=TextLink&utm_campaign=MCU8_MMTCha_PIC18-Q41&utm_content=pic18f16q41-pwm-led-current-regulator-github&utm_bu=MCU08">MPLAB® X IDE v5.40 or newer</a>
* <a href="https://www.microchip.com/en-us/tools-resources/develop/mplab-xc-compilers?utm_source=GitHub&utm_medium=TextLink&utm_campaign=MCU8_MMTCha_PIC18-Q41&utm_content=pic18f16q41-pwm-led-current-regulator-github&utm_bu=MCU08">MPLAB XC8 Compiler v2.20 or newer</a>
* <a href="https://www.microchip.com/en-us/tools-resources/configure/mplab-code-configurator?utm_source=GitHub&utm_medium=TextLink&utm_campaign=MCU8_MMTCha_PIC18-Q41&utm_content=pic18f16q41-pwm-led-current-regulator-github&utm_bu=MCU08">MPLAB Code Configurator (MCC) Classic v3.95.0 or newer</a>

## Hardware Used

* <a href="https://www.microchip.com/en-us/product/PIC18F16Q41?utm_source=GitHub&utm_medium=TextLink&utm_campaign=MCU8_MMTCha_PIC18-Q41&utm_content=pic18f16q41-pwm-led-current-regulator-github&utm_bu=MCU08">PIC18F16Q41</a><br>
* <a href="https://www.microchip.com/en-us/development-tool/DM164137?utm_source=GitHub&utm_medium=TextLink&utm_campaign=MCU8_MMTCha_PIC18-Q41&utm_content=pic18f16q41-pwm-led-current-regulator-github&utm_bu=MCU08"> Microchip Curiosity Development Board (DM164137), Rev 4 </a>

## External Parts Required

* 6.8 ohm resistor (current shunt)
* 100k ohm resistor
    * This resistor discharges the capacitor passively and ensures the MOSFET remains off if the part is off or the pin is tri-stated. For this example, it is not needed.
* 10k ohm resistor
* 0.1uF capacitor
* N-Type MOSFET (rec. signal transistor, such as a 2N7000A)
* LED
* Breadboard

#### Power Dissipation
Before building or operating this demo, ensure that the parts selected for this demo are appropriately rated for the peak power of this circuit.

The parts that dissipate the most power are the 6.8 ohm shunt resistor, the MOSFET, and the LED. Some parts **may become warm or hot** during operation.

## Wiring
#### Simple Wiring Diagram
<img src="images/basicWiring.png" alt="Basic Wiring Diagram" width="500px"/><br>

#### Amplified Wiring Diagram
<img src="images/amplifiedWiring.png" alt="Amplified Diagram" width="500px"/><br>

#### Connections

| Pin | Function
| --- | --------
| RB6 | Comparator Output (connect to the RC network)
| RC3 | Current Feedback Input
| RB5 | OPA1IN0+ (see note)
| RC2 | OPA1OUT (see note)

Note:<br> **If using the operational amplifier**, connect the current shunt output to RB5, then the output of the operational amplifier to RC3. <br>**Otherwise**, connect the current shunt output to RC3 directly.

## Operation
This code example regulates the current through the LED(s) to generate a breathing effect. The MOSFET acts as a voltage-controlled current sink. To set the voltage on the MOSFET's gate, and thus the current through the transistor, the comparator compares the voltage on the current shunt (RC3) with the DAC's level. If the current sensed is less than the set level, the comparator turns on. When the current sensed is greater than or equal to the DAC level, then the comparator turns off. The signal is integrated through the 10k ohm and 0.1uF RC network on the gate of the MOSFET to regulate the transistor. To reduce switching noise, the comparator is used in synchronous mode by latching the output through an internal flip-flop.

Using the internal DAC, almost any arbitrary pattern can be created (flickering for candles, linear brightness control, etc...). The breathing effect was chosen to show the ramp up and down of the current through the LED. The GIF below shows the LED breathing effect.

<br><img src="images/Demo.gif" alt="Demo GIF" width="500px"/><br>
*Example Output*

To generate the effect, the DAC generates a triangle wave which is used by the comparator to set the current through the LED according to the following formula:

**Iout = Vdac / (6.8 x Gain)**

In the unamplified (or gain of 1) configuration, the maximum current is **~151mA**, assuming sufficient headroom on the power supply. (The image below shows the power supply saturate and fail to reach the full range.) Using the OPA module with a gain > 1 can either:

1. Reduce the current output range. The default gain is 8, which limits the current to a maximum of **~19mA**.
2. Reduce the size of the current shunt resistor, reducing the power dissipated by the resistor.
<br>

<img src="images/currentSense.PNG" alt="Voltage on Sense Resistor" width="500px"/><br>
*Voltage Across the Sense Resistor*

#### Short Circuit Protection
Due to the MOSFET acting as voltage controlled current sink, there is some short circuit protection built into the circuit. However, it is not recommended to depend on this circuit as the only means of protection. Prolonged short circuits may cause heating.

## High Voltage Operation
This code example can be run at voltages in excess of Vdd. Running at a higher voltage enables more LEDs to be strung together in series. However, some modifications to the circuit should be made to handle the higher voltages and power.

**Important!** If an I/O associated with the PIC18F16Q41 exceeds Vdd or Vss, then the device will be permanently damaged.

## Summary
With a few external parts, the analog peripherals on the PIC18F16Q41 can be used to control the current through an LED.
