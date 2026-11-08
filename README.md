# Two-Stage RC Coupled Amplifier Characterization Unit

A complete design-to-validation project for a two-stage RC coupled common-emitter BJT amplifier, covering analytical design, QSPICE simulation, perfboard hardware implementation, and experimental frequency-response characterization.

**Course:** Mini Project — Third Year B.Tech (Electrical Engineering), Semester V  
**Institution:** Department of Electrical Engineering, COEP Technological University, Pune  
**Academic Year:** 2026–2027  
**Guide:** Dr. V. S. Rajguru

---

## Overview

This project takes a two-stage audio-frequency amplifier through the full engineering cycle: from first-principles bias calculations and pole–zero analysis, through Qorvo QSPICE simulation, to a soldered perfboard unit tested with bench instruments. The central goal is to quantify how closely a hand-built circuit matches its simulation — and to explain every significant deviation.

The amplifier uses two **BC547B NPN BJTs** in the common-emitter (CE) configuration, coupled by an RC network, powered from a **12 V DC supply**, and targets a **mid-band voltage gain of ~200 V/V** over **100 Hz to 10 kHz**.

---

## Key Specifications and Results

| Parameter | Design Target | QSPICE Simulation | Hardware Measurement |
|---|---|---|---|
| Mid-band gain | ~200 V/V | 226 V/V (47.1 dB) | 222 V/V (46.9 dB) |
| Lower cut-off f_L | ≤ 100 Hz | ≈ 103 Hz | ≈ 118 Hz |
| Upper cut-off f_H | ≥ 10 kHz | ≈ 1.07 MHz | ≈ 410 kHz |
| Bandwidth | — | ≈ 1.07 MHz | ≈ 410 kHz |
| Supply voltage | 12 V | 12 V | 12 V |
| Quiescent current I_C | 1 mA | 1 mA | — |
| Q-point V_CE | ≈ 6 V | ≈ 6 V | — |

Deviation from simulation at 10 kHz (mid-band): **1.9%** — well within the ±10% target.

---

## Circuit Design

### Topology
- **Two identical common-emitter stages** with voltage-divider biasing, coupled through a series capacitor
- **Partial emitter bypass**: each stage splits the emitter resistance into a bypassed part (R_E1 = 1 kΩ, bypassed by 22 µF) and an unbypassed part (R_E2 = 200 Ω) for gain stability and predictability
- Each stage uses a **4.7 kΩ collector resistor** and **200 kΩ / 39 kΩ bias network**

### Component Values (per stage)

| Component | Value | Function |
|---|---|---|
| Q1, Q2 | BC547B NPN | Amplifying device |
| R_C (R1, R10) | 4.7 kΩ | Collector resistor |
| R_1 (R4, R6) | 200 kΩ | Upper bias resistor |
| R_2 (R5, R7) | 39 kΩ | Lower bias resistor |
| R_E1 (R2, R8) | 1 kΩ | Bypassed emitter resistor |
| R_E2 (R3, R9) | 200 Ω | Unbypassed emitter resistor |
| C_E (C2, C3) | 22 µF | Emitter bypass capacitor |
| C_in (C4) | 1 µF | Input coupling capacitor |
| C_C (C1) | 0.1 µF | Inter-stage coupling capacitor |
| C_out (C5) | 1 µF | Output coupling capacitor |
| R_L (R11) | 10 kΩ | Load resistor |

### Design Parameters
- β (h_FE) = 200, V_BE = 0.7 V, V_T = 26 mV (room temperature)
- Transconductance g_m = I_C / V_T = 1 mA / 26 mV ≈ 38.5 mA/V
- Small-signal r_π = β / g_m ≈ 5.2 kΩ

---

## Frequency Response Analysis

### Low-Frequency Pole–Zero Model
A five-capacitor transfer function H_L(s) was derived for the low-frequency region. Each capacitor contributes a pole (and each bypass capacitor also a zero). The theoretical lower cut-off, computed by solving |H_L(jω)|² = ½, is **54.7 Hz**, dominated by the two emitter-bypass networks (which contribute ~97% of the lower corner).

### Mid-Band Gain Calculation
Using the π-model with inter-stage loading accounted for:
- Stage-1 gain (loaded by Stage-2 input): A_v1 ≈ −15.0 V/V
- Stage-2 gain (loaded by R_L): A_v2 ≈ −15.7 V/V
- Overall: A_v = A_v1 × A_v2 ≈ 236 V/V (simulation: 226 V/V, hardware: 222 V/V)

### Coupling Method Selection — Pugh Matrix
Four inter-stage coupling methods were compared (RC, transformer, direct, impedance) across nine criteria. RC coupling was selected as the datum — every alternative scored worse overall, confirming it as the optimal choice for an audio-band discrete amplifier.

---

## Simulation (Qorvo QSPICE)

Simulations were run at three frequencies to characterise all three regions of the frequency response:

| Frequency | Region | Output V_pp (sim) | Gain (V/V) |
|---|---|---|---|
| 50 Hz | Low-frequency roll-off | 960–1000 mV | 96–100 |
| 10 kHz | Mid-band | 2.4–2.6 V | 240–260 |
| 500 kHz | High-frequency roll-off begins | 1.8–2.0 V | 180–200 |

