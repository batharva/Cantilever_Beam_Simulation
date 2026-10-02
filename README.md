# Cantilever Beam Simulation

## Safety Factor of a Cantilever I-Beam Under Trapezoidal Loading

**DME Assignment — Problem 14**

This project presents a comparison between **analytical hand calculations** and an **ANSYS Mechanical Static Structural simulation** of a cantilever I-beam subjected to a linearly varying trapezoidal load.

The objective is to determine the maximum stress, maximum shear stress, safety factor, and deformation of the beam and compare the analytical and FEA results.

---

## 📌 Problem Statement

A **3 m cantilever I-beam** made of ductile structural steel is fixed at the wall at end **B** and free at end **A**.

The beam is subjected to a trapezoidal distributed load varying from:

- **12 kN/m at the free end A**
- **18 kN/m at the fixed end B**

The yield strength of the material is:

\[
\sigma_{yt}=250\text{ MPa}
\]

### Problem Parameters

| Parameter | Value |
|---|---:|
| Beam length | 3 m |
| Load at free end A | 12 kN/m |
| Load at fixed end B | 18 kN/m |
| Flange dimensions | 250 × 20 mm |
| Web thickness | 20 mm |
| Overall section depth | 340 mm |
| Material | Structural Steel |
| Yield strength | 250 MPa |
| Analysis | Static Structural |
| Software | ANSYS Mechanical 2026 R1 |

---

## 📐 Loading Condition

The trapezoidal load is implemented in ANSYS as a **tabular pressure** on the 250 mm wide top face.

Because:

\[
p=\frac{w}{b}
\]

where

- \(w\) = distributed load
- \(b=250\text{ mm}=0.25\text{ m}\)

the equivalent pressures are:

\[
p_A=\frac{12\,000}{0.25}=48\,000\text{ Pa}=48\text{ kPa}
\]

\[
p_B=\frac{18\,000}{0.25}=72\,000\text{ Pa}=72\text{ kPa}
\]

Therefore, the ANSYS pressure varies linearly from:

**48 kPa → 72 kPa**

along the beam.

---

# 🧮 Hand Calculation

## 1. Resultant Load

The trapezoidal load can be separated into:

- Uniform load = 12 kN/m
- Additional triangular load = 6 kN/m

### Uniform component

\[
W_1=12(3)=36\text{ kN}
\]

acting at:

\[
x_1=1.5\text{ m}
\]

from the fixed end.

### Triangular component

\[
W_2=\frac{1}{2}(6)(3)=9\text{ kN}
\]

Since the triangular component is maximum at the fixed end, its resultant acts 1 m from the wall.

Therefore:

\[
V_B=36+9=45\text{ kN}
\]

and the bending moment at the fixed end is:

\[
M_B=(36)(1.5)+(9)(1)
\]

\[
\boxed{M_B=63\text{ kN}\cdot\text{m}}
\]

---

## 2. Section Moment of Inertia

Using the parallel-axis theorem for the two flanges and the web:

\[
I_{zz}=3.0133\times10^{-4}\text{ m}^4
\]

The distance from the neutral axis to the extreme fibre is:

\[
y=170\text{ mm}=0.17\text{ m}
\]

---

## 3. Maximum Bending Stress

Using:

\[
\sigma=\frac{My}{I}
\]

\[
\sigma=
\frac{(63\times10^3)(0.17)}
{3.0133\times10^{-4}}
\]

\[
\boxed{\sigma_{\max}\approx35.5\text{ MPa}}
\]

---

## 4. Analytical Safety Factor

For a uniaxial stress state at the extreme fibre:

\[
\sigma_{vm}=\sigma
\]

Therefore:

\[
n=\frac{\sigma_{yt}}{\sigma_{vm}}
\]

\[
n=\frac{250}{35.5}
\]

\[
\boxed{n\approx7.03}
\]

---

# 💻 ANSYS Mechanical Simulation

The model was analysed using:

**ANSYS Workbench → Static Structural → Mechanical**

### Boundary Condition

