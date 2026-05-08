
# NS Simulation Dataset — 3D Driven Turbulence (128³)

## Overview

This dataset contains 1001 snapshots from a 3D isothermal hydrodynamic driven turbulence simulation run with AthenaK. The simulation models subsonic turbulence driven on large scales in a periodic unit box. The data are stored as Legacy VTK binary files on the Hugging Face Hub at `pedrota2000/NS_simulation` (HF token: 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).

**A 13-snapshot sample has been pre-downloaded** to `/home/node/work/projects/ns_turbulence_v1/data_sample/`, covering indices 18903, 18953, 19003, 19053, 19103, 19203, 19303, 19403, 19503, 19603, 19703, 19803, 19903.

For full analysis, **all 1001 files must be downloaded** from Hugging Face using `huggingface_hub.hf_hub_download` with `repo_id="pedrota2000/NS_simulation"`, `repo_type="dataset"`, `token="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"`. Download to a local directory such as `/home/node/work/projects/ns_turbulence_v1/data/`. Files follow the naming pattern `Turb.hydro_w.NNNNN.vtk` where NNNNN ranges from 18903 to 19903. Each file is ~42 MB, total ~42 GB.

## File Format and Reading

Files are Legacy VTK binary (DATASET STRUCTURED_POINTS) and can be read with `pyvista`:

```python
import pyvista as pv
mesh = pv.read("/home/node/work/projects/ns_turbulence_v1/data/Turb.hydro_w.18903.vtk")
dens = mesh["dens"].reshape(128, 128, 128)
velx = mesh["velx"].reshape(128, 128, 128)
vely = mesh["vely"].reshape(128, 128, 128)
velz = mesh["velz"].reshape(128, 128, 128)
```

## Grid Geometry

- **Dimensions**: 128 × 128 × 128 cells (128³ = 2,097,152 cells per field)
- **Domain**: [-0.5, 0.5]³ (unit box, periodic on all faces)
- **Cell spacing**: dx = dy = dz = 1/128 ≈ 0.0078125
- **Cell centres**: from -0.5 + dx/2 to 0.5 - dx/2 in each dimension

## Data Fields (per file, cell-centred scalars)

| Field | Shape (reshaped) | dtype | Physical range (observed) | Description |
|-------|-----------------|-------|---------------------------|-------------|
| `dens` | (128,128,128) | float32 | [0.985, 1.007] | Mass density ρ (near-unity; isothermal subsonic) |
| `velx` | (128,128,128) | float32 | [-0.865, 0.691] | Velocity x-component v_x |
| `vely` | (128,128,128) | float32 | [-0.687, 0.717] | Velocity y-component v_y |
| `velz` | (128,128,128) | float32 | [-0.795, 0.853] | Velocity z-component v_z |
| `s_00` | (128,128,128) | float32 | ≈ 0.047 (uniform) | Passive scalar tracer (uniform; not useful for dynamics) |

## Temporal Structure

- **Total snapshots**: 1001 (indices 18903–19903 inclusive)
- **Simulation time**: t ≈ 189.03 to 199.03 (output cadence Δt = 0.01 per snapshot)
- **Total time span**: Δt_total ≈ 10.0 simulation time units
- **Snapshot index → simulation time**: t = index × 0.01

## Simulation Parameters

- Code: AthenaK
- EOS: Isothermal, sound speed c_s = 5.0
- Turbulence driving: solenoidal (divergence-free), dedt = 1×10⁻⁴, correlation time τ_corr = 5.0
- Driving wavenumber: n_low=1, n_high=3 (peak at n=2 → large-scale forcing)
- Time integrator: RK2, CFL = 0.3
- Riemann solver: HLLE, reconstruction: PLM
- Boundary conditions: periodic on all faces

## Vorticity

The 3D vorticity vector ω = ∇ × v has components:
- ω_x = ∂v_z/∂y − ∂v_y/∂z
- ω_y = ∂v_x/∂z − ∂v_z/∂x
- ω_z = ∂v_y/∂x − ∂v_x/∂y

Observed vorticity magnitude |ω|: mean ≈ 6.4, max ≈ 28.4 (at t=189.03).
Vorticity is computed via finite differences (np.gradient) on the 128³ grid with spacing dx=0.0078125.

## Research Objective

**Primary task:** Identify vortices in the 3D turbulence simulation and track their centres of vorticity through time. For each identified vortex, compute how its centre position evolves as a function of simulation time. Then statistically characterise whether the vortex centre trajectories are consistent with:
1. A **Gaussian random walk** (Brownian motion): step sizes drawn from a Gaussian distribution, MSD ∝ t (normal diffusion)
2. A **Lévy flight**: step sizes drawn from a heavy-tailed (power-law) distribution, MSD ∝ t^α with α > 1 (superdiffusion)

**Methodology hints:**
- **Vortex detection**: Use the Q-criterion (Q = 0.5*(|Ω|² − |S|²) > threshold, where Ω is the antisymmetric part and S is the symmetric part of the velocity gradient tensor ∇v) or the λ₂ criterion. Label connected regions above threshold as individual vortex structures. Alternatively use vorticity magnitude thresholding (|ω| > threshold × mean(|ω|)).
- **Centre of vorticity**: For each labelled vortex region, compute the vorticity-weighted centroid: x_c = Σ(|ω_i| * x_i) / Σ|ω_i| for x, y, z coordinates.
- **Vortex tracking**: Match vortices across consecutive timesteps using nearest-neighbour matching on centroid positions (Hungarian algorithm or simple greedy nearest-neighbour). Handle vortex birth/death (merge/split events).
- **Trajectory statistics**: For each tracked vortex trajectory of length N_t, compute step displacements Δr_i = r_{i+1} − r_i. Analyse the distribution of |Δr| — fit to Gaussian vs. Lévy (stable distribution or power-law tail). Compute MSD(τ) = <|r(t+τ) − r(t)|²> and fit the diffusion exponent α.
- **Periodic boundaries**: The domain is periodic. Use minimum-image convention for displacement calculations: Δx = ((x2−x1+0.5) mod 1.0) − 0.5.

## Storage and Memory Considerations

- Each VTK file: ~42 MB on disk, ~40 MB in memory (5 fields × 128³ × 4 bytes)
- All 1001 files: ~42 GB on disk — download to `/home/node/work/projects/ns_turbulence_v1/data/`
- Process files sequentially (one at a time) to avoid memory exhaustion
- For parallel downloading: use up to 8 threads
- The pre-downloaded sample at `/home/node/work/projects/ns_turbulence_v1/data_sample/` can be used for algorithm development before running on the full dataset
