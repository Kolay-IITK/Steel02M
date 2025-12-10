# Steel02M: An Improved Giuffr`e-Menegotto-Pinto Model

A well-known limitation of the Steel02 material model in OpenSees is that it causes overshooting on reloading upon a small partial unloading (see the figure below). Steel02M eliminates this error using a simple energy-based approach that is controlled by an optional normalized energy dissipation tolerance ΔŪₜₒₗ  for which a default value of 0.02 is recommended. Note that the Steel02M becomes identical to Steel02 when ΔŪₜₒₗ=−1 is used keeping all other parameters the same between the two models.

The features of the Steel02M are as follows:
- Eliminates the overshooting error caused by Steel02
- Allows users to specify different initial yield strength in positive and negative loading directions
- Removes redundant parameters in isotropic hardening of Steel02 (see the equations below for isotropic hardening in Steel02 and Steel02M) 
- Allows users to control the rate of isotropic hardening in positive and negative loading direction through parameters (b⁺ and b⁻)
- Allows user to limit the hardened yield strength through parameters (fᵧₛ⁺ and fᵧₛ⁻)

**Steel02:**

$$
\sigma_y^{+} = \sigma_{y0}\left[1 + a_3 \left(\frac{\zeta}{a_4}\right)^{0.8}\right]
= \sigma_{y0}\left[1 + a^{+}\zeta^{b^{+}}\right]
$$

$$
\sigma_y^{-} = \sigma_{y0}\left[1 + a_1 \left(\frac{\zeta}{a_2}\right)^{0.8}\right]
= \sigma_{y0}\left[1 + a^{-}\zeta^{b^{-}}\right]
$$

$$
\zeta = \frac{\varepsilon_{\max}-\varepsilon_{\min}}{2\varepsilon_{y0}}
$$


**Steel02M:**

$$
\sigma_y^{\pm} = \sigma_{y0}^{\pm}\left[1 + a^{\pm}\zeta^{b^{\pm}}\right]
\quad \text{subject to } |\sigma_y^{\pm}| \le f_{ys}^{\pm}|\sigma_{y0}^{\pm}|
$$

$$
\zeta = \frac{\varepsilon_{\max} - \varepsilon_{\min}}{\varepsilon_{y0}^{+} - \varepsilon_{y0}^{-}}
$$

$$
\varepsilon_{y0}^{\pm} = \frac{\sigma_{y0}^{\pm}}{E_0}
$$

<p align="center">
  <img src="Stress-strain_plot.png" width="500"><br>
  <em>Figure : Stress-strain response using Steel02 and Steel02M</em>
</p>


    
### Input Syntax
```tcl
uniaxialMaterial Steel02M $matTag  $E₀ $σᵧ₀⁺ $σᵧ₀⁻ $α $R₀ $cᵣ₁ $cᵣ₂ <$a⁺ $a⁻> <$fᵧₛ⁺ $fᵧₛ⁻> <$b1 $b2> <$ΔŪₜₒₗ >
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
This TCL input file generates the σ–ε response shown in the figure for the Steel02M material model.
```tcl
# -----------------------------
# Horizontal axial element(truss) with unit length and area with Steel02M material model
#Unit considered:N,mm
# -----------------------------
wipe
model BasicBuilder -ndm 2 -ndf 2
# -----------------------------
# Nodes
node 1 0.0 0.0
node 2 1.0 0.0
# -----------------------------
#Boundary condition
fix 1 1 1
fix 2 0 1
# -----------------------------
# Define Steel02M uniaxial material Arguments:
# matTag  E₀ σᵧ₀⁺ σᵧ₀⁻ α R₀ $cᵣ₁ $cᵣ₂ <$fᵧₛ⁺ $fᵧₛ⁻ $a⁺ $a⁻ $b1 $b2 Ed_tol>
# matTag E FyPos FyNeg alpha R0 cR1 cR2 <fysfyPos fysfyNeg> <a_pos a_neg> <b1 b2> <Ed_tol>
set matTag 1
set Fy 300
set FyPos $Fy; # FyPos: Positive yield stress
set FyNeg [expr -1*$Fy]; # FyNeg: Negative yield stress
set E 200000; # E: Elastic Young's modulus
set b 0.0196; # alpha: Post yield stiffness ratio
set R0 20.0; # R0: Initial transition curvature 
set cR1 0.916; # cR1:  Parameter controlling transition curvature 
set cR2 0.15; # cR2:  Parameter controlling transition curvature 
set a_pos 0.0; # Control rate of transition in positive loading direction
set a_neg 0.074; # Control rate of transition in negative loading direction
#uniaxialMaterial Steel02M $matTag $E $FyPos $FyNeg $alpha $R0 $cR1 $cR2 <$a_pos $a_neg> <$fysfyPos $fysfyNeg>  <$b1 $b2> <Ed_tol>
uniaxialMaterial Steel02M $matTag $E $FyPos $FyNeg $b $R0 $cR1 $cR2 $a_pos $a_neg
# -----------------------------
# Truss
set eleTag 1
set A 1.0
element truss $eleTag 1 2 $A $matTag
# -----------------------------
# Load pattern (reference load)
# -----------------------------
pattern Plain 1 Linear {
    load 2 1.0 0.0; # Apply unit horizontal load at Node 2
}
# -----------------------------
# Recorders
# -----------------------------
recorder Node -file node2_disp_exp.out -time -node 2 -dof 1 disp
recorder Element -file ele1_force_exp.out -time -ele 1 force
# -----------------------------
# Analysis components
# -----------------------------
system BandGeneral
numberer RCM
constraints Plain
test NormDispIncr 1e-8 10
algorithm Newton
# strain peaks in units of 1e-3
set strainPeaks {0.001 2 1.023 27.629 13.373 27.259 13.506 15.282 14.389 18.020 17.718 27.325 24.932 25.961 23.187 23.486 6.109}
set de 1e-4 ;# displacement increment per step
foreach peak $strainPeaks {    
    set targetDisp [expr $peak * 1e-3]
    set currentDisp [nodeDisp 2 1]
    set dU [expr $targetDisp - $currentDisp]
    set nSteps [expr round(($dU)/$de)]
    puts "Loading to target strain = $peak x 1e-3 
    dU = $dU  steps = $nSteps"

    if {$nSteps != 0} {
        integrator DisplacementControl 2 1 $de
        if {$nSteps < 0} {
            integrator DisplacementControl 2 1 [expr -$de]
            set nSteps [expr abs($nSteps)]
        }
    }
    analysis Static
}
puts "Finished all peak loadings."







```
Code developed by: Dr. Chinmoy Kolay, IIT Kanpur and implemented by Ms. Sukanya Karmakar, IIT Kanpur  
Images developed by: Ms. Sukanya Karmakar, IIT Kanpur



