The observed superdiffusive behavior ($\alpha \approx 1.8$) is not merely a result of persistent advection by the large-scale forcing, but is specifically modulated by the **topological evolution of vortex filaments** (stretching and reconnection events). I hypothesize that the "decorrelation time" $\tau_c \approx 0.22$ is physically linked to the local vortex stretching rate $\gamma = \omega \cdot S \cdot \omega / |\omega|^2$. 

To test this, we will:
1. Compute the local vortex stretching term $\gamma$ at each vortex centroid for every timestep.
2. Segment vortex trajectories into "quiescent" (low $\gamma$) and "active" (high $\gamma$) phases.
3. Calculate the conditional MSD and velocity autocorrelation function for these two populations. 

If the hypothesis holds, "quiescent" trajectories will exhibit higher persistence (higher $\alpha$ and longer $\tau_c$) as they are dominated by passive advection, while "active" trajectories will show a faster transition toward Brownian motion ($\alpha \to 1$) due to the topological disruption of the vortex filament, effectively acting as a "reset" mechanism for the vortex's velocity correlation. This will provide a mechanistic explanation for the observed intermediate diffusion regime by linking Lagrangian transport directly to the Eulerian vortex stretching dynamics.