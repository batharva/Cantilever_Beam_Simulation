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

The full hand calculation is in the [`HandWritten/`](./HandWritten) folder.

| Quantity | Hand (load only) | Hand (+ self-weight) | ANSYS |
|---|---:|---:|---:|
| Max bending / von Mises stress | 35.5 MPa | 38.7 MPa | 40.58 MPa |
| Max shear stress (σ/2) | 17.8 MPa | 19.3 MPa | 21.14 MPa |
| Safety factor | 7.03 | 6.46 | 6.16 |
| Tip deflection | 2.76 mm | 2.97 mm | 2.42 mm |
| Critical region | Fixed end | Fixed end | Fixed end |

**Stress:** ANSYS is 14.3% above the load-only hand value, but only about 5% above the hand value once self-weight is included. Most of the gap is therefore self-weight.

**Safety factor:** both methods give n well above 1, so the beam is safe with a large margin (about 6 to 7).

---

## Why Do the Results Differ?

1. **Self-weight.** ANSYS includes gravity by default. This adds about 5.5 kN·m at the root and accounts for most of the stress difference.
2. **Fixed-support singularity.** A perfectly rigid support in a 3D solid model creates a stress singularity at the wall. The peak nodal stress there depends on mesh size and keeps rising as the mesh is refined. By Saint-Venant's principle, stress should be read about one beam depth (about 340 mm) away from the wall to compare fairly with beam theory.
3. **3D vs. beam theory.** The ANSYS model solves the full solid, including local effects, flange behaviour and the Poisson effect. Euler-Bernoulli theory assumes plane sections remain plane.
4. **Deflection difference.** The ANSYS tip deflection (2.42 mm) is *lower* than beam theory (2.76 mm). The 3D fixed face restrains warping and lateral contraction at the root, which makes the real solid stiffer than an ideal cantilever.
5. **Load application.** The load is applied as pressure over a finite surface rather than as an ideal line load.
6. **Discretisation.** Results depend on element type, size and mesh quality.

---

---

## Conclusion

- Hand calculation: σ_max ≈ 35.5 MPa, n ≈ 7.03.
- ANSYS: σ_vm ≈ 40.58 MPa, n ≈ 6.16, δ_max ≈ 2.42 mm.
- The two methods agree on the critical location (fixed end) and on the conclusion that the beam is safe with a large margin.
- The remaining stress difference is explained mainly by self-weight, plus the local effects at the fixed support.

---

## Tools

- ANSYS Mechanical 2026 R1 (Workbench, Static Structural)
- Python 3 (verification script)
