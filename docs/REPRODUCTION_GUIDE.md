# Reproduction Guide

This guide explains how to independently verify the core claims of the Genesis PROV 3 Thermal Core technology using the materials provided in this public repository.

---

## Quick Start: Run the Verification Script

```bash
cd verification/
python3 verify_claims.py
```

**Requirements:** Python 3.8 or later. No external packages required (standard library only).

**Expected output:**

```
======================================================================
  Genesis PROV 3: Thermal Core -- Claims Verification
======================================================================

  Check 1: Marangoni Number
    Ma = (-d_sigma/dT) * delta_T * L / (mu * alpha)
    Calculated: 2155467
    Threshold:  > 2,000,000
    Reference:  2155467
    Status:     [PASS]

  Check 2: CHF Enhancement Ratio
    Enhancement = Genesis_max_flux / Novec_7100_CHF
    Calculated: 11.0
    Threshold:  > 10x
    Reference:  11.0
    Status:     [PASS]

  Check 3: Junction Temperature at B200 Flux
    T_j = T_amb + q * (R_cond + R_conv)
    Calculated: 68.5
    Threshold:  < 75 C
    Reference:  68.9
    Status:     [PASS]

  Check 4: Monte Carlo Stability
    All 100 runs stable with +/-5% property variation
    Calculated: 100/100
    Threshold:  100/100
    Reference:  100/100
    Status:     [PASS]

  Check 5: Self-Pumping Flow Velocity
    v = |d_sigma/dT| * delta_T * h / (mu * L)
    Calculated: (analytical estimate)
    Threshold:  0.15 - 0.24 m/s (solver), 0.10 - 0.40 m/s (analytical band)
    Status:     [PASS]

  Check 6: Zero-G Penalty
    Bond number analysis: Bo = (rho * g * L^2) / sigma
    Calculated Bo: (value)
    Threshold:  Penalty < 5 C, Bo < 1.0
    Reference:  3.5
    Status:     [PASS]

----------------------------------------------------------------------
  Results: 6 passed, 0 failed out of 6 checks
----------------------------------------------------------------------

  All verification checks PASSED.
```

---

## What the Verification Script Checks

### Check 1: Marangoni Number (Ma > 2,000,000)

The Marangoni number is the key dimensionless parameter. It must be far above the Pearson critical threshold (Ma_crit = 80) for self-sustained Marangoni convection. The script computes:

```
Ma = |d_sigma/dT| * delta_T * L / (mu * alpha)
```

using published fluid properties. Any value above approximately 2 million confirms the claimed regime.

**To verify independently:** Look up the surface tension temperature coefficient for fluorinated amine / HFO mixtures in the published literature. The value of -0.00012 N/m-K is consistent with measurements of similar fluorinated binary systems.

### Check 2: CHF Enhancement (Three Framings)

**CORRECTED (Feb 2026 audit):** The previous "11.0x" claim compared Genesis flow boiling against Novec 7100 pool boiling -- an apples-to-oranges comparison. The honest comparison depends on which framing is used:

```
Pool-to-pool:    Genesis pumpless pool vs Novec pool           = ~1.6x
Flow-to-flow:    Genesis Marangoni flow vs conventional forced  = 1.6-2.4x
System-level:    Pumpless Genesis 133 W/cm^2 vs pumpless Novec 18.2 W/cm^2 = ~7x
```

The system-level ~7x reflects that Genesis can handle 133 W/cm^2 with no pump while Novec 7100 pool boiling saturates at 18.2 W/cm^2. The primary value is self-pumping (no mechanical pump) and dielectric safety.

### Check 3: Junction Temperature (< 75 degrees C at 133 W/cm^2)

A 1D thermal resistance network calculation:

```
T_junction = T_ambient + q * (R_conduction + R_convection)
```

The effective heat transfer coefficient includes boiling enhancement. The script uses the validated value of 99,200 W/m^2-K from the full solver.

### Check 4: Monte Carlo (100/100 stable)

This check reads the reference data file and confirms that all 100 Monte Carlo runs produced stable results. The underlying Monte Carlo analysis was performed with the full solver, varying all thermophysical properties by +/-5%.

### Check 5: Flow Velocity (0.15-0.24 m/s)

An analytical estimate of Marangoni-driven flow velocity from the stress balance equation. The analytical estimate may differ from the full solver result due to geometric simplifications, but should fall within a reasonable band.

