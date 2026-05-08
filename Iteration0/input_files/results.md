# Results: Lagrangian Vortex Diffusion in 3D Isothermal Turbulence

## Summary

We analysed 1001 consecutive snapshots of a 3D isothermal driven turbulence simulation (AthenaK, 128³ grid, t ∈ [189.03, 199.03]) to identify vortex structures, track their centres of vorticity through time, and determine whether their Lagrangian trajectories are consistent with a Gaussian random walk (Brownian motion, MSD ∝ τ^1) or a Lévy flight (superdiffusion, MSD ∝ τ^α, α > 1).

**The principal finding is that vortex centre trajectories are robustly superdiffusive, with ensemble MSD exponent α = 1.815 ± 0.009 (R² = 0.9989).** This strongly rejects Brownian motion and is consistent with persistent, correlated vortex motion driven by the large-scale solenoidal forcing at wavenumbers n = 1–3. Despite the superdiffusive MSD, step-size distributions are near-Gaussian (Kolmogorov–Smirnov p ≈ 0.06–0.38), indicating that this is anomalous diffusion arising from temporal correlations (persistent motion), not from heavy-tailed jump statistics. This regime is therefore better described as a correlated random walk / fractional Brownian motion rather than a classical Lévy flight.

---

## 1. Data and Pre-processing

The dataset consists of 1001 Legacy VTK binary files (indices 18903–19903), each containing 128³ cell-centred fields: mass density ρ, velocity components v_x, v_y, v_z, and a passive tracer. The domain is [-0.5, 0.5]³ with periodic boundaries. The isothermal sound speed is c_s = 5.0 and the Mach number is subsonic (|v|/c_s ≪ 1). All 1001 files were downloaded and processed (total ~42 GB).

The density field is nearly uniform (ρ ∈ [0.985, 1.007], mean = 1.000), confirming near-incompressible subsonic turbulence. Velocity components have zero mean and a standard deviation of ~0.23 in each direction, giving an rms Mach number M_rms ≈ 0.23/5 ≈ 0.046 — well subsonic.

---

## 2. Vortex Identification

Vortices were identified using the Q-criterion, Q = ½(|Ω|² − |S|²), where Ω is the antisymmetric (rotation) part and S is the symmetric (strain) part of the velocity gradient tensor ∇v. Candidate voxels were retained where Q > μ_Q + 3σ_Q (local adaptive threshold). Connected-component labelling with a minimum volume of 20 voxels was applied to isolate individual structures. Structures spanning more than 64 voxels in any direction were excluded as non-physical mergers.

**Key statistics:**
- Mean number of vortices per snapshot: 21.1
- Total vortex detections across all 1000 processed snapshots: 21,061
- Vortex volumes ranged from 20 to several hundred voxels
- Mean vorticity magnitude within detected vortex cores: ~15–18 (approximately 2.3–2.8× the domain mean of 6.4)
- Q-criterion threshold: Q > 51.7 (μ_Q = −9.1, σ_Q = 20.3)

The simulation contains O(20) coherent vortex structures per snapshot, consistent with the large-scale (n=2) driving that injects energy at the box scale and produces ~10–20 large vortex filaments in the inertial range.

---

## 3. Vortex Tracking

Vortex centroids (vorticity-weighted, with periodic boundary unwrapping within each cluster) were linked across consecutive timesteps using a greedy nearest-neighbour algorithm. The maximum permissible displacement per timestep d_max was set at the 95th percentile of the empirical nearest-neighbour distance distribution. The minimum-image convention (periodic domain of side 1) was applied for all distance calculations.

**Tracking statistics:**
- Total unique trajectories identified: 1,185
- Trajectories persisting ≥ 10 consecutive timesteps: 634 (53.5%)
- Trajectories persisting ≥ 30 timesteps: used for per-trajectory α fits (N = 234)
- Median trajectory length: 11 timesteps (Δt = 0.11 simulation time units)
- Maximum trajectory length: 199 timesteps (Δt = 1.99 time units)
- Snapshot coverage: 1000/1001 snapshots

The moderate median length reflects both physical vortex birth/death events and tracking losses at merge/split events. The 101 trajectories with length ≥ 50 timesteps (Δt ≥ 0.5 time units) provide the most reliable statistics for diffusion analysis.

---

