# ⚡ Day 2 — Velocity Saturation & CMOS Inverter VTC (SKY130)

## 📘 Introduction / Background

This module focuses on understanding **short-channel effects**, particularly **velocity saturation**, and how it impacts the **Voltage Transfer Characteristics (VTC)** of a CMOS inverter.

At lower technology nodes (e.g., 130 nm and below), the electric fields inside the channel can become very large, leading to the saturation of carrier velocity — **deviating from the ideal quadratic current–voltage relationship** used for long-channel devices. This affects:

* Drain current characteristics (Id–Vgs and Id–Vds curves)
* Effective threshold voltage
* Switching speed and VTC shape of CMOS inverters

### Key Concepts Covered

* Velocity saturation in NMOS transistors
* Id–Vgs characteristics for long vs short channel devices
* Threshold voltage extraction
* MOSFET as a switch
* VTC derivation and SPICE simulation
* Effect of device scaling on switching point and noise margins

---

## 🧪 Part A — Velocity Saturation Effect

### A1. **SPICE Simulation for Lower Nodes**

#### **Objective:**

To observe how velocity saturation alters the drain current behavior in short-channel devices compared to long-channel devices.

#### **SPICE Netlist — Id–Vds Sweep**

```spice
*Model Description
.param temp=27


*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt


*Netlist Description



XM1 Vdd n1 0 0 sky130_fd_pr__nfet_01v8 w=0.39 l=0.15

R1 n1 in 55

Vdd vdd 0 1.8V
Vin in 0 1.8V

*simulation commands

.op
.dc Vdd 0 1.8 0.1 Vin 0 1.8 0.2

.control

run
display
setplot dc1
.endc

.end

```
* Plot Id V/s Vds
* <img width="696" height="543" alt="Screenshot from 2025-10-18 12-48-24" src="https://github.com/user-attachments/assets/a993aaf9-82b7-43e5-950f-a62913b1b09b" />

#### **SPICE Netlist — Id–Vgs Sweep**

```spice
*Model Description
.param temp=27


*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt


*Netlist Description

XM1 Vdd n1 0 0 sky130_fd_pr__nfet_01v8 w=0.39 l=0.15

R1 n1 in 55

Vdd vdd 0 1.8V
Vin in 0 1.8V

*simulation commands

.op
.dc Vin 0 1.8 0.1

.control

run
display
setplot dc1
.endc

.end
```
* Plot Id v/s  Vgs
  <img width="696" height="543" alt="image" src="https://github.com/user-attachments/assets/f78b8b96-677a-48c5-b4ad-784f55d420a7" />

---

### A2. **Drain Current vs Gate Voltage — Long vs Short Channel**

* Long-channel: Current increases quadratically with Vgs after threshold.
* Short-channel: Current deviates from quadratic behavior due to carrier velocity saturation.

📊 *Threshold voltage (Vt) can be extracted from the linear extrapolation of Id–Vgs.*

| Parameter | Long Channel (L=1 µm)   | Short Channel (L=0.15 µm) |
| --------- | ----------------------- | ------------------------- |
| Vt        | 0.47 V                  | 0.41 V                    |
| Idsat     | Higher Quadratic Growth | Saturated Earlier         |

---

### A3. **Velocity Saturation Model**

This is one of the effect for short channel. Conventionally there are three mode of operation of mosfet viz cut off, linear and saturation region, however for lower nodes there are four mode of operation with velocity saturation region being one apart from the other three. In velocity saturation effect it is observe that for lower value of electric field the velocity is linear however after a certain time or higher electric field the velocity starts to saturate or becomes constant due to an effect known as scattering effect.

![IMG](https://github.com/user-attachments/assets/231c002d-67f6-4329-a44f-b206a1d063ef)

For short-channel devices,
[
I_{ds} = W C_{ox} v_{sat} (V_{gs} - V_t)
]
when
[
E \geq E_{crit} \quad \text{(critical electric field)}
]
This linear relation (instead of quadratic) limits current drive.

![IMG](https://github.com/user-attachments/assets/ad389b91-da04-49c1-8cd7-885007d4a41f)

---

## 🧪 Part B — CMOS Inverter Voltage Transfer Characteristics (VTC)

### B1. **MOSFET as a Switch**

* When **Vin < Vt (NMOS)** → NMOS off, PMOS on → Output ≈ VDD
* When **Vin > Vt (PMOS)** → NMOS on, PMOS off → Output ≈ GND

This forms the basis of inverter switching.

---

### B2. **VTC Derivation Steps**

#### Step 1: Convert PMOS gate-source voltage to Vin

[
V_{SGP} = V_{DD} - V_{in}
]

#### Step 2 & 3: Convert PMOS and NMOS drain-source voltage to Vout

[
V_{DSN} = V_{out}, \quad V_{SDP} = V_{DD} - V_{out}
]

#### Step 4: Merge PMOS–NMOS Load Curves

* Solve ( I_{DN} = I_{DP} ) for each Vin
* The intersection gives **switching point** of the inverter.
* Plot **Vout vs Vin** to obtain the VTC.

---

## 🧠 Observations / Analysis

* Short-channel devices experience **velocity saturation**, leading to:

  * Reduced current at higher Vgs.
  * Shift in threshold voltage.
  * Impact on switching speed and inverter gain.

---

## 🏁 Conclusions

* **Velocity saturation** becomes dominant at short channel lengths, flattening Id–Vgs curves.
* This limits current drive and affects digital timing characteristics.
* **VTC** captures inverter behavior and is essential to **timing and noise margin** analysis.
* Transistor-level effects directly **impact STA margins and critical paths** in large designs.

---

## 📚 References

* SkyWater Technology Foundry PDK Documentation
* Ngspice User Guide
* “Digital Integrated Circuits” — Jan M. Rabaey
* “CMOS Digital Integrated Circuits” — Neil H. E. Weste
* GitHub: [Sky130CircuitDesignWorkshop](https://github.com/kunalg123/sky130CircuitDesignWorkshop/)
---


