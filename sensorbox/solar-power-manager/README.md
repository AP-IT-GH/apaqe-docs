# Analysis document Solar Power Manager (SPM)

- Jesse Zaenen
- Kobe Nevelsteen

# Expectations SPM

- Monitoring battery status
- Alert when battery is low
- Current in / out
- Controlling polling rate 
- Low power mode implementation
- Calculating power budget

# Different modules

| Designer     | Board                                     | IC                                                                             |                        |
| ------------ | ----------------------------------------- | ------------------------------------------------------------------------------ | ---------------------- |
| Sparkfun     | https://www.sparkfun.com/products/12885   | https://www.analog.com/media/en/technical-documentation/data-sheets/3652fe.pdf |                        |
| Adafruit     | https://www.adafruit.com/product/4755     | https://www.ti.com/lit/ds/symlink/bq24074.pdf                                  |                        |
| DFrobot      | https://www.dfrobot.com/product-1712.html | https://www.analog.com/media/en/technical-documentation/data-sheets/3652fe.pdf |                        |
| DFrobot      | https://www.dfrobot.com/product-1139.html |                                                                                | Obsolete               |

# Research IC’s

## Comparison

| IC | Price | Current | Nominal current | Number of cells | MPPT | Mosfet | I2C | package | link |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| LT3652EMSE | 7.250 € / 10 pieces Farnell | 2 A |  | … | Yes | No | No | 12-lead plastic MSOP package | http://bit.ly/LT3652EMSE |
| BQ24072RGTR | 1.75 € / 10 pieces Mouser | 1.5 A |  | 1 | No | No | No | VQFN-16 | https://bit.ly/BQ24072RGTR |
| BQ25798RQMR | 4.85 € / 10 pieces Mouser | 5 A |  | 1-4 | yes | yes | yes | VQFN-HR-29 | https://bit.ly/BQ25798RQMR |
| SPV1050TTR | 2.86 € / 10 pieces Mouser | 70 mA |  | / | yes | No | No | VFQFPN-20 | https://bit.ly/SPV1050TTR |
| MP2731GQC-0000-P | 2.39 € / 10 pieces Farnell | 4.5 A |  | 1 | No | yes | yes | QFN-26 | https://bit.ly/MP27x1GQC-0000-P |
| SPV1040 | 3.38 € / 10 pieces Mouser | 1.65 A | 0.6 A | 1 | yes | yes | No | TSSOP8 | http://bit.ly/3TsVsnQ |

## Example schematics

### LT3652EMSE

<img src="sensorbox/solar-power-manager/images/lt3652.png" alt="lt3652" width="75%" height="auto">

### BQ24072RGTR

<img src="sensorbox/solar-power-manager/images/bq24075.png" alt="bq24075" width="75%" height="auto">

### BQ25798RQMR

<img src="sensorbox/solar-power-manager/images/bq25798.png" alt="bq25798" width="75%" height="auto">

### SPV1050TTR

<img src="sensorbox/solar-power-manager/images/BMS.png" alt="BMS" width="75%" height="auto">

### MP2731GQC-0000-P

<img src="sensorbox/solar-power-manager/images/mp2731.png" alt="mp2731" width="75%" height="auto">

### SPV1040

<img src="sensorbox/solar-power-manager/images/SPV1040.png" alt="mp2731" width="75%" height="auto">

## Shortlist IC selection

### LT3652
  -The LT3652 is a good candidate because it is relatively simple in design and not overkill for our application. It also supports MPPT, making it optimized to work with solar energy.
  -The downside is that it does not have I2C; however, we can use the open collector pins to trigger certain measurements.
  
  [Summary](sensorbox/solar-power-manager/resources/samenvattinglt3652_ENG.md)
  [3652fe.pdf](sensorbox/solar-power-manager/resources/3652fe.pdf)

