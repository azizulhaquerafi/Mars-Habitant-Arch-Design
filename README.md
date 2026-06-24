# Mars-Habitant-Arch-Design
Code, Flow Chart and Equation
# Aerospace Architecture & Structural Simulation Report
### Advanced Martian Habitat (Axisymmetric Parabolic Fixed Arch System)

---

## 1. Load Calculation & Structural Behavior Analysis

The structural diagram depicts a highly efficient axisymmetric design optimized for containing a high-differential internal atmospheric pressure (1 atm) against the thin Martian environment.

* **Internal Volumetric Pressure Control:** The life-support atmosphere inside the structure exerts a constant outward force. This internal pressure acts perpendicularly (90 degrees) to any curved boundary surface, creating a continuous distributed load known as the **Resultant Force ($R$)**.
* **Lateral Force Neutralization:** Due to the double-curved geometric configuration, when this resultant load decomposes into lateral (horizontal) force components, the opposing forces traveling along the circular ring walls meet directly face-to-face. As a result, these distributed loads **perfectly neutralize each other**. This boundary equilibrium eliminates localized tensile stress concentrations at the critical wall joints.
* **Vertical Compressive Alignment:** The vector arrows on the diagram show the top vertical forces pointing downward and the lower vertical forces pointing upward. This configuration places the side walls under pure **compression**. Because high-strength aerospace concrete or composite structural shells perform exceptionally well under compression, this design ensures that the habitat remains securely anchored to the Martian ground without inflating or rupturing like a standard flexible balloon dome.

---

## 2. Unified Mathematical Equations

To ensure strict geometric continuity and optimize structural load distribution, a single fundamental parabolic template equation is used to profile both the vertical and lateral dimensions of the habitat shell:

$$\mathbf{y = h \cdot \left(1 - \frac{4x^2}{b^2}\right)}$$

By varying the inputs and spatial orientations, this uniform equation is mapped onto the 3D cylindrical coordinates $(R, \theta, Z)$ to generate both major structural components without any alignment gaps.

### A. Side Wall Layer (Lateral Inward Constriction Profile)
The side wall curves inward to form a compression-dominant arch. The equation uses the elevation $Z$ as an input relative to the wall's mid-height axis:

$$\text{wall\ inward\ profile}(Z) = b_w \cdot \left(1 - \frac{4 \cdot \left(Z - \frac{h_w}{2}\right)^2}{h_w^2}\right)$$
This offset is subtracted from the outer baseline boundary radius to pull the circular shell inward symmetrically:

$$R_{\text{wall}}(Z) = \frac{b_r}{2} - \text{wall\ inward\ profile}(Z)$$
### B. Roof Layer (Suspended Parabolic Profile)
The shallow roof operates under tension to contain internal pressure. It uses the horizontal radial distance $R$ from the absolute center axis to calculate the suspended elevation curve:

$$Z\_{\text{roof}}(R) = h\_w - h\_r \cdot \left(1 - \frac{4 \cdot R^2}{b\_r^2}\right)$$

* **At Center Axis ($R = 0$):** The roof settles at its lowest suspended elevation: $Z = h\_w - h\_r$.
* **At Perimeter Edge ($R = \frac{b\_r}{2}$):** The roof climbs to exactly meet the wall boundary: $Z = h\_w$.

### C. 3D Cartesian Coordinate Projection
To transform the 2D radial profiles into a fully enclosed 360-degree cylindrical mesh volume, the geometric data arrays are projected into Cartesian space using standard trigonometric relations:

$$X = R \cdot \cos(\theta), \quad Y = R \cdot \sin(\theta)$$

---

## 3. Code Architecture Workflow Diagram
```text
[Execution Initialization]
│
▼
┌────────────────────────────────────────────────────────┐
│ Global Variable Input Phase                            │
│ Set Design Metrics: b_r=34m, h_w=8m, h_r=1.8m, b_w=4.8m│
└────────────────────────────────────────────────────────┘
│
▼
┌────────────────────────────────────────────────────────┐
│ Discretization Mesh Grid Set                           │
│ Construct High-Density Nodes for Theta & Elevation Z   │
└────────────────────────────────────────────────────────┘
│
▼
┌────────────────────────────────────────────────────────┐
│ Geometry Mapping Engine                                │
├────────────────────────────────────────────────────────┤
│ 1. Pass Z offset to Equation (2) to calculate contraction│
│ 2. Deduct profile via Equation (3) to get Wall Radius  │
│ 3. Execute Equation (1) to acquire locked Roof Height  │
└────────────────────────────────────────────────────────┘
│
▼
┌────────────────────────────────────────────────────────┐
│ Coordinate Axis Transformation                         │
│ Convert 2D Structural Planes into Cartesian 3D Arrays │
│ Using Trigonometric Projections [Equation (4)]         │
└────────────────────────────────────────────────────────┘
│
▼
┌────────────────────────────────────────────────────────┐
│ Multi-Layer Render Processing                          │
├────────────────────────────────────────────────────────┤
│ Layer 1 ──> Faded Matte Martian Ground Plane (α=0.08)  │
│ Layer 2 ──> Aero-Grade Cyan Inward Parabolic Side Wall │
│ Layer 3 ──> High-Vis Green Shallow Suspended Roof Shell│
└────────────────────────────────────────────────────────┘
│
▼
┌────────────────────────────────────────────────────────┐
│ Graphic Pipeline Normalization                         │
│ Adjust Aspect Ratios, View Projection (20°/40° Elev)    │
│ Remove Visual Noise & Attenuate Console Grid Lines     │
└────────────────────────────────────────────────────────┘
│
▼
[Display 3D Projection]
```


