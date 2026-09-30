# Cantilever Beam Simulation

Safety factor of a cantilever I-beam under a trapezoidal load: hand calculation vs. ANSYS Mechanical (static structural). DME assignment, Problem 14.

## Problem

A 3 m cantilever I-beam of ductile material (σyt = 250 MPa) is fixed at the wall (B) and free at the other end (A).

| Item | Value |
|---|---|
| Length | 3 m |
| Load | Trapezoidal, 12 kN/m at free end A to 18 kN/m at wall B |
| Section | I-beam, flanges 250 × 20 mm, web 20 mm thick |
| Material | Structural Steel (σyt = 250 MPa) |
| Goal | Safety factor at the critical element (von Mises / Tresca) |

In ANSYS the load is applied as a tabular pressure on the top face (250 mm wide): 48 kPa at the free end to 72 kPa at the wall, which equals 12 to 18 kN/m.

## Results

| Quantity | Hand calculation | ANSYS |
|---|---|---|
| Max stress (von Mises) | 40.62 MPa | 40.58 MPa |
| Max shear stress | 20.31 MPa | 21.14 MPa |
| Safety factor | 6.15 | 6.16 |
| Max deflection | not computed | 2.42 mm |

Critical element in both: top fibre at the fixed end.

> **Note:** for a triangular load that is 0 at A and maximum at B, the 9 kN resultant acts 1 m from the wall, which gives M_B = 63 kN·m (the handwritten sheet uses a 2 m arm, 72 kN·m). Corrected hand values: σ = 35.5 MPa, n ≈ 7.03. The FEA value near a fixed support is typically higher because of local stress build-up, so refine the mesh or read stress a short distance from the wall to compare.

## Repository structure

```
Cantilever_Beam_Simulation/
├── AnsysSimulationFiles/
│   ├── DME_asn.wbpj              # ANSYS Workbench project (open this)
│   ├── DME_asn_files/            # Workbench project data (results, session files)
│   └── part0001.igs              # geometry used by the project
├── SOLID_MODEL/
│   ├── CantiliverBeam.prt        # Creo Parametric part file
│   └── CantiliverBeam.igs        # IGES export of the beam (mm)
├── HandWritten/
│   └── HandWritten_Solution.pdf  # hand calculation
├── Simulation_Video/
│   └── Ansys_Solution.mp4        # screen recording of the ANSYS run and results
├── Problem14_Handwritten_vs_ANSYS.pptx   # presentation comparing both solutions
└── README.md
```

## Requirements

| Purpose | Software |
|---|---|
| Run the simulation | ANSYS Workbench / Mechanical **2026 R1** (Student version works) |
| Edit the solid model (optional) | PTC Creo Parametric (or any CAD that imports IGES) |
| View the presentation | Microsoft PowerPoint or LibreOffice Impress |

The project was saved with ANSYS 2026 R1, so older ANSYS releases may not open it.

## How to run the simulation

1. Clone the repository:
   ```bash
   git clone https://github.com/batharva/Cantilever_Beam_Simulation.git
   cd Cantilever_Beam_Simulation
   ```
2. Open **ANSYS Workbench 2026 R1**, then **File → Open** and select `AnsysSimulationFiles/DME_asn.wbpj`.
   Keep the `DME_asn_files` folder next to the `.wbpj` file.
3. Double-click **Model / Setup** on the Static Structural system to open **Mechanical**.
4. Check the setup in the Outline tree:
   - **Geometry**: I-beam imported from `part0001.igs` (units: mm)
   - **Material**: Structural Steel
   - **Fixed Support**: end face at wall B
   - **Pressure**: top face, tabular data in Z (48000 Pa at Z = -3 m, 72000 Pa at Z = 0)
5. Click **Solve**.
6. Read the results under **Solution**:
   - Total Deformation
   - Equivalent (von Mises) Stress
   - Maximum Shear Stress
   - Maximum Principal Stress
   - Stress Tool → Safety Factor (minimum about 6.16)

If the geometry does not show up, right-click **Geometry → Import/Refresh** and point it to `AnsysSimulationFiles/part0001.igs` or `SOLID_MODEL/CantiliverBeam.igs`.


To skip running the solver, watch the full walkthrough:

[![ANSYS Simulation Video](https://img.youtube.com/vi/VIDEO_ID/hqdefault.jpg](https://youtu.be/-R6-BGfNlug)

## Hand calculation (summary)

1. Izz = 3.0133 × 10⁻⁴ m⁴ (web + two flanges, parallel-axis theorem)
2. V_max = 36 kN + 9 kN = 45 kN
3. σ = M·y / I, with y = 170 mm at the top fibre
4. Uniaxial state at the critical point, so von Mises and Tresca give the same safety factor: n = σyt / σ

Full working is in `HandWritten/HandWritten_Solution.pdf`.

## Author

Atharva, B.Tech Mechanical Engineering, IIT Tirupati  
GitHub: [@batharva](https://github.com/batharva)
