# Integerated-Universal-Precision-Power-Supply
Designed a universal precision power board in EasyEDA for upcoming instrumentation systems: USB C and DC input with LM66100 ideal diode ORing, TPS6216x buck digital rails, and an LT8330 boost and TPS63700 inverter feeding LT3042/LT3094 LDOs for clean ±5V and 3.3V analog rails, plus a buffered REF5025 2.5V reference.
# Universal Precision Power Board for Instrumentation Systems

> A dedicated, reusable power supply board that provides clean digital and analog rails for upcoming precision instrumentation projects.

**Author:** Devansh Sharma, 1st Year Integrated MSc, NISER Bhubaneswar
**Status:** Design complete (schematic, placement and routing). Physical bring up pending.

> **Note:** This README is a temporary placeholder. It is an AI generated overview of the completed design, written to give a quick idea of the project while the full documentation is being prepared. A detailed write up covering design decisions, layout, bring up and test results will replace it once the board has been assembled and tested.

## Overview

Precision analog circuits are only as good as the supply they run on. Switching regulator ripple, ground noise and a drifting reference all show up directly in the measurement. Instead of redesigning the power section for every new project, this board provides one universal, well filtered supply that future instrumentation boards can plug into.

The design separates the two jobs of a power supply: efficient switching regulators produce the digital rails, and ultra low noise LDOs produce the analog rails in a separate clean domain.

**Provides:**

1. USB Type C and external DC input with automatic source selection
2. 5V and 3.3V digital rails from efficient buck converters
3. Clean ±5V analog rails for bipolar op amp circuits
4. A clean +3.3V analog rail for low voltage analog and mixed signal circuits
5. A buffered precision 2.5V reference
6. Power good LEDs on major rails for quick visual checks

## Power Architecture

```
USB Type C (5V) ────────────────────┐
                                    ├→ LM66100 ×2 (Ideal Diode OR) → 5V rail
External DC IN → TPS62163 Buck → 5V ┘

5V rail ─┬→ TPS62162 Buck ────────────→ 3.3V digital                      (dirty domain)
         │
         ├→ LT8330 Boost → +6V ─┬→ LT3042 → +5V analog                    (clean domain)
         │                      └→ LT3042 → +3.3V analog
         │
         └→ TPS63700 Inverter → −6V → LT3094 → −5V analog                 (clean domain)

+5V analog → REF5025 (2.5V) → OPA140 Unity Gain Buffer → 2.5V REF
```

### Input and Source Selection

Two **LM66100** ideal diodes OR the USB Type C and external DC inputs. They automatically select the higher source with near zero voltage drop and stop one source from back feeding the other.

### Digital Rails

**TPS62163** and **TPS62162** buck converters generate the 5V and 3.3V digital rails for microcontrollers, logic and other digital loads. Buck converters are efficient, but their switching ripple makes them unsuitable for feeding sensitive analog circuits directly.

### LDO Headroom Stages

A low noise LDO needs some voltage above its output to regulate cleanly. A 5V input cannot produce a clean 5V output, so:

1. An **LT8330** boost converter raises 5V to **+6V**, giving the positive LDOs their headroom.
2. A **TPS63700** inverting converter generates **−6V** for the negative analog supply.

### Analog Rails

<table>
<tr><th>Rail</th><th>Regulator</th><th>Typical Use</th></tr>
<tr><td>+5V analog</td><td>LT3042</td><td>Positive supply for op amps and analog front ends</td></tr>
<tr><td>−5V analog</td><td>LT3094</td><td>Negative supply for bipolar signal swing</td></tr>
<tr><td>+3.3V analog</td><td>LT3042</td><td>Low voltage analog, ADCs and mixed signal circuits</td></tr>
</table>

The LT3042 and LT3094 are ultra low noise, high PSRR LDOs. They clean up the ripple left by the boost and inverter stages before it reaches the analog circuitry.

### Precision Reference

A **REF5025** generates a precision 2.5V reference, buffered by an **OPA140** in unity gain. The buffer lets the reference drive loads such as ADC reference inputs, bias points or mid supply references without its output voltage being pulled by the load.

## Clean and Dirty Domain Separation

1. Hard separation between the switching (dirty) and analog (clean) power domains, carried through the PCB placement and routing
2. Separate clean and dirty grounds, joined through controlled stitching so return currents from the switchers do not flow under analog circuits
3. Ferrite bead filtering between the switching and analog domains

## Bring Up Flexibility

Since this is a first revision, the board is designed to be tuned rather than redesigned:

1. Optional RC filter, snubber and bypass footprints throughout
2. DNP (Do Not Populate) footprints pre placed for extra decoupling, tuning and debugging
3. Power good LEDs on major rails for immediate visual validation

## Key Components

<table>
<tr><th>Part</th><th>Function</th></tr>
<tr><td>LM66100 ×2</td><td>Ideal diode ORing of USB and DC inputs</td></tr>
<tr><td>TPS62163</td><td>Buck regulator, DC input to 5V</td></tr>
<tr><td>TPS62162</td><td>Buck regulator, 5V to 3.3V digital</td></tr>
<tr><td>LT8330</td><td>Boost converter, 5V to +6V</td></tr>
<tr><td>TPS63700</td><td>Inverting converter, −6V generation</td></tr>
<tr><td>LT3042</td><td>Ultra low noise positive LDO (+5V and +3.3V analog)</td></tr>
<tr><td>LT3094</td><td>Ultra low noise negative LDO (−5V analog)</td></tr>
<tr><td>REF5025</td><td>2.5V precision voltage reference</td></tr>
<tr><td>OPA140</td><td>Unity gain reference buffer</td></tr>
</table>

## Current Status

<table>
<tr><th>Stage</th><th>Status</th></tr>
<tr><td>Schematic</td><td>Complete</td></tr>
<tr><td>PCB placement</td><td>Complete</td></tr>
<tr><td>Routing</td><td>Complete</td></tr>
<tr><td>Fabrication and assembly</td><td>Pending</td></tr>
<tr><td>Physical bring up and testing</td><td>Pending</td></tr>
</table>

The design has about 200 components and 127 nets.

## Next Steps

1. Fabricate and assemble the board
2. Bring up rail by rail, checking each rail's voltage and power good LED before enabling the next stage
3. Measure noise and ripple on each analog rail and the 2.5V reference
4. Complete the detailed project documentation
5. Use the board as the common power source for upcoming instrumentation systems

## References

1. [LM66100 Datasheet](https://www.ti.com/lit/ds/symlink/lm66100.pdf)
2. [TPS62160 Family Datasheet](https://www.ti.com/lit/ds/symlink/tps62160.pdf)
3. [LT8330 Datasheet](https://www.analog.com/media/en/technical-documentation/data-sheets/lt8330.pdf)
4. [TPS63700 Datasheet](https://www.ti.com/lit/ds/symlink/tps63700.pdf)
5. [LT3042 Datasheet](https://www.analog.com/media/en/technical-documentation/data-sheets/lt3042.pdf)
6. [LT3094 Datasheet](https://www.analog.com/media/en/technical-documentation/data-sheets/lt3094.pdf)
7. [REF5025 Datasheet](https://www.ti.com/lit/ds/symlink/ref5025.pdf)
8. [OPA140 Datasheet](https://www.ti.com/lit/ds/symlink/opa140.pdf)
