# Smart Water LoRaWAN-based Irrigation System
## Overview

This project presents an end-to-end smart irrigation system designed for water-efficient precision agriculture. It combines multi-parameter soil and weather sensing with autonomous actuator control, all coordinated over a low-power wide-area  LoRaWAN network. Sensor data is continuously uploaded to a cloud server where two key agronomic indicators are derived: the evapotranspiration (ET) — quantifying water loss from soil and vegetation through combined evaporation and plant transpiration — and the Crop Water Stress Index (CWSI), which assesses the degree of water stress experienced by the grass or canopy by comparing actual to potential transpiration.

## System Architecture

![System Architecture](docs/architecture.png)

## Hardware

- [[ STM32WL55 ]]: STM32 LoRa Board active and fully supported by STMicroelectronics. [
](https://www.st.com/en/microcontrollers-microprocessors/stm32wl55jc.html).  
   - Sensors: 
     - [[Watermark-200ss]]: measures soil water tension
     - [[DFRobot-SEN0308]]: measures soil _moisture_ (volumetric water content as a percentage) 
     - [[DS18B20]]: measures soil temperature
     - [[BME680]]: measures air temperature, relative humidity and barometric pressure
     - [[SI1145]]: measures visible & NIR (Near Infra-Red) light and estimates a UV index using a mathematical approximation from visible and IR readings 
     - [[AMG8833]] (8 x 8 Pixels): Thermal sensor to measure the spatial temperature distribution of the canopy/grass surface. Used to derive the Crop Water Stress Index (CWSI).
     - Actuator: relay 5V to control the water pump
- [[Dragino-LPS8]]: An open source LoRaWAN Gateway. [
](https://www.dragino.com/products/lora-lorawan-gateway/item/148-lps8.html)
## Repository structure

README.md                         
│
├── hardware/
│   ├── README.md                   
│   ├── sensors/
│   │   ├── Watermark-200ss.md      
│   │   ├── DFRobot-SEN0308.md
│   │   ├── DS18B20.md
│   │   ├── BME680.md
│   │   ├── SI1145.md
│   │   └── AMG8833.md
│   └── actuator/
│       └── Solenoid-Valve.md      
│
├── firmware/
│   └── README.md                  
│
├── network/
│   └── README.md                  
│
├── data-processing/
│   └── README.md                  
│
└── dashboard/
    └── README.md                
