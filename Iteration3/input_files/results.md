# Results: Lagrangian Vortex Diffusion — Iteration 3
# Velocity Correlation and MSD Anisotropy

## Summary

Two final physical analyses complete the picture: (1) correlation between vortex centroid velocity and local fluid velocity, and (2) MSD anisotropy parallel vs. perpendicular to the local vorticity vector.

---

## 1. Vortex Velocity vs. Local Fluid Velocity Correlation

We computed the Pearson correlation between the vortex centroid velocity (finite difference of centroid positions) and the background fluid velocity interpolated at the centroid position, for 3,617 matched (snapshot, trajectory) pairs from 200 sampled snapshots:

| Component | Pearson r | Significance |
|-----------|-----------|-------------|
| v_x | 0.139 | p = 4×10⁻¹⁷ |
| v_y | 0.790 | p ≈ 0 |
| v_z | 0.059 | p = 4×10⁻⁴ |
| |v| speed | 0.336 | p ≈ 0 |

**Direction alignment:**
- Mean cos(θ) between v_vortex and v_fluid = 0.317
- Fraction with cos(θ) > 0.5 (< 60° misalignment): 48.2%
- Fraction with cos(θ) > 0.9 (< 26° misalignment): 14.8%

The y-component shows strong correlation (r = 0.79), while x and z are weakly correlated. This anisotropic coupling suggests the large-scale forcing creates a preferred velocity direction along y (consistent with the observed α_y > α_x in the component MSD analysis). However, the overall alignment (mean cos(θ) = 0.32) indicates that vortex motion is only partially slaved to the local fluid velocity — nearly half the motion is independent of the local flow.

This rules out both extremes: vortices are neither purely passive tracers (which would require r ≈ 1) nor completely decoupled from the fluid (r ≈ 0). The moderate correlation confirms that the superdiffusion arises from a combination of advection by the correlated large-scale flow and intrinsic vortex dynamics.

---

## 2. MSD Anisotropy

**Component-level MSD exponents:**
- α_x = 1.724 ± 0.016 (R² = 0.996)
- α_y = 1.871 ± 0.003 (R² = 1.000)
- α_z = 1.849 ± 0.009 (R² = 0.999)
- α_total = 1.815 ± 0.009 (R² = 0.999)

All three components are superdiffusive (α > 1), confirming that the superdiffusion is not confined to a single spatial direction. The slight anisotropy (α_x ≈ 1.72 vs α_y,z ≈ 1.86) likely reflects the anisotropic velocity correlation observed above (r_y = 0.79 vs r_x = 0.14).

**Parallel vs. perpendicular to vorticity vector (1,834 matched pairs):**
- Mean |Δr_∥| (parallel to ω) = 0.001800
- Mean |Δr_⊥| (perpendicular to ω) = 0.002131
- Ratio ∥/⊥ = 0.845

Vortex filaments preferentially drift perpendicular to their own vorticity axis (by ~18%). This is physically sensible: vortex filaments are extended along ω, so their centroid cannot move easily along their own axis without shearing the filament. Instead, they drift in the plane perpendicular to ω, consistent with the known dynamics of vortex filament transport in 3D turbulence.

---

## 3. Complete Physical Picture

Assembling all three iterations:

1. **Vortex trajectory MSD**: α ≈ 1.86–1.92 (strongly superdiffusive, robust across all tests)
2. **Step-size distribution**: Near-Gaussian (KS p > 0.05, kurtosis < 0.5) → NOT Lévy flight
3. **VACF**: Decays slowly, τ_1/e ≈ 0.19 sim time → persistent correlated motion
4. **Threshold robustness**: α stable across Q = 2.5σ–4σ → result independent of vortex definition
5. **Advection subtraction**: α_residual ≈ 1.93 > α_raw → superdiffusion is intrinsic, not passive
6. **Trajectory length convergence**: α stable from min_len=20 to min_len=100 → not a finite-time artefact
7. **Temporal stationarity**: Δα ≈ 0 between trajectory halves → non-transient regime
8. **Velocity correlation**: r(v_vortex, v_fluid) ≈ 0.14–0.79 by component → partial coupling only
9. **Spatial anisotropy**: Vortex drift is 18% stronger perpendicular to ω than parallel → filament physics

**Mechanism (final):** The superdiffusion α ≈ 1.9 arises from persistent correlated motion driven by the long-correlation-time large-scale solenoidal forcing (τ_corr = 5.0 >> τ_c ≈ 0.22). Vortices are partially coupled to the local fluid velocity (r ≈ 0.3–0.8) and exhibit intrinsic dynamics (advection-subtracted α is even higher). The motion is preferentially perpendicular to the vorticity axis. This is definitively *not* a Lévy flight (which requires heavy-tailed steps); it is correlated persistent random walk / anomalous sub-ballistic diffusion arising from long-lived velocity correlations.
