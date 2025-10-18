#  Ngspice + Sky130 — Day 3

## CMOS Switching Threshold & Dynamic Simulations

---

## 📘 1. Introduction / Background

This session focuses on analyzing the switching threshold of the CMOS inverter and exploring its static and dynamic performance through SPICE simulations.

**Why it matters:**

* The switching threshold voltage (VM) is a key parameter that determines noise margins and logic robustness.
* By adjusting PMOS and NMOS sizing ratios, we can tune VM to improve inverter symmetry and noise immunity.
* Dynamic simulations help observe real switching behavior, including rise/fall delay and transition slopes, which directly influence timing in real digital circuits.

---

##  2. Theory & Key Concepts

### 2.1 Switching Threshold (VM)

* VM is the input voltage at which the inverter output equals the input.
* At VM, both NMOS and PMOS are in saturation.
* VM ≈ VDD/2 gives maximum noise margin.
* PMOS is usually sized larger than NMOS to compensate for lower mobility.

### 2.2 Relation Between VM and Sizing Ratio

* The ratio of PMOS to NMOS sizing (Wp/Lp vs Wn/Ln) determines VM.
* Typically, the ratio is chosen around 2–3 to balance the inverter.

### 2.3 Dynamic Behavior of CMOS Inverter

* Output transitions are determined by charging/discharging of the load capacitance.
* tPLH — propagation delay for LOW→HIGH transition
* tPHL — propagation delay for HIGH→LOW transition
* Average propagation delay tp = (tPLH + tPHL)/2
* PMOS and NMOS sizing strongly affect rise/fall asymmetry. Larger PMOS width improves charging, leading to more balanced delay.

---

## 🧰 3. SPICE Simulation Setup

| Exp No. | Experiment                                   | Description                                  |
| ------- | -------------------------------------------- | -------------------------------------------- |
| 30-L1   | SPICE deck creation for CMOS inverter        | Build basic inverter netlist                 |
| 31-L2   | SPICE simulation for CMOS inverter           | DC sweep for static VTC                      |
| 32-L3   | Labs Sky130 SPICE simulation                 | Verify inverter behavior using Sky130 models |
| 33-L1   | Switching threshold VM                       | Identify VM from VTC                         |
| 34-L2   | Analytical expression of VM                  | Theoretical derivation                       |
| 35-L3   | Inverse sizing from VM                       | Derive Wp/Lp, Wn/Ln from VM                  |
| 36-L4   | Static and dynamic simulation                | DC + transient analysis                      |
| 37-L5   | Dynamic simulation with increased PMOS width | Observe effect on rise time and VM           |
| 38-L6   | Application in clock network and STA         | Discuss timing significance                  |

---

## 🧾 4. Example SPICE Netlist

```spice
* CMOS inverter - Sky130
.include ./sky130_fd_pr__nfet_01v8.model
.include ./sky130_fd_pr__pfet_01v8.model

Vdd vdd 0 1.8
Vin in 0 PULSE (0 1.8 0n 100p 100p 2n 4n)
CL out 0 10f

M1 out in 0 0 sky130_fd_pr__nfet_01v8 w=1u l=0.15u
M2 out in vdd vdd sky130_fd_pr__pfet_01v8 w=2u l=0.15u

* DC sweep for VTC
.DC Vin 0 1.8 0.01
.print DC V(out)

* Transient simulation
.tran 0.01n 10n
.print tran V(in) V(out)

.end
```

---

## 📊 5. Plots & Figures

* VTC Curve with switching threshold VM marked
* Transient Response showing rise/fall times
* Effect of PMOS sizing on threshold and delays
* Noise margins annotated
<img width="690" height="542" alt="Screenshot from 2025-10-18 15-14-42" src="https://github.com/user-attachments/assets/361577df-ad87-4b1a-aa11-979a5a04cda2" />

---

## 📑 6. Tabulated Results

| Parameter         | Value (Example)     | Notes                                     |
| ----------------- | ------------------- | ----------------------------------------- |
| VTHN              | 0.45 V              | Extracted from NMOS transfer              |
| VTHP              | -0.47 V             | PMOS transfer                             |
| VM                | 0.90 V              | Switching threshold                       |
| βp/βn             | 2.1                 | Achieved by sizing PMOS                   |
| tPLH              | 85 ps               | Rise propagation delay                    |
| tPHL              | 60 ps               | Fall propagation delay                    |
| NMH               | 0.72 V              | High noise margin                         |
| NML               | 0.70 V              | Low noise margin                          |
| PMOS width effect | ↑ Rise time ↓ delay | Larger PMOS improved charging performance |

---

## 📝 7. Observations / Analysis

* VM shifts depending on PMOS/NMOS sizing ratio.
* Larger PMOS:

  * Slight upward shift in VM
  * Rise time improves
  * Noise margins become more symmetric
* Transient simulation confirms dynamic behavior matches theoretical prediction.
* Balanced sizing is key for clock network design to minimize skew.

---

## ⚡ CMOS Inverter Transient Simulation — Output vs. Time

* Captures dynamic response when input toggles between logic 0 and 1.
* Allows extraction of rise time, fall time, and propagation delay.

---

## 🧰 1. SPICE Netlist — Transient Simulation

```spice
* CMOS Inverter Transient Simulation - Sky130
.param temp=27
.lib "sky130_fd_pr/models/sky130.lib.spice" tt

XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=0.84 l=0.15
XM2 out in 0   0   sky130_fd_pr__nfet_01v8 w=0.36 l=0.15

Cload out 0 50fF

Vdd vdd 0 1.8V
Vin in 0 PULSE(0V 1.8V 0 0.1ns 0.1ns 2ns 4ns)

.tran 1n 10n

.control
run
plot v(in) v(out)
.endc

.end
```
 Plots & Figures
 
 <img width="1204" height="764" alt="Screenshot from 2025-10-18 15-24-07" src="https://github.com/user-attachments/assets/d9ebc0ba-acff-4964-8f97-1686a23283ac" />

---

## 📊 2. Key Timing Parameters

| Parameter                 | Symbol | Description                        |
| ------------------------- | ------ | ---------------------------------- |
| Rise Time                 | tr     | Output rise from 10% to 90% of VDD |
| Fall Time                 | tf     | Output fall from 90% to 10% of VDD |
| Propagation Delay (L→H)   | tPLH   | Delay from input low→high          |
| Propagation Delay (H→L)   | tPHL   | Delay from input high→low          |
| Average Propagation Delay | tp     | Average of tPLH and tPHL           |

* Output lags input due to charging/discharging of 50 fF capacitor.

---


## 📝 5. Observations

* tPHL is typically smaller than tPLH because NMOS is stronger than PMOS.
* Increasing Wp can balance rise/fall times.
* Larger load capacitance increases delay.
* Delay values are critical for static timing analysis (STA).

---

## 🧭 8. Applications in STA

* Switching threshold determines logic ‘1’ and ‘0’ recognition levels.
* Variation in VM affects noise margins → timing robustness.
* PMOS/NMOS sizing tuning is used in clock buffers and timing-critical paths to control:

  * Rise/fall time balance
  * Transition slew
  * Skew in clock tree
* Essential in static timing analysis for real chip design.

---

## 📚 9. References

* Sky130 PDK Documentation — SkyWater Technology Foundry
* CMOS VLSI Design — Neil Weste & David Harris
* Digital Integrated Circuits — Jan M. Rabaey

---
