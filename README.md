# Cantilever I-Beam Under Trapezoidal Load: Safety Factor

**DME Assignment, Problem 14**: analytical hand calculation vs. **ANSYS Mechanical 2026 R1** (Static Structural).

A 3 m cantilever I-beam carries a linearly varying (trapezoidal) distributed load. The aim is to find the maximum stress, maximum shear stress, safety factor and tip deflection, and to compare the hand calculation with the FEA result.

---

## Problem Statement

A **3 m cantilever I-beam** of ductile structural steel is fixed at the wall at end **B** and free at end **A**. It carries a trapezoidal load of **12 kN/m at the free end A** rising to **18 kN/m at the fixed end B**.

| Parameter | Value |
|---|---:|
| Beam length, L | 3 m |
| Load at free end A | 12 kN/m |
| Load at fixed end B | 18 kN/m |
| Flanges | 250 × 20 mm |
| Web thickness | 20 mm |
| Overall depth | 340 mm |
| Material | Structural steel |
| Yield strength, σ_yt | 250 MPa |
| Young's modulus, E | 200 GPa |
| Analysis | Static Structural |
| Software | ANSYS Mechanical 2026 R1 |

---

## Repository Structure

```text
Cantilever_Beam_Simulation/
├── AnsysSimulationFiles/              # ANSYS Workbench project
├── HandWritten/                       # Handwritten calculation
├── SOLID_MODEL/                       # CAD model of the I-beam
├── Simulation_Video/                  # Screen recording of the simulation
├── Imgs/                              # Result images used in this README
├── cantilever_calc.py                 # Python check of the hand calculation
├── Problem14_Handwritten_vs_ANSYS.pptx
└── README.md
```

---

## Load Modelling in ANSYS

The trapezoidal load is applied as a **tabular pressure** on the 250 mm wide top face:

$$p = \frac{w}{b}, \qquad b = 0.25\ \text{m}$$

$$p_A = \frac{12\,000}{0.25} = 48\ \text{kPa}, \qquad p_B = \frac{18\,000}{0.25} = 72\ \text{kPa}$$

The pressure varies linearly from **48 kPa (free end A) to 72 kPa (fixed end B)**.

---

## Hand Calculation

Taken from the handwritten solution in [`HandWritten/`](./HandWritten): find the critical point, then apply the von Mises and Tresca failure theories.

### 1. Section properties

$$I_{zz} = \frac{1}{12}(20)(300)^3 + 2\left[\frac{1}{12}(250)(20)^3 + (250)(20)(160)^2\right]$$

$$I_{zz} = 3.0133\times10^{-4}\ \text{m}^4 = 3.0133\times10^{8}\ \text{mm}^4$$

### 2. Free body diagram and bending moment at B

The trapezoid splits into a uniform part (12 kN/m) and a triangular part (18 − 12 = 6 kN/m).

$$F_1 = 12 \times 3 = 36\ \text{kN} \quad \text{(arm 1.5 m)}$$

$$F_2 = \tfrac{1}{2}(6)(3) = 9\ \text{kN} \quad \text{(arm 2 m)}$$

$$M_B = 12\times3\times1.5 + \tfrac{1}{2}\times6\times3\times2 = \mathbf{72\times10^{6}\ N\cdot mm}$$

The bending moment is maximum at B because B is at the fixed end.

### 3. Maximum shear force

$$R_y = V_{\max} = 12\times3 + (0.5\times6\times3) = 45\times10^{3}\ \text{N}$$

### 4. Stress at point B (y = h/2 = 170 mm = 0.17 m)

$$\sigma_B = \frac{M_{\max}\,y}{I} = \frac{(72\times10^{6})(170)}{3.0133\times10^{8}} = \mathbf{40.62\ MPa}, \qquad \tau_B = 0$$

### 5. Stress at point C (neutral axis, y = 0)

$$\sigma_C = 0$$

$$Q = (250\times20)(150+10) + (150\times20)(75) = 1.025\times10^{6}\ \text{mm}^3$$

$$\tau_C = \frac{V_{\max}\,Q}{I\,t_w} = \frac{(45\times10^{3})(1.025\times10^{6})}{(3.0133\times10^{8})(20)} = \mathbf{7.65\ MPa}$$

Since the stress at B is greater than at C, **the critical point is B**.

### 6. Failure theories at the critical point B

Principal stresses: σ₁ = 40.62 MPa, σ₂ = σ₃ = 0, with σ_yt = 250 MPa.

**von Mises**

$$\sigma_{vm} = \sqrt{\sigma_1^2 + \sigma_2^2 - \sigma_1\sigma_2} = 40.62\ \text{MPa}$$

$$n = \frac{\sigma_{yt}}{\sigma_{vm}} = \frac{250}{40.62} = \mathbf{6.15}$$

**Tresca (maximum shear stress)**

$$\tau_{\max} = \frac{\sigma_1 - \sigma_2}{2} = \frac{40.62 - 0}{2} = \mathbf{20.31\ MPa}$$

$$n = \frac{\sigma_{yt}}{2\,\tau_{\max}} = \frac{250}{40.62} = \mathbf{6.15}$$

---

## Known Error in the Handwritten Solution

> **Note:** the handwritten solution above uses a **2 m** moment arm for the triangular load component. This is incorrect.

