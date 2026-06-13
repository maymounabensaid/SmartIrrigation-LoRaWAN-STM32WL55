## Overview

  
 The Watermark 200SS is a granular matrix sensor that measures soil water tension (centibars or kPa). Internally, it consists of two electrodes embedded in a specialized matrix material; the electrical resistance between these electrodes varies inversely with soil moisture. As the soil becomes wetter, the resistance decreases.
Unlike simple resistive probes, the Watermark 200SS requires AC excitation (alternating current) during measurement. This prevents electrode polarization and galvanic corrosion, which would otherwise lead to reading drift and permanent sensor degradation if a constant DC voltage were applied.
## Measurement Principle

Inside the sensor, two concentric electrodes are embedded within a specialized, porous granular matrix. 
* High Soil Moisture (Low Tension): When the surrounding soil is wet, the granular matrix absorbs water. This increases the concentration of free ions inside the sensor, making the matrix highly conductive. Consequently, the electrical resistance ($R_{wm}$) drops. 
* Low Soil Moisture (High Tension): As the soil dries, it sucks water back out of the granular matrix. With less water and fewer mobile ions between the electrodes, conductivity decreases, causing the electrical resistance ($R_{wm}$) to rise sharply.
* Typical values:

| Soil Condition | Resistance |
| -------------- | ---------- |
| Saturated      | ~1–2 kΩ    |
| Moist          | ~5–20 kΩ   |
| Dry            | ~50–200 kΩ |
## Alternating Current (AC) Excitation Requirement 
Because the internal matrix relies on ion mobility within an aqueous environment, applying a continuous Direct Current (DC) voltage causes **electrochemical polarization**. Ions migrate permanently to opposite electrodes, building up a parasitic capacitive charge layer that artificially increases resistance readings and causes rapid galvanic corrosion. To circumvent this, the system must utilize Pseudo-AC Excitation: 
1. **Forward Pulse:** A voltage pulse is driven in one direction across the sensor for a few microseconds to sample the resistance. 
2. **Reverse Pulse:** The polarity is immediately inverted for an identical duration. This forces migrating ions back to their baseline equilibrium, neutralizing the chemical matrix. 
3. **Isolation:** The pins are set to a low-power or floating state until the next measurement window.

## Method 1: Resistive Voltage Divider
### Circuit
To measure the sensor (R_wm), we use a known reference resistor (R_ref) form a voltage divider. 
![ Method 1 : Voltage Divider](voltage-divider.png)
- AC excitation cycles:
   1. Cycle A (measurement): Read ADC
    - GPIO_A = HIGH (VCC)
    - GPIO_B = LOW (GND)
    $$ R_{wm} = R_{ref} \left(\frac{V_{mid}}{V_{cc} - V_{mid}}\right)  $$
    $$ R_{ref} = 10 kΩ \text{  and } VCC = 3.3 V $$
    
  2. Cycle B (reverse):  discharge, no read
    - GPIO_A = LOW (GND)
    - GPIO_B = HIGH (VCC)
    
 3. Mathematical Translation (Ohm's Law to Centibars) 

To translate raw resistance to Soil Water Tension in centibars (cb) or kilopascals (kPa), we apply the **Shock & Campbell (1998)** multi-segment calibration model. 

First, we define the baseline resistance in kilo-ohms ($k\Omega$):
$$R_{k} = \frac{R}{1000}$$

We also define the environmental thermal expansion factor $f(T)$ used for the high-dry soil threshold:
$$f(T) = 1 + 0.018 \cdot (T - 24)$$

Using these terms, the piecewise function evaluates across four distinct soil moisture zones:

$$
\Lambda = \begin{cases} 
0 & R \le 550\ \Omega \quad \text{(Saturated Soil)} \\ 
(0.009733 \cdot T \cdot R_{k}) - (0.01205 \cdot T) - (0.6936 \cdot R_{k}) + 1.205 & 550\ \Omega < R \le 1000\ \Omega \quad \text{(Very Wet Soil)} \\
\frac{3.213 \cdot R_{k} + 4.093}{1 - 0.009733 \cdot R_{k} - 0.01205 \cdot T} & 1000\ \Omega < R \le 8000\ \Omega \quad \text{(Mid-Range / Drying)} \\
2.246 + 5.239 \cdot R_{k} \cdot f(T) + 0.06756 \cdot [R_{k} \cdot f(T)]^2 & R > 8000\ \Omega \quad \text{(Critically Dry Soil)}
\end{cases}
$$

### Firmware Implementation

#### Driver files

The driver is split into two files located in `firmware/watermark200ss/`:

- `watermark200ss.h` — constants and function declarations
- `watermark200ss.c` — full implementation

#### Configuration

Before using the driver, verify these defines in `watermark200ss.h` match your wiring:

```c
#define WM_R_REF_OHM    10000.0f   // Reference resistor (Ω)
#define WM_VREF_V       3.3f       // ADC reference voltage
#define WM_DRIVE_A_PIN  GPIO_PIN_0 // Top of divider (Phase A)
#define WM_DRIVE_B_PIN  GPIO_PIN_2 // Bottom of divider (Phase A)
// ADC pin (PA1) configured in CubeMX as ADC1_IN1

```

#### Functions

**`WM_ReadResistance()`** — core measurement function. Runs the dual-phase AC excitation cycle, averages both readings to cancel DC offset, and returns the sensor resistance in ohms. 

**`WM_ResistanceToKPa()`** — converts resistance to soil water tension in kPa using the Shock & Campbell (1998) calibration formula, with temperature compensation.

## Method 2:  
 
### Circuit
 
 
### Firmware Implementation


