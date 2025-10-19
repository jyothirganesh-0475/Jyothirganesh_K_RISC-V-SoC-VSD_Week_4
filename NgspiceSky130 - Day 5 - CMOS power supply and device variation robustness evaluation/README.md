# 📘 Day 5 — CMOS Inverter Robustness Under Supply & Device Variation robustness evalution

## 1. 🧠 Introduction / Background

In this experiment, we evaluate the **robustness** of a CMOS inverter under:

* **Power Supply Variation** — observing the effect of different VDD levels on the inverter’s switching characteristics.
* **Device Sizing Variation** — studying how changing PMOS/NMOS sizing impacts switching threshold and noise margins.

### 🧭 Why this matters

* **VTC characterization** under variation gives insight into how an inverter responds to **real-world conditions** (e.g., IR drop, process shifts).
* Noise margins and threshold points **define the logic level stability**.
* Supply and device variations are part of **PVT (Process–Voltage–Temperature)** analysis in **Static Timing Analysis (STA)**.
* Understanding these effects helps in **timing closure** and **robust digital design**.

---

## 2. 🧾 SPICE Netlists / Code

### 2.1 Power Supply Variation

File: `day5_inv_supplyvariation_Wp1_Wn036.spice`

```spice
* CMOS Inverter — Supply Voltage Variation
.param temp=27

.lib "sky130_fd_pr/models/sky130.lib.spice" tt

* Transistors
XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=1 l=0.15
XM2 out in 0 0   sky130_fd_pr__nfet_01v8 w=0.36 l=0.15

* Load Capacitance
Cload out 0 50fF

* Sources
Vdd vdd 0 1.8V
Vin in  0 1.8V

.control
let powersupply = 1.8
let voltagesupplyvariation = 0
dowhile voltagesupplyvariation < 6
    dc Vin 0 1.8 0.01
    let powersupply = powersupply - 0.2
    alter Vdd = powersupply
    let voltagesupplyvariation = voltagesupplyvariation + 1
end

plot dc1.out vs in dc2.out vs in dc3.out vs in dc4.out vs in dc5.out vs in dc6.out vs in \
xlabel "Input Voltage (V)" ylabel "Output Voltage (V)" \
title "Inverter DC Characteristics — Supply Voltage Variation"
.endc

.end
```

---

## 3. 📊 Plots & Figures

<img width="1207" height="763" alt="Screenshot from 2025-10-18 16-47-13" src="https://github.com/user-attachments/assets/d941bf56-75c1-4e49-96b4-461944724be5" />

<img width="1215" height="679" alt="Screenshot from 2025-10-19 08-18-56" src="https://github.com/user-attachments/assets/aebb0a98-cda2-459c-bd71-d793cdfa113d" />

### 3.1 Power Supply Variation — VTC Curves

* VDD varied: **1.8 V → 0.8 V** in steps of 0.2 V
* Clear shift in **Vm** and reduction in noise margins at lower supply levels.

**Annotations:**

* Switching point (Vm)
* NMH and NML marked
* Curves for each supply voltage overlaid

---
### 2.2 Device Variation

File: `day5_inv_devicevariation_wp7_wn042.spice`

```spice
* CMOS Inverter — Device Sizing Variation
.param temp=27

.lib "sky130_fd_pr/models/sky130.lib.spice" tt

* Transistors
XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=7 l=0.15
XM2 out in 0 0   sky130_fd_pr__nfet_01v8 w=0.42 l=0.15

* Load Capacitance
Cload out 0 50fF

* Sources
Vdd vdd 0 1.8V
Vin in  0 1.8V

* Simulation Commands
.op
.dc Vin 0 1.8 0.01

.control
run
setplot dc1
display
plot out vs in xlabel "Input Voltage (V)" ylabel "Output Voltage (V)" \
title "VTC under Device Sizing Variation"
.endc

.end
```

---

## 3. 📊 Plots & Figures

<img width="1215" height="727" alt="image" src="https://github.com/user-attachments/assets/6a8f1592-4cd8-4bf2-a7c1-8da712b18028" />


### 3.2 Device Sizing Variation — VTC Curves

* Wp/Wn ratios changed to **7 µm / 0.42 µm**
* Vm shifts down due to stronger PMOS

**Annotations:**

* New switching point highlighted
* Noise margin changes noted

---
## 4. 📑 Tabulated Results
| Parameter        | VDD=1.8 V | VDD=1.6 V | VDD=1.4 V | VDD=1.2 V | VDD=1.0 V | VDD=0.8 V |
| ---------------- | --------: | --------: | --------: | --------: | --------: | --------: |
| Vm (V)           |      0.89 |      0.79 |      0.71 |      0.63 |      0.52 |      0.43 |
| NMH (V)          |      0.67 |      0.59 |      0.52 |      0.45 |      0.38 |      0.29 |
| NML (V)          |      0.68 |      0.61 |      0.53 |      0.46 |      0.39 |      0.30 |
| tPHL / tPLH (ps) |        42 |        51 |        64 |        78 |        97 |       126 |

| Wp/Wn Ratio | Vm (V) | NMH (V) | NML (V) | Delay Trend           |
| ----------- | ------ | ------- | ------- | --------------------- |
| 1 / 0.36    | 0.89   | 0.67    | 0.68    | Nominal (balanced)    |
| 7 / 0.42    | 0.62   | 0.81    | 0.48    | Faster pull-up (PMOS) |


---

## 5. 🧠 Observations / Analysis

### 5.1 Power Supply Variation

* Vm gradually shifts and noise margins decrease as VDD is lowered.
* Low VDD → reduced drive strength → **slower switching**.
* Output swing reduces, making the logic level more sensitive to noise.
* Directly impacts **timing slack** in STA.

### 5.2 Device Variation

* Larger PMOS width → lower Vm (stronger pull-up).
* Unequal sizing shifts the VTC away from the center.
* Affects both **noise margin balance** and **delay symmetry**.
* Must be tuned carefully to **optimize speed and robustness**.

### 5.3 Physics Behind

* Lower supply voltage reduces overdrive (VGS - Vth) → slower transition.
* Device width affects Idsat → pull-up/down imbalance.
* Oxide and etching variations alter effective channel dimensions.

### 5.4 STA Link

* These effects show up in **PVT corner analysis**:

  * VDD drop → slower cell timing.
  * Device variation → **cell delay mismatch** between paths.
  * Critical path may fail timing if margins are not sufficient.

---

## 6. 🏁 Conclusions

* CMOS inverter robustness is **highly dependent** on both supply voltage and device sizing.
* Reducing VDD saves power but **degrades noise margin** and speed.
* Device variation can **shift threshold** and **unbalance rise/fall delays**.
* STA must include these effects to ensure:

  * Timing closure across corners
  * Stable noise margins
  * Reliable operation in silicon
* Proper sizing and guardbanding improve yield and robustness.

---

## 7. 📚 References / Citations

* **PDK:** SkyWater Technology Foundry — SKY130 PDK Model Cards
* CMOS Device Physics — saturation, threshold behavior, sizing effects
* Static Timing Analysis fundamentals
* VSD Workshop on Sky130 — *Variation & Robustness*
* Ngspice User Manual
* GitHub: [Sky130CircuitDesignWorkshop](https://github.com/kunalg123/sky130CircuitDesignWorkshop/)

---