The triangular part of the load (6 kN/m) is **zero at the free end A and maximum at the fixed end B**. Its resultant (9 kN) therefore acts at **L/3 = 1 m** from the wall, not 2 m. A 2 m arm would correspond to a triangle that is maximum at the free end, which contradicts the problem figure.

Corrected calculation:

$$M_B = 36(1.5) + 9(1) = \mathbf{63\ kN\cdot m} \quad (\text{not } 72\ \text{kN·m})$$

| Quantity | As submitted (2 m arm) | Corrected (1 m arm) |
|---|---:|---:|
| Bending moment at B | 72 kN·m | 63 kN·m |
| Max bending / von Mises stress | 40.62 MPa | 35.54 MPa |
| Max shear stress (Tresca) | 20.31 MPa | 17.77 MPa |
| Safety factor | 6.15 | 7.03 |
| Shear stress at C | 7.65 MPa | 7.65 MPa (unchanged) |

The section properties, the shear force (45 kN) and the shear stress at C are not affected. The beam is safe in both cases.

The closer agreement between the submitted hand values and ANSYS (40.62 vs. 40.58 MPa) should not be read as validation of the 72 kN·m moment. With the corrected moment, ANSYS is about 14% higher than the hand value. This difference comes from local effects at the fixed support, which depend on the mesh, and from the beam's self-weight, which ANSYS includes by default (about 1.2 kN/m) and the hand calculation does not.

---

## ANSYS Simulation

**Workflow:** Geometry → Material assignment → Fixed support at B → Tabular pressure on top face → Mesh → Solve → Post-processing

- **Boundary condition:** end face at B fully fixed.
- **Load:** linearly varying pressure on the top face, 48 kPa at A to 72 kPa at B.
- **Material:** Structural Steel, σ_yt = 250 MPa.

---

## Simulation Results

### Equivalent (von Mises) stress

<img src="./Imgs/Equivalent_Stress.jpg" alt="ANSYS Equivalent von Mises Stress" width="850">

**Figure 1:** Equivalent von Mises stress. The maximum, **σ_vm ≈ 40.58 MPa**, occurs near the fixed end where the bending moment is greatest.

### Total deformation

<img src="./Imgs/Total_Deformation.jpg" alt="ANSYS Total Deformation" width="850">

**Figure 2:** Total deformation. The maximum, **δ_max ≈ 2.42 mm**, is at the free end, as expected for a cantilever.

### Maximum shear and principal stress

<table>
<tr>
<td align="center">
<img src="./Imgs/Max_Shear_Stress.jpg" width="450">
<br>
<b>Figure 3: Maximum shear stress</b>
</td>
<td align="center">
<img src="./Imgs/Max_Principal_Stress.jpg" width="450">
<br>
<b>Figure 4: Maximum principal stress</b>
</td>
</tr>
</table>

Maximum shear stress from ANSYS: **τ_max ≈ 21.14 MPa**.

### Safety factor

<img src="./Imgs/Safety_Factor.jpg" alt="ANSYS Safety Factor" width="850">

**Figure 5:** Safety factor distribution. Minimum safety factor from ANSYS: **n ≈ 6.16**.

---

## Hand Calculation vs. ANSYS

| Quantity | Hand Calculation | ANSYS | Difference |
|---|---:|---:|---:|
| Max bending / von Mises stress | 40.62 MPa | 40.58 MPa | -0.1% |
| Max shear stress (Tresca) | 20.31 MPa | 21.14 MPa | +4.1% |
| Safety factor | 6.15 | 6.16 | +0.2% |
| Max deformation | n/a | 2.42 mm | n/a |
| Critical region | Fixed end B | Fixed end B | same |

The hand calculation and ANSYS agree closely on stress and safety factor, and both identify the fixed end as the critical location.

---

## Possible Sources of Small Differences

1. **Fixed-support effects.** A perfectly rigid support in a 3D model creates a local stress concentration at the wall, and the peak nodal value depends on the mesh.
2. **3D vs. beam theory.** ANSYS solves the full solid, while Euler-Bernoulli theory assumes plane sections remain plane.
3. **Load application.** The load is applied as pressure over a finite surface rather than as an ideal line load.
4. **Discretisation.** Results depend on element type, size and mesh quality.

---

## Run the Python Check

The script reproduces the **corrected** hand calculation (1 m arm, M_B = 63 kN·m) and prints the comparison with ANSYS. It uses only the standard library.

```bash
python cantilever_calc.py
```

---

## Conclusion

- Hand calculation: σ_max = 40.62 MPa, τ_max = 20.31 MPa, n = 6.15 (von Mises and Tresca).
- ANSYS: σ_vm = 40.58 MPa, τ_max = 21.14 MPa, n = 6.16, δ_max = 2.42 mm.
- The two methods agree closely, and the fixed end B is the critical location.
- The beam is safe, with a safety factor of about 6 to 7.
- The handwritten moment uses a 2 m arm for the triangular load; the correct arm is 1 m, giving M_B = 63 kN·m, σ = 35.54 MPa and n = 7.03 (see *Known Error in the Handwritten Solution*).

---

## Tools

- ANSYS Mechanical 2026 R1 (Workbench, Static Structural)
- Python 3 (verification script)
