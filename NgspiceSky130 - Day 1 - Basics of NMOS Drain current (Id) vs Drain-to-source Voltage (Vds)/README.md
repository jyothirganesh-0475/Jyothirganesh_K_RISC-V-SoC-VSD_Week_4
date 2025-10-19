# **Day 1 — NMOS Device Fundamentals & Id–Vds Characteristics**

## 🏁 **1. Introduction / Background**

The **NMOS transistor** is one of the two fundamental building blocks of CMOS (Complementary Metal-Oxide-Semiconductor) technology.
Understanding its **electrical characteristics** is crucial because:

*  **NMOS behavior directly impacts CMOS logic performance** — e.g., inverter switching threshold, rise/fall times, and static power.
* It forms the basis for **transistor-level delay models** used in static timing analysis (STA).
* ⚡ Device characteristics such as **threshold voltage (Vt)**, **transconductance**, and **saturation behavior** determine switching speed and power.

In a MOSFET, the **gate voltage** controls whether a **conductive channel** forms between the source and drain. This channel allows electrons to flow when the device is in inversion.

In this experiment:

* We study **Id–Vds** (output characteristics) and **Id–Vgs** (transfer characteristics).
* Simulate the device using Ngspice and SkyWater Technology Foundry **sky130** PDK.
* Extract threshold voltage Vt.
* Relate the device behavior to digital design concepts like switching threshold and drive strength.

> 💡 *Id–Vds characteristics help us visualize how the transistor behaves as a voltage-controlled current source, while Id–Vgs gives insight into threshold voltage and subthreshold behavior.*

---

##  **2. NMOS Device Physics & Operation**

### 2.1 Structure

An **NMOS transistor** is a four-terminal device with:

* **Drain (D)** — terminal through which current flows out.
* **Gate (G)** — controls channel formation (insulated by SiO₂).
* **Source (S)** — terminal through which carriers enter.
* **Body/Substrate (B)** — p-type bulk region.
<img width="478" height="406" alt="6384228715111282221516252" src="https://github.com/user-attachments/assets/ee97ba8e-7f97-4e4c-8f19-9940ad8afdd9" />

**Fabrication overview**:

* Built on **p-type substrate**.
* Source and drain are **n+ diffusion** regions.
* Gate oxide (SiO₂) provides insulation between gate and body.
* Polysilicon or metal gate controls inversion charge.

---

### ⚡ 2.2 Threshold Voltage & Channel Formation

* At **Vgs = 0 V**, the **pn junctions** between n+ source/drain and p-substrate are reverse biased.
  → No conduction between source and drain.

* As **Vgs** increases:

  * Positive charge builds up on the gate.
  * Holes in the p-substrate are repelled.
  * A **depletion region** forms.
  * At **Vgs = Vt**, electrons accumulate at the surface forming an **inversion layer (n-channel)**.

* Above Vt, this inversion layer acts as a **conductive channel**, allowing electrons to flow from source to drain when Vds is applied.

---

### 2.3 Body Effect

When a positive **Vsb** is applied:

* The depletion width widens.
* A larger **Vgs** is required to invert the surface.
* Threshold voltage **increases**, shifting Id–Vgs curve.
* This effect is captured in the SPICE model parameters (e.g., body-effect coefficient γ).

This body effect becomes critical in **bulk CMOS designs**, especially in analog and low-power circuits.

---

##  **3. NMOS Regions of Operation**

The MOSFET operates in three distinct regions depending on **Vgs** and **Vds**:

### 3.1 Cutoff Region

* **Condition:** ( V_{GS} < V_t )
* The gate voltage is too low to invert the channel.
* No conducting path between source and drain.
* Drain current:
  [
  I_D = 0
  ]
* Used when transistor should act as an **OFF switch** in digital logic.

---

### 3.2 Linear (Triode) Region

* **Condition:** ( V_{GS} > V_t ) and ( V_{DS} < (V_{GS} - V_t) )
* A strong inversion channel exists.
* Current increases **linearly with Vds** for small Vds values.
* Device behaves like a **voltage-controlled resistor**.
  [
  I_D = μ_n C_{ox} \frac{W}{L} \left[(V_{GS}-V_t)V_{DS} - \frac{V_{DS}^2}{2}\right]
  ]
* Important for **analog circuits**, **switching transients**, and **small-signal modeling**.

---

### 3.3 Saturation Region

* **Condition:** ( V_{DS} ≥ (V_{GS} - V_t) )
* Channel **pinches off** near the drain.
* Current **saturates** and becomes relatively insensitive to Vds.
  [
  I_D = \frac{1}{2} μ_n C_{ox} \frac{W}{L} (V_{GS} - V_t)^2 (1 + λ V_{DS})
  ]
* ( λ ) is the channel length modulation parameter.
* Used when transistor acts as an **active current source**, common in digital gates and amplifiers.

---

## **4. SPICE Simulation Setup**

We use Ngspice with the **sky130 TT model** to simulate these characteristics.
Simulation flow:

1. Write SPICE netlist with NMOS instance and model file.
2. Perform `.dc` analysis for sweeping Vgs and Vds.
3. Extract **Vt** and plot **Id–Vds**, **Id–Vgs** curves.
4. Observe **region transitions** and **saturation behavior**.
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d55a0eec-cd90-4f32-9a7f-8cece2ba21f6" />

---

## 🧾 **5. SPICE Netlists / Code**

```spice 
*Model Description
.param temp=27


*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt


*Netlist Description



XM1 Vdd n1 0 0 sky130_fd_pr__nfet_01v8 w=5 l=2

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

---

## 📊 **6. Plots & Figures**

<img width="699" height="550" alt="Screenshot from 2025-10-18 10-02-56" src="https://github.com/user-attachments/assets/e0aba95b-3670-40da-9023-82f6ecc4bfc1" />

---

## 📋 **7. Tabulated Results**

| Parameter               | Measured (Example) |
| ----------------------- | ------------------ |
| Threshold Voltage (Vt)  | ~0.45 V            |
| Supply Voltage (Vdd)    | 1.8 V              |
| W / L                   | 5 µm / 2 µm        |
| Temp                    | 27 °C              |
| λ (channel length mod.) | From model file    |


---

## 🧠 **8. Observations / Analysis**

* Transition between regions is clearly visible.
* Vt ~ 0.45 V aligns with sky130 TT model specs.
* Increasing Vgs increases current in both linear and saturation regions.
* These curves form the **foundation for CMOS inverter analysis** in later experiments.

---

## 🧭 **9. Conclusions**

* NMOS device operation determines **logic gate performance**.
* Threshold voltage plays a key role in **noise margin** and **switching point**.
* Understanding Id–Vds helps in sizing transistors for speed vs. power tradeoffs.
* **Variation in Vt or Vdd** affects inverter delay and STA margins.

---

## 📚 **10. References / Citations**

* sky130 PDK Documentation (TT Corner)
* Ngspice User Manual
* VSD Sky130 Workshop
* Standard VLSI Device Physics Texts (e.g., CMOS VLSI Design: A Circuits and Systems Perspective)
* GitHub: [Sky130CircuitDesignWorkshop](https://github.com/kunalg123/sky130CircuitDesignWorkshop/)
---