The end face at **B** is completely fixed.

### Loading

A linearly varying pressure is applied to the top surface:

| Location | Pressure |
|---|---:|
| Free end A | 48 kPa |
| Fixed end B | 72 kPa |

### Material

Structural Steel with:

\[
\sigma_{yt}=250\text{ MPa}
\]

---

# 📊 Simulation Results

The following results were obtained from ANSYS Mechanical.

## Equivalent (von Mises) Stress

<img src="./Imgs/Equivalent_Stress.jpg" alt="ANSYS Equivalent von Mises Stress" width="850">

**Figure 1 — Equivalent von Mises stress distribution**

The maximum von Mises stress occurs near the fixed end of the cantilever, where the bending moment is maximum.

ANSYS result:

\[
\boxed{\sigma_{vm,\ ANSYS}\approx40.58\text{ MPa}}
\]

---

## Total Deformation

<img src="./Imgs/Total_Deformation.jpg" alt="ANSYS Total Deformation" width="850">

**Figure 2 — Total deformation of the cantilever beam**

The deformation increases toward the free end, as expected for a cantilever beam.

Maximum deformation obtained from ANSYS:

\[
\boxed{\delta_{\max}\approx2.42\text{ mm}}
\]

---

## Maximum Shear Stress

<table>
<tr>
<td align="center">
<img src="./Imgs/Max_Shear_Stress.jpg" width="450">
<br>
<b>Figure 3 — Maximum Shear Stress</b>
</td>
<td align="center">
<img src="./Imgs/Max_Principal_Stress.jpg" width="450">
<br>
<b>Figure 4 — Maximum Principal Stress</b>
</td>
</tr>
</table>

The maximum shear stress obtained from ANSYS is approximately:

\[
\boxed{\tau_{\max}\approx21.14\text{ MPa}}
\]

---

## Safety Factor

<img src="./Imgs/Safety_Factor.jpg" alt="ANSYS Safety Factor" width="850">

**Figure 5 — Safety factor distribution**

The minimum safety factor reported by ANSYS is approximately:

\[
\boxed{n_{ANSYS}\approx6.16}
\]

---

# 📈 Hand Calculation vs ANSYS

| Quantity | Hand Calculation | ANSYS |
|---|---:|---:|
| Maximum bending/von Mises stress | 35.5 MPa | 40.58 MPa |
| Maximum shear stress | — | 21.14 MPa |
| Safety factor | 7.03 | 6.16 |
| Maximum deformation | — | 2.42 mm |
| Critical region | Fixed end | Fixed end |

### Stress Comparison

The analytical solution predicts approximately:

\[
\sigma_{hand}=35.5\text{ MPa}
\]

while ANSYS gives:

\[
\sigma_{ANSYS}=40.58\text{ MPa}
\]

The difference is approximately:

\[
\frac{40.58-35.5}{35.5}\times100
\approx14.3\%
\]

The higher local FEA stress can be associated with the stress concentration and boundary-condition effects near the fixed support.

Therefore, the stress should be compared at an appropriate distance from the support when validating the elementary beam-theory solution.

---

# 🔍 Why Do Hand and FEA Results Differ?

The analytical solution assumes ideal beam theory and primarily evaluates the bending stress:

\[
\sigma=\frac{My}{I}
\]

ANSYS, however, solves the three-dimensional solid model and considers the local stress state around the fixed boundary.

Important sources of difference include:

1. **Fixed-support boundary effects**
2. **Local stress concentration near the wall**
3. **3D stress distribution**
4. **Finite element discretization**
5. **Load application over a finite surface**
6. **Difference between ideal beam theory and the actual solid geometry**

For this reason, the maximum nodal stress immediately adjacent to a perfectly fixed support may be higher than the elementary beam-theory value.

---

# 🖥️ Simulation Setup

The ANSYS model consists of:

```text
Geometry
   ↓
Material Assignment
   ↓
Fixed Support at B
   ↓
Linearly Varying Pressure
   ↓
Mesh
   ↓
Solve
   ↓
Post Processing
