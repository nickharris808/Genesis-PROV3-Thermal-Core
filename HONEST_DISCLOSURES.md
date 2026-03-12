# Honest Disclosures

This document provides a transparent accounting of the limitations, assumptions, and current maturity level of the Genesis PROV 3 Thermal Core technology. We believe honest disclosure strengthens credibility and helps stakeholders make informed decisions.

---

## Technology Readiness Level

**Current TRL: 4 (Component validation in laboratory environment -- computational)**

All results presented in this repository are from computational simulations. No physical prototypes have been built. No experimental measurements have been performed on the Genesis binary fluid system.

---

## What Has Been Validated Computationally

| Claim | Validation Method | Confidence |
|:---|:---|:---|
| Surface tension of binary mixture (17.5 mN/m) | GROMACS 2025.3 molecular dynamics (10 ns, 10,000 frames) | High |
| Surface tension temperature coefficient (-0.00012 N/m-K) | OpenFOAM v2406 interFoam VOF transport properties | High |
| Junction temperature at 133 W/cm-squared (68.9 degrees C) | 1D finite-difference solver (50 nodes, converged) | Medium-High |
| Self-pumping velocity (0.15-0.24 m/s) | Marangoni stress balance calculation | Medium |
| CHF enhancement (1.6-2.4x flow-to-flow; system-level ~7x pumpless-to-pumpless) | Corrected comparison: flow-to-flow CHF ratio is 1.6-2.4x; system-level ~7x compares pumpless Genesis to pumpless Novec 7100 pool boiling | Medium |
| Thermal stress reduction (8x via NeuralValve) | CalculiX v2.22 FEA (SS316L, 10,215 nodes) | High |
| Monte Carlo robustness (100/100 stable) | 100 runs with +/-5% property variation | High |
| Zero-G penalty (3.5 degrees C) | Bond number analysis + modified solver | Medium |

---

## What Has NOT Been Validated

### 1. No Physical Prototype

No Genesis cold plate has been fabricated. No experimental CHF measurement has been performed. The corrected CHF enhancement is 1.6-2.4x flow-to-flow; the system-level ~7x figure compares pumpless Genesis to pumpless Novec 7100 pool boiling. The previous 11.0x claim was an apples-to-oranges comparison (forced-convection Genesis vs. pool-boiling Novec 7100). A physical CHF experiment (estimated cost: $30,000) is the critical de-risking step.

### 2. Simplified Physics Model

The core solver is a **1D finite-difference model**, not a full 3D CFD simulation of two-phase flow. Key simplifications include:

- **No explicit bubble dynamics.** The boiling model uses a heat transfer coefficient correlation (1500 * delta_T^2, capped at 100 kW/m^2-K) from published literature. Bubble nucleation, growth, departure, and coalescence are not modeled.
- **1D geometry.** The solver computes temperature through the cold plate thickness only. Lateral flow patterns, recirculation zones, and 3D effects are not captured.
- **Steady-state only.** Transient startup, load cycling, and dynamic response are not modeled.
- **No phase-change front tracking.** The location and shape of the boiling front are not explicitly resolved.

The OpenFOAM interFoam VOF simulations provide 3D validation of the Marangoni flow field but use simplified boundary conditions. The GROMACS molecular dynamics provides first-principles validation of surface tension but at a much smaller length scale than the device.

### 3. Fluid Stability and Compatibility

- **No long-term degradation data.** The chemical stability of TFA (2,2,2-Trifluoroethylamine) at operating temperatures over thousands of hours is unverified.
- **No seal compatibility testing.** Fluorinated fluids can attack certain elastomers and polymers. Material compatibility with seals, gaskets, and cold plate materials has not been tested.
- **No corrosion data.** Copper cold plate compatibility with the binary mixture is assumed based on general fluorinated fluid behavior but has not been experimentally confirmed.
- **Acid risk.** HFO-1336mzz-Z can decompose above approximately 250 degrees C. Operating temperature is 68.9 degrees C (margin: 181 degrees C). However, local hot spots during transient events or dry-out could approach decomposition temperatures.

### 4. GROMACS Topology Gap

The molecular dynamics topology files for TFA (2,2,2-Trifluoroethylamine) may need reconstruction for fully independent MD verification. The existing GROMACS production run (10 ns, 10,000 frames) was performed with available force field parameters, but independent groups may need to regenerate or validate these parameters.

