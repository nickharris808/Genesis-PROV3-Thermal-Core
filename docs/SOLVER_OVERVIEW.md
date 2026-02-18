# Solver Architecture Overview

This document describes the computational architecture used to generate and validate the Genesis PROV 3 Thermal Core results. The solver code itself is proprietary and not included in this public repository. This overview is provided so that reviewers can understand the methodology and, if desired, implement independent verification.

---

## Core Solver: 1D Finite-Difference Thermal Model

### Purpose

The core solver computes the steady-state temperature distribution through a cold plate assembly subjected to a uniform heat flux on one side (chip interface) and convective/boiling cooling on the other (fluid interface).

### Grid and Discretization

- **Domain:** 1D through the cold plate thickness
- **Nodes:** 50 (canonical configuration)
- **Method:** Central finite differences with iterative convergence
- **Boundary conditions:**
  - Hot side: constant heat flux (q = TDP / A_die)
  - Cold side: convective cooling with temperature-dependent heat transfer coefficient

### Physics Models

#### Conduction

Standard Fourier conduction through the copper substrate:

```
q_cond = -k_Cu * dT/dx
```

where k_Cu = 385 W/m-K for OFHC copper.

#### Boiling Heat Transfer

The nucleate boiling heat transfer coefficient is computed using a conservative correlation:

```
h_boiling = min(1500 * delta_T^2, 100000) [W/m^2-K]
```

where delta_T is the local wall superheat (T_wall - T_sat). The cap at 100,000 W/m^2-K prevents unbounded growth in the correlation at high superheat.

This is a simplified model. It does not explicitly resolve bubble nucleation, growth, departure, or coalescence. The correlation coefficients are from published pool boiling literature, not tuned to the specific Genesis fluid mixture.

#### Marangoni Flow

The self-pumping velocity driven by the solutal Marangoni effect is computed from a stress balance at the liquid-vapor interface:

```
tau_Marangoni = d_sigma/dT * dT/dx

v_flow = tau_Marangoni * L_interface / (mu * L_channel)
```

The solver starts with zero initial velocity (no priming) and iteratively converges the temperature field and flow velocity to a self-consistent solution.

#### No Artificial Floors or Priming

The current solver (strict mode, v10.0+) contains:
- Zero initial velocity (uf = 0)
- No artificial surface tension floor
- No fallback values for mixture properties
- Convergence check on temperature residual

Previous versions contained artificial priming flow and property fallbacks. These have been removed and documented in the audit trail.

### Convergence

The solver iterates until the maximum temperature change between iterations is less than 0.01 degrees C. Typical convergence is achieved in 50-200 iterations depending on heat flux.

---

## Supporting Simulation Tools

### GROMACS 2025.3 -- Molecular Dynamics

**Purpose:** First-principles validation of binary mixture surface tension.

- **System:** HFO-1336mzz-Z + TFA binary mixture at 90:10 mass ratio
- **Ensemble:** NPT (constant pressure, constant temperature)
- **Production run:** 10 ns, 10,000 frames saved
- **Output:** Surface tension = 17.5 mN/m
- **Method:** Kirkwood-Buff / pressure tensor anisotropy

The GROMACS result provides the fundamental thermophysical input (surface tension) that drives all downstream calculations.

### OpenFOAM v2406 -- Computational Fluid Dynamics

**Purpose:** 3D validation of Marangoni-driven flow field.

- **Solver:** interFoam (Volume of Fluid method for two-phase flow)
- **Domain:** 3D cold plate channel geometry
- **Transport properties:** sigma = 0.0178 N/m, d_sigma/dT = -0.00012 N/m-K
- **Output:** Velocity field, phase distribution, interface dynamics

The OpenFOAM simulations confirm that Marangoni flow develops in the expected direction and at the expected magnitude in a 3D geometry, validating the 1D solver's simplified treatment.

### CalculiX v2.22 -- Finite Element Analysis

**Purpose:** Structural validation of NeuralValve topology-optimized geometry.

- **Component:** NeuralValve flow control structure
- **Material:** SS316L stainless steel
- **Mesh:** 10,215 nodes
- **Loading:** Thermal stress from temperature gradients during operation
- **Output:** 8x stress reduction vs straight-channel baseline

The CalculiX results validate that the topology-optimized geometries are structurally sound under operating conditions.

---

## Marangoni Number Calculation

The dimensionless Marangoni number is computed as:

```
Ma = |d_sigma/dT| * delta_T * L / (mu * alpha)
```

where:
- d_sigma/dT = -0.00012 N/m-K (temperature coefficient of surface tension)
- delta_T = temperature difference across the fluid layer [K]
- L = characteristic length [m]
- mu = dynamic viscosity [Pa-s]
- alpha = k / (rho * Cp) = thermal diffusivity [m^2/s]

For the Genesis fluid at B200 operating conditions: Ma = 2,155,467, which is 26,943 times above the Pearson critical threshold (Ma_crit = 80). This confirms vigorous, self-sustained Marangoni convection.

---

## Monte Carlo Robustness Analysis

The Monte Carlo analysis assesses sensitivity to uncertainty in thermophysical properties:

- **Method:** 100 independent solver runs
- **Variation:** Each of 4 key properties (surface tension, viscosity, thermal conductivity, density) varied by +/-5% using uniform random sampling
- **Solver:** Full 1D FD solver (not a surrogate or toy model)
- **Result:** 100/100 stable (all runs below 85 degrees C threshold)
- **Statistics:** Mean = 66.1 degrees C, Std = 4.3 degrees C, P99 = 70.9 degrees C

---

## Zuber CHF Calculation

The classical Zuber correlation for pool boiling CHF provides a physics-based reference point:

```
q_CHF = 0.131 * h_fg * rho_v * [sigma * g * (rho_l - rho_v) / rho_v^2]^(1/4)
```

For Novec 7100, this yields approximately 18.2 W/cm^2, consistent with published experimental data. The Genesis system exceeds this by 11.0x through Marangoni-enhanced flow and boiling.

---

## What This Repository Does NOT Include

- The 1D FD solver source code (proprietary)
- The topology optimizer source code (proprietary)
- The binary mixture thermophysics engine (proprietary)
- GROMACS topology files for TFA
- OpenFOAM case files
- CalculiX input files
- ML surrogate model weights

These are available in the full Genesis data room for qualified reviewers under NDA.

---

*This solver overview is provided for transparency. The verification script (verify_claims.py) performs independent calculations that do not depend on the proprietary solver.*