### SPV1040

  - Another possibility is the SPV1040. The example schematic above shows us the simplicity of the implementation.
  - Over-voltage, over-current, reversed-polarity, and short-circuit protection
  - Soft start
  - Shutdown mode

  [Datasheet](sensorbox/solar-power-manager/resources/spv1040.pdf)
  [Application note](sensorbox/solar-power-manager/resources/SPV1040ApplicationNote.pdf)
  [Summary](sensorbox/solar-power-manager/resources/samenvattingspv1040_ENG.md)

**to use this IC with a LiPo battery, we need to use an external charge controller**
(SPV1040 Question, 2023)

### BQ52798
  - This IC is a real all-rounder, but the downside is that the implementation is quite complex.
  - Current and voltage measurement onboard.
  - Two different power sources possible.
  - Separate battery and load circuit.
  [bq25798.pdf](sensorbox/solar-power-manager/docs/resources/bq25798.pdf)

---

# Final comparison

|                        | SPV1040                                                                                                    | LT3652EMSE                     |
| ---------------------- | ---------------------------------------------------------------------------------------------------------- | ------------------------------ |
| External pin control   | -XSHUT                                                                                                     | - SHDN <br> - CHRG <br>- FAULT |
| Protection             | - overcurrent protection <br> - overtemperature protection <br> - Input source reverse polarity protection | - thermal foldback protection  |
| shutdown current       | 0.7 - 5 µA                                                                                                 | 15 µA                          |
| documentation          |                                                                                                            | More complete                  |

# Solar panel

Curently we are working with solar panels which were available at school. These were 5V. Because we need a minimum of 3,3V above the battery voltage, we need a bit more voltage at the input. Because of this we are connecting the panels in series.

1. Attach the panels in series instead of parallel.
2. Replace the panels by 12V variant.
   1. Connect in series for biggest buffer.
   2. Parallel to achieve the biggest charge current.

## Options

| Power (WP) | Dimensions | Voltage (V) | Max. voltage (V) | Max. current (mA) | Open circuit voltage (V) | Link                                                                                                                                         |
| -------- | ---------- | ---------------- | ----------------- | ---------------- | ------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| 5   | 230x185    | 12               | 18,3              | 280              | 22,5                | https://kleinezonnepanelen.nl/nl/zonnepanelen/kleine-zonnepanelen-5w-210w/5-watt-zonnepaneel-monokristal-afm-230x185-mm-stuks/a-419-10000124 |
| 1,5 | 115x85     | 12               |                   | 0-110            |                     | https://etronixcenter.com/nl/8168883-al129-oem-12v-15w-115x85mm-mini-zonnepaneel-7110213386853.html                                          |

<img src="sensorbox/solar-power-manager/images/5W.png" alt="5 watt zonnepanneel" width="30%" height="auto">

5 watt

<img src="sensorbox/solar-power-manager/images/1_5Wsolar.png" alt="1,5 watt zonnepanneel" width="30%" height="auto">

1,5 watt

# Battery

| Supplier   | Type                     | price (€)  | Voltage (V)  | Capaciteit (mAh) | link |
| ---------- | ------------------------ | -------- | ----------- | -------------- | ---- |
| LG chem    | INR18650MH1 (Lithium-ion)| 9,99     | 3,7         | 3000           | https://www.conrad.be/nl/p/lg-chem-inr18650mh1-speciale-oplaadbare-batterij-18650-geschikt-voor-hoge-stroomsterktes-li-ion-3-7-v-3000-mah-1558879.html |
| Jauch      | LP102530JU (Lipo)        | 18,99    | 3,7         | 700            | https://www.conrad.be/nl/p/jauch-quartz-lp102530ju-speciale-oplaadbare-batterij-prismatisch-kabel-lipo-3-7-v-700-mah-2141955.html|
| Jauch      | LP523450JU (Lipo)        | 19,99    | 3,7         | 950            | https://www.conrad.be/nl/p/jauch-quartz-lp523450ju-speciale-oplaadbare-batterij-prismatisch-kabel-lipo-3-7-v-950-mah-2141930.html|
| Adafruit   | ADA258 (Lipo)            | 11,91    | 3,7         | 1200       | https://www.gotron.be/lithium-ion-polymer-battery-3-7v-1200mah.html|