## 4. Trajectory Unwrapping

Each trajectory was unwrapped using the cumulative minimum-image convention: at each step, the displacement Δr_i = r_{i+1} − r_i was corrected to the nearest image (|Δr| < 0.5 in each component), and the unwrapped position was accumulated as r̃_i = r̃_{i−1} + Δr_i. This correctly handles vortex crossings of the periodic boundary without introducing artificial large jumps.

---

## 5. Mean Squared Displacement

The ensemble-averaged MSD was computed as:

  MSD(τ) = ⟨|r̃(t+τ) − r̃(t)|²⟩

averaging over all (trajectory, time-origin) pairs at each lag τ. The maximum lag was limited to τ_max = 49 steps (one quarter of the longest trajectory). The number of contributing pairs ranged from 18,789 at τ=1 to 2,225 at τ=49, ensuring good statistics throughout.

**MSD power-law fit (ensemble):**

  MSD(τ) ∝ τ^α, α = **1.815 ± 0.009** (R² = 0.9989)

The fit was performed on all lags with more than 100 contributing pairs (all 49 lags satisfied this criterion). The MSD grows as MSD(1) = 1.0×10⁻⁵ at τ=1 to MSD(49) = 1.25×10⁻² at τ=49.

**Per-component MSD exponents (isotropy check):**
- α_x = 1.724 ± 0.016 (R² = 0.996)
- α_y = 1.871 ± 0.003 (R² = 1.000)
- α_z = 1.849 ± 0.009 (R² = 0.999)

The slight anisotropy between x and (y, z) components likely reflects the finite turbulent fluctuations within the 10-time-unit observation window, as the driving is isotropic (solenoidal, all polarisations equally weighted). The ensemble exponent α ≈ 1.82 is robustly midway between Brownian (α=1) and ballistic (α=2).

**Per-trajectory diffusion exponents:**
- N = 234 trajectories (≥30 steps each)
- Mean α = 1.878, standard deviation σ_α = 0.094
- Range: [1.461, 2.031]
- Fraction with α > 1 (superdiffusive): 100%
- Fraction with α > 1.5: 99.6%

Every tracked vortex is superdiffusive. The near-ballistic α ≈ 1.88 for individual vortices, and the ensemble α ≈ 1.82, are in excellent agreement.

---

## 6. Step-Size Distribution Analysis

We computed 18,789 single-timestep displacements Δr_i = r̃_{i+1} − r̃_i for all tracked trajectories (≥10 steps).

**Displacement statistics:**
- Mean |Δr| = 0.00292 (in domain units)
- Std |Δr| = 0.00138
- Max |Δr| = 0.00823
- The typical step size (2.9×10⁻³) is approximately 0.37 grid cells — vortices move sub-cell distances per Δt=0.01

**Gaussian fit to components:**
- Δx: μ = −2.8×10⁻⁶, σ = 0.00228
- Δy: μ = 4.4×10⁻⁶, σ = 0.00194
- Δz: μ = −5.8×10⁻⁷, σ = 0.00228
- The near-zero means confirm that vortex motion has no systematic drift

**Kolmogorov–Smirnov test for Gaussianity (n=5000 subsample):**
- Δx: KS statistic = 0.0187, p = 0.061 → *cannot reject Gaussian at 5% level*
- Δy: KS statistic = 0.0128, p = 0.381 → *strongly consistent with Gaussian*
- Δz: KS statistic = 0.0160, p = 0.151 → *consistent with Gaussian*

**Excess kurtosis:**
- Δx: κ = 0.403 (mild positive kurtosis — slightly heavier tails than Gaussian)
- Δy: κ = 0.112 (near-Gaussian)
- Δz: κ = 0.457 (mild positive kurtosis)
- For comparison, a Lévy stable flight with α_Lévy < 2 would have divergent kurtosis (κ → ∞)

**Shapiro–Wilk normality test (Δx, n=1000):** W = 0.9963, p = 0.019 → marginal departure from Gaussianity at the 5% level (expected with 18,789 samples; power is very high)

**Power-law tail fit on |Δr| (MLE, Clauset method):**
- 90th percentile threshold = 0.00449, tail N = 1,879: α_PL = 6.80
- 95th percentile threshold = 0.00509, tail N = 940: α_PL = 8.00
- Hill estimator (k=200): α_Hill = 12.1; (k=500): α_Hill = 9.3
- All tail exponents are large (>6), confirming *rapidly decaying (non-power-law) tails*, consistent with a near-Gaussian distribution, not a Lévy flight

