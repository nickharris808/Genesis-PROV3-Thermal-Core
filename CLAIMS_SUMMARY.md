# Genesis PROV 3: Patent Claims Summary

**Total Claims:** ~200 across multiple provisional filings (81 core + ~120 unique external)
**Priority Date:** 2026-01-30 (USPTO Provisional)
**Status:** Filed

---

## Overview

The Genesis Thermal Core patent portfolio protects the complete innovation stack: from the binary fluid composition itself, through the computational discovery methods used to identify it, to the hardware topology and manufacturing processes required to deploy it.

---

## Master Omnibus Patent (110 Claims)

The Master Omnibus consolidates claims from six component provisional filings into a single authoritative document.

### Claim Families

| Family | Coverage | Representative Claims |
|:---|:---|:---|
| Self-Rewetting Fluid Composition | Binary fluid with solutal Marangoni effect, delta-sigma >= 3 mN/m | Claims 1-15 |
| Passive Cooling Method | Self-pumping thermal management with zero mechanical pump | Claims 16-30 |
| Cold Plate System | Topology-optimized cold plate with NeuralValve geometry | Claims 31-45 |
| Binary Mixture Thermophysics | Computational engine for mixture property prediction | Claims 46-55 |
| Zero-Gravity Stability | Gravity-independent Marangoni cooling (Bond number < 0.1) | Claims 56-65 |
| Lattice Boltzmann Simulation | LBM-based coupled thermal-fluid simulation method | Claims 66-75 |
| Hypersonic TPS | Gyroid TPMS structures for thermal protection | Claims 76-85 |
| ML-Accelerated Design | Physics-informed ML surrogate for thermal prediction | Claims 86-95 |
| Differentiable Manifold | Differentiable manifold optimization for cold plate design | Claims 96-105 |
| Manufacturing Validation | Framework for computational-to-physical validation | Claims 106-110 |

### Key Independent Claims

**Claim 1 (Composition):** A binary cooling fluid composition comprising a fluorinated base fluid and a fluorinated amine additive, wherein the mixture exhibits a surface tension differential (delta-sigma) of at least 3 mN/m between the base fluid and the additive, and wherein preferential evaporation of the base fluid at a heat source creates a composition gradient that drives solutal Marangoni flow toward the heat source.

**Claim 36 (The Hook):** Covers ALL fluorinated ketone + fluorinated amine binary combinations exhibiting solutal Marangoni self-pumping behavior for electronics cooling.

**Claim 72 (Self-Pumping):** A thermal management method wherein a binary fluid achieves self-pumping velocity greater than 0.05 m/s without mechanical pumping, driven solely by surface tension gradients arising from composition changes induced by localized boiling.

---

## Supporting Provisional Filings (~240 Additional Claims)

### Provisional 1: Fluid System and Method

- **Claims:** ~124
- **Coverage:** Detailed fluid composition claims, method of use claims, system integration claims
- **Key innovation:** Specific fluid pairings (HFO-1336mzz-Z + TFA, HFE-7100 + fluorinated amines, Novec 649 + fluorinated alcohols) and their operating envelopes

### Provisional 2: Discovery Engine

- **Claims:** ~52
- **Coverage:** Computational method for discovering self-rewetting binary fluid mixtures
- **Key innovation:** Systematic screening of fluorinated chemical space using fragment-based surface tension prediction, molecular dynamics validation, and CFD confirmation

### Provisional 3: Physics-Informed ML

- **Claims:** ~19
- **Coverage:** Machine learning surrogates trained on physics simulation data
- **Key innovation:** Neural network models that predict surface tension, flow velocity, and thermal performance from molecular descriptors without running full simulations

### Provisional 4: Differentiable Manifold Design

- **Claims:** ~19
- **Coverage:** Automatic differentiation-based optimization of cold plate topology
- **Key innovation:** End-to-end differentiable physics pipeline from geometry to thermal performance, enabling gradient-based optimization of complex 3D flow structures

### AI Design Method

- **Claims:** ~51
- **Coverage:** AI-driven design methodology for thermal management systems
- **Key innovation:** Automated design loop integrating topology optimization, CFD validation, FEA stress analysis, and manufacturing constraint checking

### Manufacturing Validation

- **Claims:** ~38
- **Coverage:** Process validation framework for manufacturing Marangoni cooling devices
- **Key innovation:** Computational-to-physical validation pipeline with defined acceptance criteria at each manufacturing stage

---

## Design Space Coverage (Patent Moat)

The patent portfolio is structured to cover the entire viable design space for solutal Marangoni cooling:

### Covered Fluid Combinations

| Pump Additive | delta-sigma (mN/m) | Covered By |
|:---|:---|:---|
| TF-Ethylamine (champion) | 4.8 | Claim 7 |
| TFE (alcohol) | 8.1 | Claim 38 |
| TF-Propylamine | 6.3 | Claim 43 |
| 3,3,3-TF-Propanol | 10.4 | Claim 39 |
| HFIP | 3.1 | Claim 45 |
| Pentafluoropropanol | 4.5 | Claim 44 |
| Heptafluorobutanol | 3.5 | Claim 44 |
| Perfluorodecalin | 4.6 | Claim 46 |
| TF-Butylamine | 7.5 | Claim 43 |
| Pentafluoroethanol | 5.2 | Claim 44 |
| Hexafluoroisopropanol | 3.4 | Claim 45 |
| Octafluoropentanol | 2.8 | Claim 44 |

### Covered Base Fluids

| Base Fluid | Boiling Point (degrees C) | GWP | Covered By |
|:---|:---|:---|:---|
| HFO-1336mzz-Z (champion) | 33.4 | 2 | Claim 1 |
| HFE-7100 (Novec 7100) | 61.0 | 297 | Claim 36 |
| HFE-7200 | 76.0 | 55 | Claim 36 |
| Novec 649 | 49.0 | 1 | Claim 36 |

### Non-Viable Alternatives (Not Covered, Fail for Physics Reasons)

| Category | Failure Mode |
|:---|:---|
| Alcohols (methanol, ethanol) | Wrong delta-sigma sign -- flow goes away from hotspot |
| Alkanes (hexane, heptane) | Boiling point mismatch, fractionation |
| Ketones (acetone, MEK) | Anti-surfactant behavior, unstable Marangoni |
| Silicone oils (PDMS) | Too viscous, chokes flow velocity |
| Perfluorocarbons (FC-72, FC-770) | Insufficient delta-sigma (< 3 mN/m) |

---

## Blocker Claims

**Claim 171 (Blocker):** ANY compound achieving delta-sigma >= 2 mN/m under operating conditions in a binary mixture used for electronics cooling via solutal Marangoni effect.

**Claim 180 (Universal):** ANY surface tension gradient mechanism applied to passive electronics cooling in a sealed cold plate without mechanical pumping.

These blocker claims are designed to prevent design-around attempts by competitors who might use different specific compounds but the same underlying physics.

---

## Defense and Space Claims

| Application | Relevant Claims | Key IP |
|:---|:---|:---|
| Zero-G Directed Energy Weapons | 71-72, 139-141 | Gravity-independent Marangoni cooling |
| Fusion Divertor Cooling | 136-138 | Topology-optimized lattice cooling |
| Hypersonic Thermal Protection | 146-147 | Gyroid stress reduction structures |
| Long-Duration Space Systems | 150-153 | 10+ year passive Marangoni operation |

---

*This summary is provided for informational purposes. Full patent texts are not included in this public repository. Contact the Genesis team for licensing inquiries.*
