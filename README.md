# Steel02M: An Improved Giuffr`e-Menegotto-Pinto Model


A well-known limitation of the original `Steel02` material model in OpenSees is that after a small partial unloading, the stress–strain curve does not correctly retrace the original loading path during reloading. This behavior often leads to overshooting of stress.

The `Steel02M` uniaxial material model resolves this issue by introducing:
- **Improved energy-based path-following rules** to ensure the reloading curve returns to the correct envelope.
- **Optional tolerance control (`ΔŪₜₒₗ `)** for tuning reversal sensitivity.
- Control rate of transition in positive and negative loading directions (**b⁺** and **b⁻**).

### Input Syntax
```tcl
uniaxialMaterial Steel02M $matTag  $E₀ $σᵧ₀⁺ $σᵧ₀⁻ $α $R₀ $cᵣ₁ $cᵣ₂ $a⁺ $a⁻ <$fᵧₛ⁺ $fᵧₛ⁻ $b1 $b2 Ed_tol>
```
| Parameter | Type  | Description                                                                |
| --------- | ----- | -------------------------------------------------------------------------- |
| $matTag | int   | Unique tag for this material instance                                      |
| $E₀   | float | Elastic Young’s modulus                                                            |
| $σᵧ₀⁺  | float | Initial yield strength in positive loading direction                               |
| $σᵧ₀⁻   | float |  Initial yield strength in negative loading direction                            |
| $α   | float | Strain hardening ratio                                                    |
| $R₀  | float |  Initial value of R that controls smoothness of transition                                                   |
| $cᵣ₁ | float | Controls the rate of change of R |
| $cᵣ₂   | float | Controls the rate of change of R|
| $a⁺   | float | Controls cyclic isotropic hardening in positive loading direction (default 0)     |
| $a⁻ | float | Controls cyclic isotropic hardening in negative loading direction (default 0)  |
| $fᵧₛ⁺   | float |  Saturation to initial yield strength ratios in positive direction (default 1.5) |
| $fᵧₛ⁻   | float| Saturation to initial yield strength ratio in negative direction  (default 1.5)  |
| $b⁺  | float| Controls rate of cyclic isotropic hardening in positive direction (default 0.8)  |
| $b⁻   | float| Controls rate of cyclic isotropic hardening in negative direction (default 0.8)  |
| $ΔŪₜₒₗ     |float | Normalized energy tolerance for identifying partial unloading  (default 0.02)    |

### Example TCL Input

```tcl
# Define Steel02M uniaxial material
# Arguments:
# matTag  E₀ σᵧ₀⁺ σᵧ₀⁻ α R₀ $cᵣ₁ $cᵣ₂ <$fᵧₛ⁺ $fᵧₛ⁻ $a⁺ $a⁻ $b1 $b2 Ed_tol>
# matTag E FyPos FyNeg alpha R0 cR1 cR2 <fysfyPos fysfyNeg a1 a2 b1 b2 Ed_tol>


set matTag 1
set E 210000.0 # E: Elastic Young's modulus
set FyPos 355.0 # FyPos: Positive yield stress
set FyNeg -355.0 # FyNeg: Negative yield stress
set alpha 0.01 # alpha: Post yield stiffness ratio
set R0 20.0 # R0: Initial transition curvature 
set cR1 0.925 # cR1:  Parameter controlling transition curvature 
set cR2 0.15 # cR2:  Parameter controlling transition curvature 
set fysfyPos 1.1 # fysfy_pos: Saturated/Initial yield strength ratio in positive loading direction
set fysfyNeg 1.1 # fysfy_neg: Saturated/Initial yield strength ratio in negative loading direction
set a3 0.0
set a4 1.0
set a1 0.0
set a2 1.0
set a_pos [expr $a3 / pow($a4,0.8)] # Control rate of transition in positive loading direction
set a_neg [expr $a1 / pow($a2,0.8)] # Control rate of transition in negative loading direction
set b1 0.8 # (optional) Exponent controlling curvature transition in the positive direction
set b2 0.8 # (optional) Exponent controlling curvature transition in the negative direction
set Ed_tol 0.02 # Ed_tol (optional): Energy dissipation tolerance

uniaxialMaterial Steel02M $matTag $E $FyPos $FyNeg $alpha $R0 $cR1 $cR2 $a_pos $a_neg $fysfyPos $fysfyNeg $b1 $b2 $Ed_tol
# Example usage in a truss element
set eleTag 1
set iNode 1
set jNode 2
set A 100
element truss $eleTag $iNode $jNode $A $matTag
```

### Notes
- If ΔŪₜₒₗ  is not specified in the input, a default value of **0.02** is used.
- Positive and negative curvature transition parameters are explicitly mentioned in positive and negative directions.



