# Summary

**Advantages:**

- Voltage range ideal for 2 solar panels in parallel, like the current setup
- MPPT (Maximum Power Point Tracking)
- Soft start
- Various protections
- Up to 95% efficiency
- TSSOP8 package

**Disadvantages:**

- Has only 1 pin for external control (XSHUT)
- Might be somewhat simplistic (could also be an advantage)

## Pinout

<img src="sensorbox/solar-power-manager/analysis/images/pinoutspv1040.png" alt="pinout spv1040" width="50%" height="auto">

## Electrical Characteristics

| Symbol | Parameter            | Min | Typ | Max | Unit |     |
| ------ | -------------------- | --- | --- | --- | ---- | --- |
| ISD    | Shutdown current     | n/a | 0.7 | 5   | µA   |     |
| Vout   | Output voltage range | 2   | n/a | 5.2 | V    |     |
| Pout   | Max output power     | n/a | n/a | 3   | W    |     |

## Bedenkingen

Bij het nalezen van de applicatie notes zijn we erachter gekomen dat het evaluatiebord niet werkt met lithium technologie. Bij verder onderzoek blijkt dat de IC ontwikkelt is voor supercondensatoren, lood batterijen en simpele LiFePO4 toepassingen.
When we reread the application notes we concluded that the evaluation board doesn't work with lithiul technology. Wit further investigation it seems that the IC is develloped for supercapacitors, lead batteries and simple LiFePO4 appliences.

**to use this IC(integrated circuit) with a LiPo battery we need to make use of an extern charge controller**