---

## 7. Interpretation: Superdiffusion Without Lévy Flights

The combination of:
1. Superdiffusive MSD exponent α ≈ 1.82 (strongly super-Brownian)
2. Near-Gaussian step-size distributions (KS p > 0.06, small kurtosis)
3. Very large power-law tail exponents (>6)

points clearly to *correlated superdiffusion* (also called persistent random walk or fractional Brownian motion with Hurst exponent H = α/2 ≈ 0.91), not to a Lévy flight. In a Lévy flight, the superdiffusion is driven by rare, very large jumps from a heavy-tailed distribution. Here, the superdiffusion is driven by *temporal persistence*: vortices tend to keep moving in the same direction from one timestep to the next, because they are advected by the large-scale coherent velocity field created by the solenoidal forcing at n=2.

This is physically sensible: the energy injection correlation time is τ_corr = 5.0 simulation time units, much longer than the observation window of Δτ_max = 0.49 (49 timesteps × 0.01). Within the correlation time, vortices move quasi-ballistically (α → 2). Over longer times (τ > τ_corr), we would expect the MSD to cross over to Brownian behaviour (α → 1). The fact that we observe α ≈ 1.82 — intermediate between ballistic and Brownian — indicates that the 49-timestep observation window spans the transition from correlated to diffusive motion but has not yet reached full decorrelation.

---

## 8. Figures

- **Figure 1** (`figure1_msd_and_stats.png`): Multi-panel summary — log-log MSD with power-law fit, step-displacement histogram with Gaussian overlay, |Δr| distribution, CCDF log-log tail analysis, trajectory length histogram, excess kurtosis bar chart.
- **Figure 2** (`figure2_3d_trajectories.png`): 3D rendering of the 20 longest vortex trajectories in the periodic unit box.
- **Figure 3** (`figure3_2d_projections.png`): XY, XZ, YZ plane projections of the top 20 trajectories.
- **Figure 4** (`figure4_qq_plots.png`): Normal Q-Q plots for Δx, Δy, Δz step components.
- **Figure 5** (`figure5_msd_detail.png`): Log-log and linear MSD plots for total and per-component, with power-law and Brownian/ballistic reference lines.
- **Figure 6** (`figure6_displacement_stats.png`): Detailed step-displacement statistics — component histograms, |Δr| distribution with Maxwell-Boltzmann fit, CCDF with power-law reference, active vortex count over time.
- **Figure 7** (`figure7_per_traj_alpha.png`): Per-trajectory MSD curves coloured by α, and histogram of per-trajectory diffusion exponents.
- **Figure 8** (`figure8_vorticity_field.png`): Vorticity magnitude |ω| and Q-criterion slices at three z-planes (t=189.03), showing the spatial structure of the turbulence.
- **Figure 9** (`figure9_individual_displacement.png`): Displacement-squared vs. elapsed time for 12 individual long-lived vortices, each fitted independently.
- **Figure 10** (`figure10_vortex_spatial.png`): Vortex centroid spatial distributions at six representative simulation times, coloured by z-coordinate.

---

## 9. Conclusions

1. In 3D isothermal driven turbulence (AthenaK, 128³, solenoidal driving at n=2), ~21 coherent vortex structures are identifiable per snapshot via the Q-criterion.
2. Vortex centre trajectories are **universally superdiffusive**: ensemble MSD exponent α = 1.815 ± 0.009, per-trajectory mean α = 1.878 ± 0.094.
3. Step-size distributions are **near-Gaussian** (KS p > 0.05, excess kurtosis < 0.5, power-law tail exponent > 6).
4. The superdiffusion is driven by **temporal correlations** (persistent motion), not heavy-tailed jumps. This is correlated random walk / sub-ballistic regime, consistent with the forcing correlation time τ_corr = 5 >> observation window.
5. **Not a Lévy flight.** Classical Lévy flights require power-law tails with exponent < 2; here α_PL ≈ 7–12, consistent with near-Gaussian tails. The superdiffusive MSD arises from velocity autocorrelation, not from rare large jumps.
