ProtoDAC TDA1387 X8 <!-- omit in toc -->
===============================================
By: @hifinet (c) 2023  
Edited by @Tim Curtis  
Updated: 2023-03-12

This document describes the ProtoDAC TDA1387 X8 Non-Oversampling (NOS) Digital to Analog Converter (DAC).

### Table of Contents <!-- omit in toc -->

- [1. Introduction](#1-introduction)
- [2. Printed Circuit Board (PCB)](#2-printed-circuit-board-pcb)
  - [2.1. PCB Layout](#21-pcb-layout)
  - [2.2. Schematic](#22-schematic)
  - [2.3. Pin assignments](#23-pin-assignments)
- [3. Component parts](#3-component-parts)
  - [3.1. I/V resistors](#31-iv-resistors)
  - [3.2. I2S resistors](#32-i2s-resistors)
  - [3.3. Electrolytic decoupling capacitors](#33-electrolytic-decoupling-capacitors)
  - [3.4. Output coupling capacitors](#34-output-coupling-capacitors)
- [4. Assembly and testing](#4-assembly-and-testing)
  - [4.1. Assembly](#41-assembly)
  - [4.2. Testing](#42-testing)
- [5. moOde configuration](#5-moode-configuration)
  - [5.1. Audio Config](#51-audio-config)
  - [5.2. CamillaDSP](#52-camilladsp)
  - [5.3. MPD and SoX](#53-mpd-and-sox)
- [6. Appendix)

# 1. Introduction

The ProtoDAC TDA1387 X8 is a DIY DAC based on the vintage Philips TDA1387 multibit DAC chip in an eight chip module as pictured below. The chips are configured in parallel with decoupling capacitors and pin 7 capacitors.

![](images/protodac_x8_module.jpg)

The module is designed as a direct plug in replacement for the famous TDA1541, which is now difficult to obtain. The TDA1541 was a flagship Philips multibit 16 bit DAC in a 28 pin DIP/DIL, and is older and more complex. The TDA1387 is a later development multibit DAC with current output, and simplified having only 8 pins.

The TDA1387 is a very interesting 16-bit design and represents the end of the multibit DAC era. It uses continuous calibration of the five most significant bit current sources, so it has 32 current sources and one spare source. Philips multibit DACs are also unique in that they do not use resistors like some other DACs, but instead use active devices to generate the currents.

The ProtoDAC is one of the easiest NOS DAC's to build. The number of components is low and the circuit design is simple since the TDA1387 directly uses I2S from the Raspberry Pi as input and passive I/V is used for the output stage.

Most importantly, the sound quality of the finished DAC is excellent, especially when using premium components.

# 2. Printed Circuit Board (PCB)

The PCB dimensions and layout conform to the Raspberry Pi "Hardware Attached on Top" (HAT) requirements including the standard 40-pin GPIO header. All components are attached using through-hole soldering.

## 2.1 PCB Layout

The Computer Aided Design (CAD) drawing of the PCB shows the board layout and location of the component parts. The design allows for several variations including axial or radial output I/V capacitors and RCA jacks or direct wiring to L/R channels.

![](images/protodac_pcb_rev2_500.jpg)

## 2.2 Schematic

Paragraph goes here

![](images/protodac_schematic_v2.jpg)

## 2.3 Pin assignments

Paragraph goes here

### I2S Inputs

|X8 Module pin|I2S|GPIO number|Broadcom pin|
| :- | :- | :- | :- |
|1|WS(LRCK)|19|35|
|2|__BCK__|18|12|
|3|DATA|21|40|
|4|__BCK__|18|12|

![](images/protodac_gpio_header_500.jpg)

### TDA1387 X8 Module

The module is implemented as a 28 pin DIP/DIL package. The module is only connected by pins 1,2,3,4,5,6,25 and 28. Note that pins 2 and 4 are internally connected.

|Pin|Function|Pin|Function|
| :- | :- |:- | :- |
|1|WS (LRCK)|6|Right channel audio out|
|2|BCK|7-24|N/C|
|3|DATA|25|Left channel audio out|
|4|BCK|26-27|N/C|
|5|GND|28|Vcc +5VDC|

![](images/protodac_module_dip_dil_v4.jpg)

# 3. Component parts

Component parts for the DAC are readily available from various suppliers but since the Philips TDA1387 chips are not produced anymore the 8 x parallel chip module long term availability cannot be guaranteed.

## 3.1 I/V resistors

### Type of resistor

Vishay naked bulk metal foil are unsurpassed, and sound very transparent with excellent detail. This may be due to the very low noise or noninductive design. The improvement in detail compared to other resistor types is dramatic.

Bulk metal foil can be ordered to spec, but they are expensive. Metal film sound OK, and have the advantage in that they are abundant and cheap. I would recommend starting with metal film to hear the possibilities of the DAC, and then move up to Vishay metal foil resistors (if not Z-foil, then S-foil) if you think the DAC shows promise.

### Value of the resistor

 This is critical. The I/V resistor should be no larger than 430R for Vcc of 5 VDC. I measured THD with 0 dB 1kHz sine wave input to the DAC with various I/V resistances. See the chart below. Distortion at 223.5R is measured at 0.0080% and at 322R it is 0.0097%, and THD increases linearly with resistance up to just past 430R. Then distortion starts to increase exponentially, indicating clipping.

![](images/protodac_thd_distortion_600_v3.jpg)

Excessive distortion is created when the DAC is clipping. Fortunately, the TDA1387 has a high voltage compliance of 3.5V at 5V Vcc. Clipping occurs if the AC signal exceeds +3.5V or 0V. The final calculation depends on the I/V resistance and the module with its particular characteristics (DC current at idle, and the peak to peak current at full signal).  The I/V resistor should be no larger than 430R for Vcc of 5 VDC.

Since there are variations in the modules, it would be prudent to measure the distortion with various resistances in the target range with your module, before buying expensive I/V resistors. This would be especially the case if you are trying to push the 430R limit or the Vcc maximum of the TDA1387. You can measure distortion with a computer, USB audio interface and REW. The most reliable method would be to use cheap metal films in 430R value, and check distortion with the variations in resistance values, based on accurate resistance measurements with a DMM. Another factor will be supply voltage, which can change full scale output current. So use the same supply voltage with the expensive I/V resistors.

The Vishay Z-foil resistors from Texas Components (TX2575) come in either 470R (too high) or 390R (possibly too low) as a standard resistance, but not 430R. They sell custom values for the same price. Charcroft does sell a Z-foil resistor in 430R. Note the tolerance is +/- 0.1%.

The output signal voltage decreases with a lower I/V resistor, and this may be a factor to consider in your particular system. Do you have a preamplifier that can amplify a weak signal from the DAC?

TeraDak TDA1387 x8 uses 390R I/V with 8000uF, which has a screen-like image (no depth). I have seen earlier versions with a 560R I/V resistor, so it seems they have corrected this problem with more recent versions. The larger capacitance somewhat compensates sonically for clipping. The L1387 USB x8 uses 560R with 1300uF. The I/V resistor is too high, clipping will occur at 0dB.

The source of Vishay S and Z-foil resistors in the US is Texas Components, and in Europe it is Charcroft . TX2575 are about $13 each without shipping. The TX2352 or S-foil is the "original" foil naked audio resistor, and are about $9 each. The Vishay S and Z foil resistors are vastly better performing in this DAC than other types.

## 3.2 I2S resistors

430R or 470R generic 1/8 or 1/4W metal film. The purpose is to limit high frequency noise on the I2S lines. I have used unshielded 10cm jumpers to an outboard proto board without a problem. Mount close to the module.

## 3.3 Electrolytic decoupling capacitors

The choice of Vcc electrolytic decoupling capacitor depends on the value of the I/V resistor in an inverse relationship. As the I/V resistor decreases, the capacitor needs to increase. If the capacitor is too low in value for the I/V resistor, the sound with be anemic, with weak bass and dynamics, but more 3D with deep soundstage. If the capacitor value is too high for the I/V resistor, the depth of soundstage will decrease.

For a 430R I/V resistor, 1800-2200uF is about right. You can use capacitors in parallel for better sound quality. Additional caps can be added in the open area below the module and between the output coupling caps. Connect positive to the 5V strip and negative to the 3V3 side GND strip with jumper wires. Listen to various values by press fitting before soldering.

## 3.4 Output coupling capacitors

These capacitors can vary considerably in size, quality and price. You may want to press fit these in place to try different types before soldering.

The value should be around 10 uF, but can be as low as 4.7 uF. With standard 10k impedence, 10 uF will give a low frequency -3dB of 1.59 Hz. 4.7 uF will give a low frequency -3dB of 3.38 Hz.  You want the low frequency cutoff to be well below 20 Hz, ideally less than 2 Hz.

A metal foil and film capacitor will be the highest quality, but physically larger and more expensive.  

A basic polypropylene metallized film capacitor is a WIMA MKP4 or MKP10 with radial leads. Digi-Key has MKP4D046806F00JSSD which is a 6.8uF 100VDC currently in stock. WIMA MKP are commonly available, competent sounding and are a good choice for this project. Generally, most polypropylene capacitors will be satisfactory. Panasonic ECW and EZP PP caps are good, and relatively small for capacitance. PP caps can also be purchased from speaker components suppliers, where they are used as crossover caps. For example, Parts-Express, Madisound, Speaker City USA, etc. It is best to get the lowest voltage rating available, which will reduce the size. Panasonic, Solen and WIMA PP caps tend to be neutral sounding without any annoying qualities.  Humble Homemade Hifi has some capacitor ratings.

Boutique film capacitors can be very expensive and are often too large for the available space on the HAT. Metallized polypropylene tend to be the best value.

# 4. Assembly and Testing

# 5. moOde configuration

# 6. Appendix