## 4. Final Professional 3D Simulation Code

```python
# Enable interactive 3D rendering in Google Colab or Jupyter Notebooks
%matplotlib inline

import numpy as np
import matplotlib.pyplot as plt
from matplotlib import cm

# =========================================================================
# 1. STRUCTURAL DESIGN PARAMETERS (UNIFORM PARABOLIC SCALING)
# =========================================================================
b_r = 34.0   # Base width / Max outer diameter of the habitat (34 meters)
h_w = 8.0    # Reference height boundary for the side wall (8 meters)
h_r = 1.8    # Shallow roof arch depth parameter (1.8 meters)
b_w = 4.8    # Wall constriction factor (Inward boundary curve depth)

# =========================================================================
# 2. FINITE ELEMENT GRID ARRAYS
# =========================================================================
theta_vals = np.linspace(0, 2 * np.pi, 250)  # Complete 360-degree axisymmetry
z_vals_wall = np.linspace(0, h_w, 150)        # Elevation nodes for the side wall
r_vals_roof = np.linspace(0, b_r / 2, 150)    # Radial distance nodes for the roof

# =========================================================================
# 3. GEOMETRY ENGINE (APPLYING REQUESTED PARABOLIC EQUATION)
# =========================================================================

# --- A. SIDE WALL LAYER (EQUATION APPLIED TO HEIGHT PROFILE) ---
THETA_w, Z_w = np.meshgrid(theta_vals, z_vals_wall)

# Using your exact formula format: h * (1 - (4 * (x^2) / (b^2)))
mid_height_offset = Z_w - (h_w / 2)
wall_inward_profile = b_w * (1 - (4 * (mid_height_offset**2) / (h_w**2)))

# Subtracting profile from outer radius pulls the wall inward symmetrically
R_wall = (b_r / 2) - wall_inward_profile

X_wall = R_wall * np.cos(THETA_w)
Y_wall = R_wall * np.sin(THETA_w)

# --- B. ROOF LAYER (EQUATION APPLIED TO RADIAL PROFILE) ---
R_r, THETA_r = np.meshgrid(r_vals_roof, theta_vals)

# Using your exact formula format: h * (1 - (4 * (x^2) / (b^2)))
# Subtracting from h_w keeps the outer edges perfectly locked to the wall boundary
Z_roof = h_w - h_r * (1 - (4 * (R_r**2) / (b_r**2)))

X_roof = R_r * np.cos(THETA_r)
Y_roof = R_r * np.sin(THETA_r)

# =========================================================================
# 4. RENDER LAYERS & MESH GENERATION
# =========================================================================
fig = plt.figure(figsize=(15, 10), facecolor='#0b0b0e')  # Tactical console backdrop
ax = fig.add_subplot(111, projection='3d')
ax.set_facecolor('#0b0b0e')

# Layer I: Faded Martian Base Layer (alpha=0.08 for minimal background noise)
r_mars = np.linspace(0, b_r * 1.3, 100)
THETA_m, R_m = np.meshgrid(theta_vals, r_mars)
X_mars = R_m * np.cos(THETA_m)
Y_mars = R_m * np.sin(THETA_m)
Z_mars = np.zeros_like(X_mars)
ax.plot_surface(X_mars, Y_mars, Z_mars, color='#b25329', alpha=0.08, edgecolor='none', rstride=4, cstride=4)

# Layer II: Structural Blue Inward Wall Shell
ax.plot_surface(X_wall, Y_wall, Z_w, color='#0088cc', alpha=0.85, edgecolor='none', rstride=2, cstride=2)

# Layer III: Connected Green Shallow Hanging Roof Shell
ax.plot_surface(X_roof, Y_roof, Z_roof, color='#21a357', alpha=0.9, edgecolor='none', rstride=2, cstride=2)

# =========================================================================
# 5. SCIENTIFIC VISUAL TUNING
# =========================================================================
ax.tick_params(colors='#8e8e93', labelsize=10)
ax.set_xlabel('SYSTEM X-AXIS (METERS)', color='#d1d1d6', fontsize=10, fontweight='bold', labelpad=12)
ax.set_ylabel('SYSTEM Y-AXIS (METERS)', color='#d1d1d6', fontsize=10, fontweight='bold', labelpad=12)
ax.set_zlabel('ELEVATION Z-AXIS (METERS)', color='#d1d1d6', fontsize=10, fontweight='bold', labelpad=12)

plt.title('UNIFORM MATHEMATICAL PROFILING: INTEGRATED MARTIAN HABITAT\n'
          '[Structural Shell Generated via Standard Formula: h * (1 - (4x² / b²))]', 
          color='#ffffff', fontsize=12, fontweight='bold', pad=30, loc='center')

# Configuration parameters for visual layout normalization
ax.view_init(elev=20, azim=40)
ax.set_zlim(0, h_w + 4)
ax.set_box_aspect([1, 1, 0.45]) 

# Clean, low-density grid lines
ax.xaxis._axinfo["grid"]['color'] = (0.2, 0.2, 0.2, 0.1)
ax.yaxis._axinfo["grid"]['color'] = (0.2, 0.2, 0.2, 0.1)
ax.zaxis._axinfo["grid"]['color'] = (0.2, 0.2, 0.2, 0.1)

plt.show()
