# Steel02M: An Improved Giuffr`e-Menegotto-Pinto Model


A well-known limitation of the original `Steel02` material model in OpenSees is that after a small partial unloading, the stress–strain curve does not correctly retrace the original loading path during reloading. This behavior often leads to overshooting of stress.

The `Steel02M` uniaxial material model resolves this issue by introducing:
- **Improved energy-based path-following rules** to ensure the reloading curve returns to the correct envelope.
- **Optional tolerance control (`Ed_tol`)** for tuning reversal sensitivity.

### Input Syntax
```tcl
uniaxialMaterial Steel02M $matTag $E $FyPos $FyNeg $alpha $R0 $cR1 $cR2 $fysfyPos $fysfyNeg $a1 $a2 $b1 $b2 <Ed_tol>
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
| `$a1`   | float | Control rate of transition in positive and negative loading direction (previously a1) |
| `$a2`   | float| Control rate of transition in positive and negative loading direction (previously a1) |
| `$b1`  | float| Control rate of transition in positive and negative loading direction (previously a2) |
| `$b2`   | float| Control rate of transition in positive and negative loading direction (previously a2) |
| `$Ed_tol`     |float | *(Optional)* Normalized energy tolerance for deciding if current excursion is partial unloading (default `0.02`)    |

### Example TCL Input

```tcl
# Define Steel02M uniaxial material
# Arguments: 
# matTag E0 FyPos FyNeg alpha R0 cR1 cR2 fysfyPos fysfyNeg a1 a2 b1 b2 <Ed_tol>

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
set a1 0.0 # a1: Control rate of transition in positive and negative loading direction (previously a1)
set a2 0.0 # a2: Control rate of transition in positive and negative loading direction (previously a1) 
set b1 1.0 # b1: Control rate of transition in positive and negative loading direction (previously a2)
set b2 1.0 # b2: Control rate of transition in positive and negative loading direction (previously a2)
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



