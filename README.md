# Steel02M: An Improved Giuffr`e-Menegotto-Pinto Model


A well-known limitation of the original `Steel02` material model in OpenSees is that after a small partial unloading, the stress–strain curve does not correctly retrace the original loading path during reloading. This behavior often leads to overshooting of stress.

The `Steel02M` uniaxial material model resolves this issue by introducing:
- **Improved energy-based path-following rules** to ensure the reloading curve returns to the correct envelope.
- **Optional tolerance control (`Ed_tol`)** for tuning reversal sensitivity.
- Control rate of transition in positive and negative loading directions(**a_pos** and **a_neg**).

### Input Syntax
```tcl
uniaxialMaterial Steel02M $matTag $E $FyPos $FyNeg $alpha $R0 $cR1 $cR2 $fysfyPos $fysfyNeg $a_pos $a_neg <$b1 $b2> <Ed_tol>
```
| Parameter | Type  | Description                                                                |
| --------- | ----- | -------------------------------------------------------------------------- |
| `$matTag` | int   | Unique tag for this material instance                                      |
| `$E`      | float | Elastic Young’s modulus                                                            |
| `$FyPos`     | float | Initial yield strength in positive loading direction                               |
| `$FyNeg`   | float |  Initial yield strength in negative loading direction                           |
| `$alpha`  | float | Post yield stiffness ratio                                                   |
| `$R0`  | float |  Initial transition curvature                                                   |
| `$cR1` | float | Parameter controlling transition curvature |
| `$cR2`   | float | Parameter controlling transition curvature |
| `$fysfyPos`   | float | Saturation-to-initial yield strength ratio in positive loading direction        |
| `$fysfyNeg` | float | Saturation-to-initial yield strength ratio in negative loading direction |
| `$a_pos`   | float | Control rate of transition in positive loading direction, computed as a_pos $=a_3/a_4^{0.8}$ |
| `$a_neg`   | float| Control rate of transition in negative loading direction, computed as a_neg $=a_1/a_2^{0.8}$  |
| `$b1`  | float| *(Optional)* Exponent controlling curvature transition in the positive direction (default `0.8`)  |
| `$b2`   | float| *(Optional)* Exponent controlling curvature transition in the negative direction (default `0.8`)  |
| `$Ed_tol`     |float | *(Optional)* Normalized energy tolerance for deciding if current excursion is partial unloading (default `0.02`)    |

### Example TCL Input

```tcl
# Define Steel02M uniaxial material
# Arguments: 
# matTag E0 FyPos FyNeg alpha R0 cR1 cR2 fysfyPos fysfyNeg a1 a2 <b1 b2> <Ed_tol>

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

uniaxialMaterial Steel02M $matTag $E $FyPos $FyNeg $alpha $R0 $cR1 $cR2 $fysfyPos $fysfyNeg $a1 $a2 $b1 $b2 $Ed_tol
# Example usage in a truss element
set eleTag 1
set iNode 1
set jNode 2
set A 100
element truss $eleTag $iNode $jNode $A $matTag
```

### Notes
- If `Ed_tol` is not specified in the input, a default value of **0.02** is used.
- Positive and negative curvature transition parameters are explicitly mentioned in positive and negative direction.