We chose for Lipo because of the many advantages over Li-ion as shown below.

| Specs             | Li-ion      | Lipo                       |
| ----------------- | ----------- | -------------------------- |
| Energy density    | 100 tot 250 | 130 tot 200                |
| Price             | Cheap       | expensive (double yo li-ion) |
| Dimennsion        | Big         | Small                      |
| loadtime          | Long        | Short                      |
| Explosion hazard  | Higer       | Lower                      |
| Weight            | Heavier     | Lighter                    |
| Safety            | Lower       | Higer                      |

(Robocraze, n.d.)

The Adafruit ADA258 Looks like a good choice. The loading and discharging of the battery happens preferably with a current of 500mA. This translates to 0,5C and makes sure the battery is charged in 2 hours.

## Charging and discharging

### C rating

<img src="sensorbox/solar-power-manager/images/C-rating.png" alt="C-rating" width="25%" height="auto">

### Load curve

<img src="sensorbox/solar-power-manager/images/charging_curve.png" alt="charging_curve" width="75%" height="auto">

### Discharge curve

<img src="sensorbox/solar-power-manager/images/Ontlaadcurve.png" alt="Ontlaadcurve" width="75%" height="auto">

## Battery monitoring

### Direct Reading via ADC

To read the battery percentage, we will use the ADC on the ESP32. Since there is already a circuit with MOSFETs to read the battery level without continuous power consumption, we will use this circuit.

### Establishing Connection via IC

Using I2C

[ds2745.pdf](resources/ds2745.pdf)

<img src="sensorbox/solar-power-manager/images/DS2745.png" alt="DS2745" width="50%" height="auto">

This IC features two ADCs that can be conveniently utilized to read the battery status and transmit it to the ESP32 via I²C.

Additionally, the IC exhibits low power consumption, as illustrated below:

| Active Current:         | Sleep Current:       |
| ----------------------- | -------------------- |
| 70µA typical, 100µA max | 1µA typical, 3µA max |

The only drawback is the presence of an additional temperature sensor. Since the LT3652 already incorporates a temperature sensor, this would result in a duplicate feature.

## Alerts

Once we have read the battery status, we can send it via LoRa, allowing the backend to provide alerts through the designated channels.

## Research amount of sun hours

<img src="sensorbox/solar-power-manager/images/zonuren.png" alt="zonuren" width="70%" height="auto">

## USB supply?

Adding USB-C power delivery would be a useful additional feature, allowing the battery to be charged without relying on solar energy.

## Current on / out

- NA = Not applicable

**Out**

| Component     | Active current consumption | Sleepmode current consumption | Clock  |
| ------------- | -------------------------- | ----------------------------- | ------ |
| LT3652        | 85µA (standby)             | 15µA                          |        |
| ATtiny412-SSN | 2.8 mA                     |                               | 5 MHz  |
|               | 4.8 mA                     |                               | 10 Mhz |
|               | 9.0 mA                     |                               | 20 Mhz |
|               | 18 µA                      |                               | 32 KHz |
|               |                            | 0.71 µA                       | /      |
| DS2745        | 70µA typical, 100µA max    | 1µA typical, 3µA max          |        |
| ht3786d       | \*NG                       | \*NG                          |        |

**In**

| Component  | Current supply | Voltage supply |
| ---------- | -------------- | -------------- |
| Solarpanel | 200 mA         | 5 V            |

<img src="sensorbox/solar-power-manager/images/USBpower.png" alt="USB power standards" width="50%" height="auto">

