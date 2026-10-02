"""
Cantilever I-beam under trapezoidal load  --  DME Assignment, Problem 14
Analytical verification of the hand calculation + comparison with ANSYS.

Run:  python cantilever_calc.py
Needs: Python 3.8+ (standard library only)

Coordinate: x measured from the FIXED end B (x = 0) to the FREE end A (x = L).
Load intensity: w(x) = w_B + (w_A - w_B) * x / L   (18 -> 12 kN/m)
"""

# ----------------------------------------------------------------- inputs
L = 3.0                      # m
w_A, w_B = 12e3, 18e3        # N/m  (free end, fixed end)
bf, tf = 0.250, 0.020        # flange width, thickness (m)
tw = 0.020                   # web thickness (m)
H = 0.340                    # overall depth (m)
E = 200e9                    # Pa  (ANSYS structural steel)
rho, g = 7850.0, 9.81        # kg/m3, m/s2  (ANSYS structural steel)
sigma_y = 250e6              # Pa

# ANSYS results (from your screenshots)
ansys = {"vm_MPa": 40.58, "tau_max_MPa": 21.14, "n": 6.16, "defl_mm": 2.42}

# ------------------------------------------------------- section properties
hw = H - 2 * tf                                   # web clear height
I = (bf * H**3 - (bf - tw) * hw**3) / 12          # m^4  (box subtraction)
y = H / 2
A = 2 * bf * tf + hw * tw
# first moment of area about NA for the half section (for web shear)
Q = bf * tf * (H / 2 - tf / 2) + tw * (hw / 2) * (hw / 4)

# ------------------------------------------------------- load resultants
w_uni = w_A                      # uniform part
w_tri = w_B - w_A                # triangular part, max at the wall

W1 = w_uni * L                   # N, acts at L/2 from wall
W2 = 0.5 * w_tri * L             # N, acts at L/3 from wall
V = W1 + W2
M = W1 * L / 2 + W2 * L / 3

# self-weight (ANSYS applies Standard Earth Gravity by default!)
w_sw = rho * g * A
V_sw = w_sw * L
M_sw = w_sw * L**2 / 2

# ------------------------------------------------------------- stresses
sigma = M * y / I
tau_web_na = V * Q / (I * tw)            # transverse shear at NA
tau_max_bend = sigma / 2                 # max shear from uniaxial bending (Mohr)
n = sigma_y / sigma

sigma_sw = (M + M_sw) * y / I
n_sw = sigma_y / sigma_sw

# ------------------------------------------------------------ deflection
EI = E * I
d_uni = w_uni * L**4 / (8 * EI)
d_tri = 11 * w_tri * L**4 / (120 * EI)   # triangle, zero at tip, max at wall
d_sw = w_sw * L**4 / (8 * EI)
delta = d_uni + d_tri


def pct(a, b):
    return (a - b) / b * 100


# ---------------------------------------------------------------- output
print("=== SECTION ===")
print(f"I  = {I:.4e} m^4   (hand calc: 3.0133e-04)")
print(f"A  = {A*1e4:.1f} cm^2, Q(NA) = {Q:.4e} m^3")

print("\n=== LOADS (about fixed end B) ===")
print(f"W1 = {W1/1e3:.1f} kN at {L/2:.2f} m,  W2 = {W2/1e3:.1f} kN at {L/3:.2f} m")
print(f"V_B = {V/1e3:.1f} kN,  M_B = {M/1e3:.1f} kN.m")
print(f"Self-weight: {w_sw/1e3:.3f} kN/m -> +{M_sw/1e3:.2f} kN.m")

print("\n=== ANALYTICAL RESULTS ===")
print(f"sigma_max (applied load only)  = {sigma/1e6:.2f} MPa -> n = {n:.2f}")
print(f"sigma_max (with self-weight)   = {sigma_sw/1e6:.2f} MPa -> n = {n_sw:.2f}")
print(f"tau_max (bending state, s/2)   = {tau_max_bend/1e6:.2f} MPa")
print(f"tau at NA (web, VQ/Ib)         = {tau_web_na/1e6:.2f} MPa")
print(f"tip deflection (load only)     = {delta*1e3:.2f} mm")
print(f"   uniform {d_uni*1e3:.2f} + triangular {d_tri*1e3:.2f}; self-weight adds {d_sw*1e3:.2f}")

print("\n=== HAND vs ANSYS ===")
rows = [
    ("von Mises [MPa]", sigma / 1e6, ansys["vm_MPa"]),
    ("von Mises + self-wt [MPa]", sigma_sw / 1e6, ansys["vm_MPa"]),
    ("Max shear [MPa]", tau_max_bend / 1e6, ansys["tau_max_MPa"]),
    ("Safety factor", n, ansys["n"]),
    ("Deflection [mm]", delta * 1e3, ansys["defl_mm"]),
]
print(f"{'Quantity':28s}{'Hand':>9s}{'ANSYS':>9s}{'ANSYS vs hand':>16s}")
for name, h, a in rows:
    print(f"{name:28s}{h:9.2f}{a:9.2f}{pct(a, h):+15.1f}%")

# ------------------------------------------------------------ sanity checks
assert abs(I - 3.0133e-4) < 1e-7, "I differs from hand calc"
assert abs(M - 63e3) < 1, "M_B differs from hand calc"
assert abs(sigma / 1e6 - 35.5) < 0.1, "sigma differs from hand calc"
print("\nAll hand-calc checks passed.")