An AC sweep confirmed f_L ≈ 103 Hz, f_H ≈ 1.07 MHz, and mid-band gain = 226 V/V (47.1 dB). Phase at mid-band ≈ 0° (two inverting stages, 360° total rotation).

---

## Hardware Implementation

### Build Process
1. **Breadboard prototype** — assembled first to verify topology and catch component procurement errors (incorrect resistor values were identified and corrected at this stage)
2. **Perfboard assembly** — components transferred and soldered; two stages arranged side-by-side along the signal path; input and output at opposite board ends to minimise feedback
3. **Test points** — four measurement nodes (TP1 at base, TP2 at inter-stage, TP3 at stage-2 collector, V_out across load) brought to accessible locations

### Test Instruments

| Instrument | Model | Use |
|---|---|---|
| Function / waveform generator | UNI-T UTG932E | Sinusoidal input, 5 mV rms |
| Digital storage oscilloscope | Tektronix TBS 1052B-EDU (50 MHz, 1 GS/s) | V_out peak-to-peak measurement |
| DC power supply | Multi-output regulated bench supply | +12 V rail |
| Multimeter | Laboratory digital | Continuity and resistor checks |

---

## Testing and Validation

### Measured Frequency Sweep Results (V_in = 5 mV rms)

| Frequency | Output V_pp | Practical Gain (V/V) | Gain (dB) | Deviation from simulation |
|---|---|---|---|---|
| 50 Hz | 1.23 V | 87 | 38.8 dB | 12.1 % |
| 10 kHz | 3.14 V | **222** | **46.9 dB** | **1.9 %** |
| 500 kHz | 2.0 V | 141 | 43.0 dB | 2.1 % |

### Deviation Discussion
- **10 kHz (1.9%)** — Excellent agreement. Confirms the bias design and gain calculation. Mid-band gain is set by resistor ratios and Q-point, which are well controlled.
- **500 kHz (2.1%)** — Good agreement. Residual difference attributed to stray (parasitic) capacitance from perfboard wiring and probe loading, absent from the simulation.
- **50 Hz (12.1%)** — Slightly exceeds the ±10% target. Attributed to the wide tolerance of the 22 µF electrolytic bypass capacitors (±10–20%), which dominate f_L. The practical f_L (118 Hz) is 15 Hz above the simulated value (103 Hz), consistent with capacitors below their nominal value raising the corner frequency.

### Debugging Log Summary

| Observed Problem | Cause | Corrective Action |
|---|---|---|
| Distorted output waveform | Congested solder joints on perfboard | Inspected and re-soldered joints |
| Unreliable connections | Missing jumper wires | Added jumper wires |
| Ground resistance | Poor solder joints on ground net | Continuity check and re-soldering |
| Incorrect resistor values | Procurement error | Procured and fitted correct values |
| Noise during testing | 12 V supply wiring coupling into circuit | Increased physical separation |
| Inconsistent generator signal | Instrument thermal drift | Verified input on oscilloscope before each measurement |

---

## Project Structure

```
├── report/
│   └── Two_Stage_RC_Coupled_Amplifier_Mini_Project_Report.pdf
├── simulation/
│   └── 2stagerce.qsch          # Qorvo QSPICE schematic
├── docs/
│   ├── bom.csv                  # Bill of materials (Appendix A)
│   └── pole_zero_analysis.pdf   # Low-frequency H_L(s) derivation (Appendix C)
└── README.md
```

> **Note:** Add your QSPICE schematic file and any supplementary data files to the directories above.

---

## Tools and Technologies

- **Simulation:** [Qorvo QSPICE](https://www.qorvo.com/design-hub/design-tools/interactive/qspice) (free)
- **Transistor model:** BC547B (native QSPICE device library)
- **Analysis methods:** Hybrid-π small-signal model, short-circuit time-constant method, pole–zero analysis
- **Design tools:** Pugh concept-selection matrix, P-diagram (parameter diagram), Critical-to-Quality (CTQ) parameters
- **Hardware:** Perfboard, soldering iron, bench supply, function generator, Tektronix DSO

---

## Concepts Demonstrated

- Voltage-divider bias design for β-independent Q-point stability
- Split emitter resistance (bypassed + unbypassed) for gain-stability trade-off
- Inter-stage loading and its effect on cascaded gain
- Low-frequency pole–zero analysis of a multi-capacitor network
- Bode plot construction from practical measurements
- Systematic debugging methodology (visual inspection → continuity → DC levels → signal injection)
- Comparison and deviation analysis between theory, simulation, and hardware

---

## Results Summary

The hardware achieved a **mid-band gain of 222 V/V (46.9 dB)** at 10 kHz, agreeing with the QSPICE simulation (226 V/V) to within **1.9%** and meeting the ≥200 V/V design target. The practical lower cut-off (f_L ≈ 118 Hz) fell close to but slightly above the 100 Hz target, with the deviation traced to electrolytic capacitor tolerance. The upper cut-off (f_H ≈ 410 kHz) substantially exceeds the 10 kHz requirement. The work demonstrates a reproducible design-to-validation cycle for a discrete multistage small-signal amplifier.