[Solved:
Micro USB Amps | Experts Exchange](https://www.experts-exchange.com/questions/28686112/Micro-USB-Amps.html)

# Current measurement IC

- INAx180

  - 2,7 tot 5.5V power supply
  - Common-mode range (VCM): -0,2 tot 26V
  - Hoge bandbreedte 350kHz
  - +- 1% gain error
  - 260µA

  <img src="sensorbox/solar-power-manager/images/INAX180.png" alt="INAX180" width="50%" height="auto">

  We used the INA in the final design, not realising the output is a milivolt per 100mA of charging/discharging. To fix this issue we added an OPAMP with 100x amplification to the connection between power manager and sensorbox. This needs to be added in a future iteration.


# MOSFET Provision

The capability to turn the charging module on and off is already integrated within the IC. Therefore, the addition of an external MOSFET is not necessary. When pin 3 / SHDN of the LT3652 is pulled low or falls below a voltage level of 0.4V, the IC will cease charging. During this state, the input supply bias is reduced to 15µA.

# Calculating a power budget

Without detailed information on component power consumption and selection is currently impractical. Therefore, it is advisable to postpone this task until further information is available. However, we have already determined the method for measuring current in the practical circuit.

## Power budget

(original setup)

<img src="sensorbox/solar-power-manager/images/powerBudget.png" alt="alt text" width="60%" height="auto">

# Terminology

| Term                  | Definition                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| MPPT                  | "Maximum Power Point Tracking” is used in solar panel chargers to optimize the match between the solar panels and the battery. The main difference between MPPT and PWM charging (the less efficient counterpart) is that MPPT determines the solar panel voltage based on the battery level and sunlight intensity to achieve the highest efficiency. (What is Maximum Power Point Tracking, n.d.)                                                                                                          |
| PWM                   | Pulse Width Modulation, a technique to manage to loadcurrent.                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| OPAMP                 | Operational Amplifier (Op-amp), an integrated circuit (IC) that can be used to amplify or compare signals.                                                                                                                                                                                                                                                                                                                                                                                                   |
| SPI                   | Serial Peripheral Interface (SPI), a communication protocol for connecting integrated circuits (ICs).                                                                                                                                                                                                                                                                                                                                                                                                        |
| Polling rate          | Frequency at which a certain value is checked.                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Open collector output | These outputs are commonly found on various ICs. They are essentially connected to a transistor with the emitter connected to ground. When the output is not driven, this open-collector pin will be high-impedance. When it is driven, a low resistance path to ground will be provided through the transistor. Therefore, we can, for instance, connect a pull-up resistor to the collector to drive another IC or use a series resistor with an LED for status monitoring. (Open Collector Outputs, n.d.) |

| C-rating | Unit of current at which a battery is charged and discharged. Typically, a battery is rated at 1C, which means a 1Ah battery can deliver 1 ampere of current for one hour. The same battery with a discharge rating of 0.5C will deliver current for 2 hours at 0.5 ampere per hour. The same analogy can be used for charging the batteries. (Power Sonic) |

# References

_Open Collector Outputs_. (sd). Retrieved from
ElectronicTutorials: [https://www.electronics-tutorials.ws/transistor/open-collector-outputs.html](https://www.electronics-tutorials.ws/transistor/open-collector-outputs.html)

_What is Maximum Power Point Tracking_. (sd). Retrieved from
NAZ: [https://www.solar-electric.com/learning-center/mppt-solar-charge-controllers.html/](https://www.solar-electric.com/learning-center/mppt-solar-charge-controllers.html/)

Robocraze. (sd). _Lithium-Ion vs Lithium Polymer battery_. Retrieved from robocraze: https://robocraze.com/blogs/post/lithium-ion-vs-lithium-polymer-battery

Power Sonic. (sd). _What is a battery C rating?_ Retrieved from power-sonic: https://www.power-sonic.com/wp-content/uploads/2021/02/What-is-a-battery-C-rating.pdf

SPV1040 question. (2023, 5 juli). https://community.st.com/t5/power-management/spv1040-question/td-p/227282