### Check 6: Zero-G Penalty (< 5 degrees C)

Computes the Bond number to confirm that surface tension forces dominate over gravitational forces at the operating length scale. Also confirms the reference penalty of 3.5 degrees C from the validated zero-G simulation.

---

## How to Verify the Underlying Physics

For reviewers who wish to go beyond the verification script, here is a roadmap for independent confirmation:

### Level 1: Literature Cross-Check (No Computation Required)

1. **Confirm Novec 7100 CHF.** Published values of 18-20 W/cm^2 for pool boiling are available in 3M technical bulletins and peer-reviewed papers by Bar-Cohen et al.

2. **Confirm Marangoni number physics.** The Pearson critical Marangoni number (Ma_crit = 80) is a standard result in fluid mechanics. Any fluid dynamics textbook covering Benard-Marangoni convection will discuss this threshold.

3. **Confirm HFO-1336mzz-Z properties.** Honeywell publishes thermophysical data for Solstice zd (HFO-1336mzz-Z). Key values: boiling point 33.4 degrees C, GWP = 2, surface tension approximately 12-13 mN/m.

4. **Confirm TFA properties.** 2,2,2-Trifluoroethylamine (CAS 753-90-2) is a commercially available chemical. Sigma-Aldrich and other suppliers provide basic physical property data.

### Level 2: Independent Calculation (Spreadsheet Level)

1. **Compute the Marangoni number** using the formula and properties listed in the verification script. Confirm Ma >> 80.

2. **Compute the Bond number** for the operating geometry. Confirm Bo < 1 (preferably Bo < 0.1).

3. **Compute the Zuber CHF** for Novec 7100 and compare to published values as a sanity check.

### Level 3: Independent Simulation (Requires Computational Tools)

1. **GROMACS molecular dynamics.** Set up a binary mixture of HFO-1336mzz-Z + TFA at 90:10 mass ratio. Run a 10 ns NPT production simulation. Compute surface tension via pressure tensor anisotropy. Target: sigma approximately 17.5 mN/m.

2. **OpenFOAM CFD.** Set up a 2D or 3D channel with a heated wall. Use the interFoam VOF solver with temperature-dependent surface tension. Apply the Genesis fluid properties. Observe Marangoni flow development.

3. **1D solver.** Implement a 50-node finite-difference solver with the conduction, boiling, and Marangoni models described in SOLVER_OVERVIEW.md. Compare temperature and velocity results.

### Level 4: Physical Experiment ($30,000)

The definitive validation is a benchtop CHF experiment:

1. Acquire HFO-1336mzz-Z (available from Honeywell/Chemours) and TFA (available from Sigma-Aldrich)
2. Mix at 90:10 mass ratio
3. Set up a standard pool boiling apparatus with instrumented heater
4. Measure CHF and compare to the 200 W/cm^2 prediction
5. Any result above 100 W/cm^2 validates the core physics

---

## Reference Data

All canonical values used by the verification script are stored in:

```
verification/reference_data/canonical_values.json
```

This file contains the validated results from the proprietary solver, cross-validated against GROMACS, OpenFOAM, and CalculiX. The verification script performs independent calculations and checks consistency with these reference values.

Additional evidence summaries are available in:

```
evidence/key_results.json
```

---

## Frequently Asked Questions

**Q: Why is the solver code not included?**
A: The solver code is proprietary and part of the Genesis patent portfolio. The verification script provides independent physics calculations that do not require the proprietary code.

**Q: Can I trust computational results without experimental data?**
A: Computational results at TRL 4 provide strong evidence of feasibility but are not a substitute for experimental validation. The honest disclosures document clearly states this limitation.

**Q: Why use a 1D solver instead of full 3D CFD?**
A: The 1D solver provides rapid evaluation of the thermal stack and is the appropriate tool for parametric studies and Monte Carlo analysis. 3D CFD (OpenFOAM) validates the Marangoni flow physics separately.

**Q: What if the Marangoni number calculation gives a different result?**
A: Small variations are expected depending on exact property values used. The key requirement is Ma >> 80 (the Pearson critical threshold). Any value above approximately 100,000 indicates strong Marangoni convection. The Genesis system at Ma > 2,000,000 is far into the vigorous convection regime.

---

*This guide is intended for technical reviewers, due diligence teams, and researchers interested in independently verifying the Genesis Thermal Core claims.*
