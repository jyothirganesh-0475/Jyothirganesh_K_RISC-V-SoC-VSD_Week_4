# 🧪 Ngspice + Sky130 — Day 4

## CMOS Noise Margin & Robustness Evaluation

---

## 📘 1. Introduction / Background

This session focuses on evaluating **static behavior**, **robustness**, and **noise margins** of a CMOS inverter.

**Why it matters:**

* **Noise margin (NM)** ensures that logic levels are correctly interpreted in digital circuits.
* Evaluating NM guarantees **robust operation** under voltage and process variations.
* Adjusting PMOS/NMOS sizing can enhance **inverter reliability** and **noise immunity**.

---

## 🧠 2. Theory & Key Concepts

### 2.1 Introduction to Noise Margin

Noise margin measures how much unwanted voltage variation (noise) a logic gate can tolerate without error.
Two types of margins define the robustness of logic levels:

* **NMH (High Noise Margin):** Input tolerance while still recognized as logic HIGH.
* **NML (Low Noise Margin):** Input tolerance while still recognized as logic LOW.

### 2.2 Noise Margin Voltage Parameters

* **VOH:** Output voltage when input is logic LOW (expected HIGH at output).
* **VOL:** Output voltage when input is logic HIGH (expected LOW at output).
* **VIH:** Minimum input voltage recognized as logic HIGH.
* **VIL:** Maximum input voltage recognized as logic LOW.

### 2.3 Noise Margin Summary

* NMH = VOH − VIH
* NML = VIL − VOL

Larger NM values imply **better noise immunity**.
The noise margin depends on **device sizing ratio (Wp/Wn)**, supply voltage, and fabrication process.

### 2.4 Noise Margin Variation with PMOS Width

* Increasing **PMOS width**:

  * Shifts switching threshold upward
  * Improves rise transition
  * Increases NMH and balances NML
* Decreasing PMOS width:

  * Reduces pull-up strength
  * Lowers NMH and overall robustness

---

## 🧰 3. SPICE Simulation Setup

| Exp No. | Experiment                             | Description                           |
| :-----: | -------------------------------------- | ------------------------------------- |
|  39-L1  | Introduction to noise margin           | Overview of NM and static robustness  |
|  40-L2  | Noise margin voltage parameters        | Extract VOL, VOH, VIL, VIH            |
|  41-L3  | Noise margin evaluation                | Measure NMH and NML                   |
|  42-L4  | Noise margin variation with PMOS width | Study effect of sizing on NM          |
|  43-L5  | Sky130 Noise margin labs               | Verify robustness using Sky130 models |

---

## 🧾 4. Example SPICE Netlist for Noise Margin Evaluation

```spice
*Model Description
.param temp=27


*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt


*Netlist Description


XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=1 l=0.15
XM2 out in 0 0 sky130_fd_pr__nfet_01v8 w=0.36 l=0.15


Cload out 0 50fF

Vdd vdd 0 1.8V
Vin in 0 1.8V

*simulation commands

.op

.dc Vin 0 1.8 0.01

.control
run
setplot dc1
display
.endc

.end

```
 * ## Plot
   <img width="1204" height="764" alt="Screenshot from 2025-10-18 16-25-36" src="https://github.com/user-attachments/assets/cc471098-04e6-4f72-9d1b-b7e6b8876dc6" />

---

## 📊 5. Observations & Analysis

<img width="1223" height="181" alt="Screenshot from 2025-10-18 16-29-40" src="https://github.com/user-attachments/assets/5ae3f5d2-9578-4ebf-9c44-445dde1e3fa5" />

### 5.1 Measured Results from Ngspice (Sky130)

|       Parameter      | Symbol | Value (V) | Description                          |
| :------------------: | :----: | :-------: | :----------------------------------- |
|      Output High     |   VOH  |   1.7188  | Output when input is logic 0         |
|      Output Low      |   VOL  |   0.1101  | Output when input is logic 1         |
|  Input Low Threshold |   VIL  |   0.7636  | Max input voltage recognized as LOW  |
| Input High Threshold |   VIH  |    0.98   | Min input voltage recognized as HIGH |
|   High Noise Margin  |   NMH  |   0.7388  | VOH − VIH                            |
|   Low Noise Margin   |   NML  |   0.6535  | VIL − VOL                            |

✅ **Result:**
The inverter exhibits **NMH = 0.7388 V** and **NML = 0.6535 V**, which are both large relative to VDD = 1.8 V, confirming **excellent static robustness** and **balanced design**.

---

### 5.2 Graphical Interpretation

On the **VTC (Vout vs Vin)** curve:

* Draw horizontal lines for **VOH** and **VOL**
* Draw vertical lines for **VIL** and **VIH**
* The horizontal distances (difference in voltage) between these intersections represent **NML** and **NMH**

This visually defines the **tolerance region** where the inverter can reject input noise without logical errors.

---

## 🧭 6. Applications

Noise margin evaluation is essential in:

* **Clock distribution networks** — ensuring reliable signal transitions
* **High-speed logic paths** — preventing metastability due to noise
* **Low-voltage digital systems** — where supply scaling reduces margins

Proper NM analysis enables designers to fine-tune **device sizing and layout** for maximum robustness.

---

## 📚 7. References

* SkyWater Technology Foundry — *Sky130 PDK Documentation*
* Neil Weste & David Harris — *CMOS VLSI Design*
* Jan M. Rabaey — *Digital Integrated Circuits*

---

✅ **Deliverables Included**

* Introduction & Theory
* SPICE Netlist (DC sweep)
* Ngspice Results with Noise Margin Calculation
* VTC Analysis & Robustness Discussion
* Sizing Variation Study

---