### 5. Manufacturing Uncertainty

- **Porous transport layer (PTL) fabrication.** The specified gradient porosity (5-200 um) in sintered copper has not been prototyped. Achieving reproducible pore structure at this scale is a known manufacturing challenge.
- **NeuralValve geometry.** The topology-optimized geometries have been validated structurally (CalculiX FEA) but not manufactured or flow-tested.
- **Hermetic sealing.** The indium gasket seal specified for the cold plate assembly has not been prototyped.

### 6. Regulatory Uncertainty

- **PFAS classification.** TFA contains C-F bonds. Under the current EPA definition, it is not classified as PFAS. Under the proposed EU universal PFAS restriction (ECHA, 2023), it may be captured by a broader definition. Regulatory status under evolving PFAS definitions is uncertain.
- **No UL listing.** The cold plate assembly has not been submitted for UL recognition. Standard certification pathways exist for sealed liquid cooling systems, but the process has not been initiated.

---

## Modeling Assumptions

The following assumptions underlie the computational results:

1. **Ideal binary mixing.** Thermophysical properties of the mixture are computed using standard mixing rules (Butler equation for surface tension, Arrhenius for viscosity). Non-ideal mixing effects beyond these correlations are not modeled.
2. **Constant composition.** The simulation assumes uniform 90:10 HFO:TFA composition throughout the system. In practice, local composition will vary due to preferential evaporation -- this is the mechanism that drives the Marangoni effect, but the spatial variation is not explicitly resolved in the 1D model.
3. **No fluid loss.** The system is assumed to be hermetically sealed with zero fluid leakage over its operational lifetime.
4. **Ambient at 25 degrees C.** All simulations assume a 25 degrees C ambient temperature. Datacenter environments may vary from 15 to 35 degrees C.
5. **No fouling or contamination.** Deposit buildup on heat transfer surfaces over time is not modeled.

---

## What Would Change Our Confidence

| Experiment | Cost | Impact |
|:---|:---|:---|
| Benchtop flow visualization | $2,000 | Visual proof of Marangoni flow direction |
| Pool boiling CHF measurement | $30,000 | Confirms or refutes 1.6-2.4x flow-to-flow / ~7x pumpless-to-pumpless enhancement claim |
| 1000-hour fluid stability test | $30,000 | Validates long-term chemical compatibility |
| Cold plate prototype and test | $15,000 | Validates manufacturing feasibility |

The $30,000 CHF experiment is the single most important de-risking step. The corrected claim is 1.6-2.4x flow-to-flow CHF enhancement (system-level ~7x pumpless-to-pumpless). The previous 11.0x figure compared forced-convection Genesis to pool-boiling Novec 7100 and has been retracted.

---

## Previous Version Issues (Corrected)

Earlier versions of the Genesis Thermal Core codebase contained errors that have been identified, documented, and corrected:

- **Artificial priming flow (v7.0):** An initial velocity of 2.0 m/s was injected into the solver, falsely implying self-starting. Removed -- all flow is now self-generated from zero initial velocity.
- **Inflated CHF (v8.0):** CHF enhancement was computed by multiplying individual enhancement factors rather than measuring the actual maximum stable flux. Corrected to measure the flux at which T_max exceeds 85 degrees C.
- **Mock Monte Carlo (v6.0):** The Monte Carlo robustness analysis used a toy model instead of the real solver. Replaced with the actual 1D FD solver.
- **Hallucinated metrics (pre-v10.0):** Patent abstracts contained performance numbers (36.8 degrees C at 1000 W/cm-squared) that were never produced by any solver. Corrected to actual solver output (68.9 degrees C at 133 W/cm-squared).

- **11.0x CHF retracted (Feb 2026 audit):** The 11.0x CHF enhancement was an apples-to-oranges comparison (forced-convection Genesis vs. pool-boiling Novec 7100). Corrected to 1.6-2.4x flow-to-flow (system-level ~7x pumpless-to-pumpless).

All current metrics are produced by the canonical 50-node solver with zero artificial floors, zero priming, and strict convergence checks.

---

*Transparency is a feature, not a weakness. These disclosures demonstrate that we understand the boundaries of our computational work and have a clear path to physical validation.*
